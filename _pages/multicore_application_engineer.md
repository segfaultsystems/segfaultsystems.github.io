---
layout: post
permalink: multicore_application_engineer.html
published: true
---

# Multicore Application Engineer

The [Multicore OCaml](https://github.com/ocaml-multicore) project aims to bring
native support for scalable concurrency and shared memory parallelism to the
OCaml programming language. At its core, Multicore OCaml extends OCaml with
[effect handlers](https://arxiv.org/abs/2104.00250) for expressing scalable
concurrency, and a [high-performance concurrent garbage
collector](https://arxiv.org/abs/2004.11663) aimed at responsive networked
applications. Multicore OCaml is also the first industrial-strength language to
be equipped with [an efficient yet modular memory
model](https://kcsrk.info/papers/pldi18-memory.pdf), allowing high-level local
program reasoning while retaining performance. Multicore OCaml is actively being
developed and core features are being [upstreamed to
OCaml](https://github.com/ocaml/ocaml/pulls?q=label%3Amulticore-prerequisite+).

As OCaml 5.0 with shared-memory parallelism support is on the horizon, we are
ready to build full-fledged OCaml applications that take advantage of the
parallelism opportunities. Specifically, the focus is on the [Tezos
blockchain](https://tezos.com/) to improve the transaction throughput, utilising
the parallelism support. We believe that adding parallelism support to a complex
project such as the Tezos shell would set a precedent for how other projects
should utilise parallelism support and help identify pain points that the
Multicore OCaml team and the wider community can address.

We are looking for an experienced engineer (3y+) to build parallel applications
using the Multicore OCaml compiler. Ideally, you will have:

## Qualifications & Experience
*(You don’t have to fill 100% of the qualifications to apply.)*

* Excellent knowledge and hands-on experience developing parallel programs in
  any language.
* Experience with the OCaml language, or other functional programming languages.
* Experience in analysing benchmarks and application performance.
* Track record of building production quality software.
* Demonstrable open source contributions are a plus, but not required.
* Good communication skills in English; English is the corporate language.
* Experience of working in multidisciplinary teams.

## Responsibilities

* Ecosystem Engineering
  + Build and maintain Tezos node using the Multicore OCaml compiler.
  + Add Tezos related packages to the [OCaml CI](https://multicore.ci.ocamllabs.io:8100/)
  + Investigate CI failures and report issues to appropriate teams
* Parallelising
  + Work with the Tezos developers to identify the opportunities for parallelism
    in the Tezos shell.
  + Utilise Multicore OCaml parallelism support (such as [Lwt offloading](https://sudha247.github.io/2020/10/01/lwt-multicore/) mechanism)
    to offload compute-intensive tasks such as crypto and serialisation to spare
    cores.
  + Work with the Irmin/Tezos storage team to add parallelism to storage tasks. 
* Benchmarking
  + Work with the Tezos developers and Irmin team to identify relevant
    benchmarks. 
  + Implement those benchmarks for Tezos, and work with the benchmarking team to
    include them in [sandmark](https://github.com/ocaml-bench/sandmark) and
    continuous benchmarking infrastructures.
* Research
  + Build a direct-style Tezos shell using the
    [eio](https://github.com/ocaml-multicore/eio), next-generation effect-based
    direct-style io library.
  + Collaborate with researchers to implement deterministic parallelism support
    in the Tezos protocol core. 

## What we offer

* Competitive pay and benefits package.
* Flexible working hours and the possibility to work remotely within India.
* Supportive team environment with experienced Technical and Team Leads.
* The opportunity to grow with a fast-growing company.

## Apply

Please send your CV and cover letter to **jobs@segfault.systems**. 

If shortlisted, you will have three online interviews starting with a general
interview, followed by a technical interview, and finally an interview with the
team.

We welcome applications from people of all backgrounds. We strive to create a
representative, inclusive and friendly team, because we know that different
experiences, perspectives and backgrounds make for a better workplace.
