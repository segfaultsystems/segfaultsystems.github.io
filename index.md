---
layout: about
permalink: /
published: true
---

We are delighted to announce that Segfault Systems is joining
[Tarides](https://tarides.com/). Segfault has worked closely with Tarides over
the last couple of years, most notably on the award-winning [Multicore
OCaml](https://github.com/ocaml-multicore/ocaml-multicore)
project and the upstreaming plans for OCaml 5.0. This alliance furthers the
goals of Tarides, bringing the compiler and benchmarking expertise of the
Segfault team directly into the Tarides organisation.

KC Sivaramakrishnan, CEO & CTO of Segfault Systems says that “Segfault Systems
was founded to secure the foundations of scalable systems programming in OCaml.
We have [successfully incorporated](https://github.com/ocaml/ocaml/pull/10831)
cutting-edge research on
[concurrent](https://dl.acm.org/doi/10.1145/3453483.3454039) and
[parallel](https://dl.acm.org/doi/10.1145/3408995) programming into OCaml. This
addresses the long-standing need of OCaml developers to utilise the widely
available multicore processing on modern machines. Tarides is at the forefront
of OCaml developer tooling and platform support, and we are excited to join the
team to make OCaml the best tool for industrial-strength concurrent and parallel
programming.”

“We’re thrilled to have the Segfault Systems team join Tarides,” says Thomas
Gazagnaire, CTO of Tarides. “They have been integral to the success of the
Multicore OCaml project, which has combined cutting edge research and
engineering with consistent communication, promoting Multicore OCaml as an
upstream candidate to the core developer team, as well as [publishing monthly
reports](https://discuss.ocaml.org/tag/multicore-monthly) for the wider
community. We look forward to working with our new partners to make OCaml the
tool of choice for developers.”

All of Segfault Systems’ existing responsibilities and open-source commitments
will migrate over to Tarides, where work will continue towards the three main
objectives in 2022: 

* Releasing OCaml 5.0 with support for domains and effect handlers
* Supporting the ecosystem to migrate the OCaml community over to OCaml 5.0 
* Improving developer productivity for OCaml 5.0 by releasing the best platform
  tools

## OCaml 5.0

The next major release of OCaml, version 5.0, will feature primitive support for
parallel and concurrent programming through domains and effect handlers. The
goal is to ensure that the fine balance that OCaml has struck between ease of
use, correctness and performance over the past 25 years continues into the
future with these additional features. 

Domains enable shared-memory parallel programming allowing OCaml programs to run
on multiple cores: with domains, OCaml programs will scale better by exploiting
multicore processing. Effect handlers are a mechanism for concurrent
programming: with the introduction of effect handlers, simple direct-style OCaml
code will be flexible, easy to develop, debug and maintain (no more monads for
concurrency!). These features will benefit the entire ecosystem and community,
and we expect it to attract many new users to the language. 

As part of the Multicore OCaml project, the team developed
[Sandmark](https://github.com/ocaml-bench/sandmark), a suite of sequential and
parallel benchmarks together with the infrastructure necessary to carefully run
the programs and analyse the results. Sandmark has been instrumental in
assessing and tuning the scalability of parallel OCaml programs and ensuring
that OCaml 5.0 does not introduce performance regressions for existing
sequential programs compared to OCaml 4. 

<p align="center"> <img width="75%" src="{{ base.url }}/assets/images/scalability.png" /> </p>
<p align="center" style="font-size:75%;"> <i>Scalability of compute intensive OCaml programs from Sandmark nightly</i> </p>

Sandmark is now run as [a nightly service](https://sandmark.ocamllabs.io)
monitoring the performance of OCaml 5 as it is being developed. Development will
continue to make it even easier to use and more practical by fully integrating
it with current-bench (the continuous benchmarking system based on
[OCurrent](https://github.com/ocurrent/ocaml-ci)). [Get in
touch](https://tarides.com/company) if you want to know more.

## Ecosystem

At Tarides, we want all OCaml users to benefit from the new features that OCaml
5.0 will bring, and this means ensuring that the ecosystem is fully prepared. We
aim to develop and maintain a robust set of libraries that work with domains and
effects, together with a diverse parallel benchmarking and performance profiling
suite to use with OCaml 5 applications. The [first version of
Eio](https://discuss.ocaml.org/t/eio-0-1-effects-based-direct-style-io-for-ocaml-5/9298), the
effects-based direct-style IO stack for OCaml 5.0, has been released, generating
lots of interesting discussion within the community. The next step is to iterate
on the design in collaboration with the community and our partners. [Get in
touch](https://tarides.com/company) if you have performance-sensitive
applications that you’d like to port to Eio, so we can discuss how the design
can meet your needs.

<p align="center"> <img src="{{ base.url }}/assets/images/http_load.png" /> </p>
<p align="center" style="font-size:75%;"> <i>HTTP server performance using 24 cores</i> </p>

<p align="center"> <img src="{{ base.url }}/assets/images/http_cores.png" /> </p>
<p align="center" style="font-size:75%;"> <i>HTTP server scaling maintaining a constant load of 1.5 million requests per second</i> </p>

## OCaml Platform

In collaboration with community members and commercial funders, Tarides has been
developing and defining the [OCaml platform tool
suite](https://v3.ocaml.org/learn/platform) for the last four years. The goal of
the platform is to provide OCaml developers with easy access to high-quality,
practical development tools to build any and every project. We will continue to
develop and maintain these tools, and make them available for OCaml 5. [Reach
out to us](https://tarides.com/company) if you have specific feature requests to
make your developer teams more efficient.

This alliance brings the headcount of Tarides up to 60+ people, all working
towards making OCaml the best language for any and every project. [Join
us](https://tarides.com/company/)!
