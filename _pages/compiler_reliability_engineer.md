---
layout: post
permalink: compiler_reliability_engineer.html
published: true
---

## Compiler Reliability Engineer

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

As we get closer to releasing OCaml 5.0 with Multicore support, there is a need
to _**ensure that the multicore support works well with the rest of the
ecosystem**_. The ecosystem includes the open-source tools and libraries
maintained by the Multicore team as well as the wider ecosystem. We have built a
number of tools to assist with the task at hand. Some of the tools are:

* [OPAM Health Check](http://check.ocamllabs.io:8082/) shows the result of
  building the packages from the [OPAM repo](https://opam.ocaml.org/packages/).
  The table shows the result of building the package and running its opam tests
  (if present) on the [stock OCaml compiler](https://github.com/ocaml/ocaml) and
  the domains-only version of the multicore compiler. Any failure in the
  multicore column with a success in the stock column must be investigated.
* [Multicore OCurrent CI](https://multicore.ci.ocamllabs.io:8100/) tests a
  matrix of blessed packages that the multicore team considers important for the
  ecosystem. Unlike OPAM health check, the packages and the compiler variants
  may be unreleased and part of other development branches in GitHub. This tool
  builds the workflow as a dependency graph of individual build tasks. The
  individual tasks will be rebuilt on demand when the corresponding item being
  tracked (say the branch on GitHub) is updated. 

## Responsibilities

As a compiler reliability engineer, your tasks will include:

* Investigating failures thrown up by OPAM Health Check monitoring and deeper
  package testsuite runs with multicore. Determine if the issue is a problem for
  the package maintainer or the multicore compiler. 
* File issues and work with the relevant team (external or multicore) to get it
  fixed by producing reproducible test cases possibly even identifying the
  subsystem where the problem is occurring.
* Help organise and link ocaml-multicore bug reports such that issues are
  correctly linked or merged. 
* Provide support to the multicore team in understanding which bugs and/or
  features external packages require in order to function well. 
* Work with the rest of the CI team to ensure that the multicore CI systems are
  running as expected. 

## Qualification & Experience

_(You don’t have to fill 100% of the qualifications to apply.)_

* Demonstrable experience with existing open source projects
* Ideally some level of experience with the OCaml language, or other functional
  programming languages
* Experience with GitHub, Git and version control
* Ideally some experience with the OCaml platform ecosystem e.g. dune, opam
* Domain knowledge relevant to the projects - package management, CI and build
  environments, developer workflow and processes
* Curious and solution-oriented mindset
* Experience of working within multidisciplinary and remote teams
* Good communication skills in English (English is the corporate language)

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
