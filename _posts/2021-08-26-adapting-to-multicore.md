---
layout: post
title: "Adapting the OCaml Ecosystem for Multicore OCaml"
date: 2021-08-25 15:50:00
categories: [multicore, opam, ocaml]
excerpt_separator: <!--more-->
authors: "Sudha Parimala"
---

Multicore OCaml is an extension of OCaml with support for Shared-Memory
Parallelism and Concurrency. OCaml 5.0 aims to ship with support for
Shared-Memory Parallelism. Upstreaming Multicore bits to OCaml trunk is already
underway, and some major patches, such as the instrumented runtime, GC Colours,
Safe points, etc., have been merged. This post aims to take readers through
changes necessary for a smooth trasition to Multicore.

<!--more-->

As OCaml 5.0 gets closer to reality, there's increasing interest among library
authors to port their libraries to Multicore. OCaml 5.0 will be accompanied by a
robust set of libraries, such as *domainslib* to aid writing efficient parallel
programs. Algebraic Effects is expected to land in later versions of OCaml.

[Parallelizing OCaml code with Multicore
OCaml](https://www.youtube.com/watch?v=Z7YZR1q8wzI) gives an excellent overview
of parallelizing existing OCaml codebases. 

## Multiprocess vs Multicore

Admittedly due to lack of Multicore support, it's common practice by OCaml
programmers to use `Unix.fork` primitive to speed up program executions.
Firstly, Multicore programs that spawn new domains don't support `Unix.fork`
primitive, though the primitive remains unaffected in sequential programs.
`Unix.create_process` is a worthy replacement of `Unix.fork` in Multicore
programs.

In a **Multiprocess** program, each process has its _own copy of global data_,
wheareas in a **Multicore** program, data are shared between the domains
present. We need to be careful while porting Multiprocess programs to Multicore,
since it might operate under the assumption that each process will modify only
its own copy of data.

<p align="center"> <img src="{{ base.url }}/assets/images/fKSY1Qn.png" /> </p>

## Building Your Package with Multicore OCaml

Getting the Multicore compiler is easier than ever! It can be obtained from
`opam-repository` with:

```bash
$ opam update
$ opam switch create 4.12.0+domains
```

**Compiler Variants:**

* 4.12.0+domains: Supports Domain-only Parallelism. This version is
  representative of what's expected to land in OCaml 5.0.

* 4.12.0+domains+effects: Supports Parallelism and Concurrency. Use this version
  only if you want Algebraic Effects.

## Infrastructure for Continuous Monitoring

### Opam Health Check

Once deployed, Opam Health Check monitors build and testsuite failures in Opam
packages. One instance of Opam Health Check monitors the `4.12.0+domains`
branch. So far, this has been useful to identify bugs in the compiler and
incompatibility with some packages. Opam Health Check can be accessed at:
[http://check.ocamllabs.io:8082/](http://check.ocamllabs.io:8082/)

### Multicore CI

A select set of packages including Lwt, Irmin, Coq, Base, Core, etc., are
monitored with more scrutinity. As a part of the OCurrent pipeline, Multicore CI
builds all the packages and runs their testsuite for every commit to
`4.12.0+domains`. This process aims to catch bugs early and maintain
compatibility with the wider ecosystem.

## Global State and Thread Safety

OCaml allows mutable values, which are widely used in OCaml codebases for
efficiency and at times correctness. Concurrent modification of mutable
variables leads to non-deterministic results. Hence, care is needed to ensure
all concurrent modifications of mutable variables do what is desired.

*Fuzzing* tools will be available soon to detect data-races. Refer to the
[Parafuzz talk](
https://icfp21.sigplan.org/details/ocaml-2021-papers/9/Parafuzz-Coverage-guided-Property-Fuzzing-for-Multicore-OCaml-programs)
for more details.

### What Multicore OCaml Offers

* **Atomic Values**: The Atomic module in the standard library provides
  atomicity guarantees for mutable atomic variables. Internally, this is
  achieved with the Compare and Swap (CAS) operation.

* **Synchronization Primitives**: Synchronization primitives, such as Mutex and
  Condition Variable, are part of the standard library. They play nicely with
  both systhreads and domains.

* **Domain-Local Storage:** DLS provides an easy way to store values local to a
  domain. They can be accessed anytime after creation within the domain.

* **Concurrent Data Structures:** Not all data structures in the standard
  library are thread-safe. Alternatives are available in lockfree.

### Standad Library Thread Safety

Standard library isn't thread-safe by default. Mutable interfaces such as Stack,
Queue, etc., still don't incur overheads on sequential code. They can be wrapped
with a Mutex or replaced by their Concurrent counterpart for use in Multicore
programs. The Multicore OCaml memory model guarantees memory safety even for
programs with data races.

Observationally pure interfaces are preserved. `Random`, `Hashtbl`, and `Format`
use mutable data internally, though they are not exposed to the user. Such
modules are still safe to be used in Multicore programs, as their global state
is replaced with domain-local state.

## Systhreads in Multicore

Multicore implementation of systhreads is backwards compatible. Hopefully no
surprises there!

Sequential programs with systhreads are expected work like they do in stock
OCaml. As for Parallel programs with systhreads:

* Systhreads are attched to the domain they are created from.
* There's no load balancing the number of systhreads attached to a domain. If
  Domain 0 has 100 systhreads and Domain 1 has 10, they will continue to do so
  until the execution is complete.
* Threads registered from `C` bindings are always attached to Domain 0. This is
  to ensure threads arn't lost when other domains terminate.

## Runtime Changes

During the development of the Multicore garbage collector (GC), some breaking
changes were introduced in the runtime. This would affect code explicitly
accessing GC internals. Changes in the runtime are discussed below:

### Lazy Values

*Lazy* values can be forced from only one domain at a time. Concurrent forcing
of Lazy values will raise `RacyLazy` exception.

```ocaml
let f n =
	let r = ref 0 in
    for _ = 1 to n do
      incr r
    done;
    !r

let l = lazy (f 1_000_000_000)
let domains = Array.init 4
	(fun _ -> Domain.spawn (fun () -> Lazy.force l))

let v =  Array.map Domain.join domains
```

This results in

```bash
$ ./lazy_example.exe
Fatal error: exception CamlinternalLazy.RacyLazy
```

To circumvent this, we can use`Lazy.try_force` which returns option value`None`
when some other domain is forcing the given Lazy value.

```ocaml
let f n =
	let r = ref 0 in
    for _ = 1 to n do
      incr r
    done;
    !r

let l = lazy (f 1_000_000_000)

let rec try_force l =
  match Lazy.try_force l with
  | Some v -> v
  | None -> try_force l

let domains = Array.init 4
	(fun _ -> Domain.spawn (fun () -> try_force l))

let v =  Array.map Domain.join domains
let () = print_int v.(0)
```

### Naked Pointers

Pointers outside the OCaml heap, also known as *naked* pointers, which were
deprecated in OCaml 4.11, so they won't be supported in OCaml 5.0. Pointers
outside the OCaml heap are typically blocks allocated directly through `malloc`.
The naked pointers checker tool is developed to detect naked pointers. To obtain
the checker, run:

```bash
$ opam update
$ opam switch create 4.12.1+trunk
$ opam install ocaml-option-nnpchecker
```

Once the `nnpchecker` is installed, the checker runs during program execution
and reports the address out of heap pointers, if present. `frama-c` contains
naked pointers; the `nnpchecker` report is below. To find the pointer's source,
`watchpoint` can be set at the given address on Mozilla rr, which will show the
location of that naked pointer.

```bash
$ rr frama-c
rr: Saving execution to trace directory `/home/kc/.local/share/rr/frama-c-5'.
Out-of-heap pointer at 0x55fc1e2754d8 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e275600 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e2754d8 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e275600 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e2754d8 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e275600 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e2754d8 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e275600 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e2754d8 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e275600 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e2754d8 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
Out-of-heap pointer at 0x55fc1e275600 of value 0x55fc1e3a0cc0 has non-black head (tag=144)
$ rr replay
(rr) watch *(value*)0x55fc1e2754d8
Hardware watchpoint 1: *(value*)0x55fc1e2754d8
(rr) c
Continuing.

Hardware watchpoint 1: *(value*)0x55fc1e2754d8

Old value = 1
New value = 94541327240384
0x000055fc1dab48f8 in camlUnmarshal__entry () at src/libraries/datatype/unmarshal.ml:72
72      src/libraries/datatype/unmarshal.ml: No such file or directory.
 ```

It's recommended to wrap the naked pointers with an Abstract_tag or Custom_tag.
More ways to tackle naked pointers can be [found here in the
manual](https://ocaml.org/manual/intfc.html#ss:c-outside-head).

### Domain State

Runtime global variables are stored in a dedicated structure known as Domain
State. This is already present in the latest releases of stock OCaml. Some of
the variables in Domain State are exposed in the compatibiltiy module. With
Multicore, it's likely that the compatibility module will be deprecated in OCaml
5.0. Also, the variables in Domain State are specific to a domain, hence they
can no longer be treated as global variables.

We recommend evaluating the global variables, and if the code still requires
them, they could be referenced from `Caml_state`, which refers to the calling
domain's Domain State.

### GC Colours

Historically OCaml had the GC colours `Black, White, Blue, and Grey`. Since
OCaml 4.12, the `Grey` colour has been replaced by Multicore's mark stack.
Multicore OCaml's GC colours are `MARKED, UNMARKED, and GARBAGE`.

It's clear that directly referring to historical GC colours will not work with
Multicore. The GC colours' API is not meant to be stable anyway, so our best bet
is to replace it with a more stable one. In inevitable cases, the Multicore
equivalent can be used.

## Parallelizing Lwt

Lwt is a Monadic concurrency libaray widely used in the OCaml ecosystem. Lwt has
proven to be efficient for I/O parallelism. With Multicore, we attempt to
introduce CPU parallelism in Lwt. This is elaborated in [this blog
post](https://sudha247.github.io/2020/10/01/lwt-multicore/) and
[PR](https://github.com/ocsigen/lwt/pull/860).

---

Hopefully, this helped you in a smooth transition to Multicore. For any specific
incompatibilites, don't hesitate to contact us via [the issue
tracker](https://github.com/ocaml-multicore/ocaml-multicore/issues).
