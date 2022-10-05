---
layout: post
title: "Spack Time 1: Getting started"
date: 2022-10-02 01:32:00 +1000
tags: spack doco
---

## Introduction

I[^1] have some time after sending a big paper off to my supervisor to read, and I've decided to overhaul my build environments. My work desktop has accumulated enough entropy that I can't even power-cycle the *monitors* without having to hard-reset! So it's time to back everything up and put on a fresh Linux install -- which means I might as well do everything right.

*Right*, in this case, means documenting my build environment. To get all evangelistic, documentation is foundational to making science more [FAIR](https://www.go-fair.org/fair-principles/). Documentation is how a complete stranger in the scientific ecosystem can be confident that code I develop and deploy is safe to run on their computer, let alone that it will produce the right results. But guess who's the biggest stranger in my life? It's *me*! I can count on "me" of six months or six years from now to remember practically nothing first-hand, so being all documentative and FAIRy is good for myself as much as for the wider world.

So here I am, documenting how I intend to set everything up correctly, and my first step is Spack.

## Spack (and Singularity)

> Spack is a package manager for supercomputers, Linux, and macOS. It makes installing scientific software easy.
>
> from: [Spack](https://spack.io/)

What? "Makes installing scientific software easy?" Sign me right up!

I'll start by trialling Spack on my home computer, which I'm not turning upside down and rattling at the same time as my work desktop, to see how it goes, and then see how the process goes on my work desktop.

My specific use case is that I (with collaborators at the Hamburg University of Technology) develop the [ELECTRODE package](https://aip.scitation.org/doi/10.1063/5.0099239), as well as working on non-equilibrium methods in molecular dynamics with [CTCMS](https://ctcms-uq.github.io/) at the University of Queensland, in the [LAMMPS](https://www.lammps.org/) code. Besides developing generic code (and writing papers about it) I try to co-develop code with the INTEL package, which relies on Intel directives to go zoom zoom on Intel processors, and I am also co-developing code with the KOKKOS package to (soon) go zoom zoom on AMD GPUs on the brand new [Setonix](https://pawsey.org.au/systems/setonix/) supercomputer.

This means I need to test code against at least three compiler toolchains: Intel, AMD, and good old GCC (because I'm not a monster). In addition, I also want to link against a variety of FFT and linear algebra codes (such as MKL, openBLAS, or openBLIS). This kind of wacky combinatorial development is, supposedly, what Spack is good for. We're about to find out!

(I read lots of chatter about containerizing everything, and I know Singularity / Apptainer is a big deal for eventually deploying to HPC. But, from everything I've read, it sounds like Spack is great on boxes which I *own*, and eventually containers would be important for deploying either to HPC or cloud computes.)

## Installing Spack and LAMMPS

So I installed Spack from GitHub using the [tutorial instructions](https://spack-tutorial.readthedocs.io/en/latest/tutorial_basics.html). Then, naively and straightforwardly, I typed in:

`spack install lammps`

and ... it looks like it will take a while. This seems like a complete post now, so I'll be back when it's done!

[^1]: You might not know who I am yet, and that's okay! I really should write something about me before diving right into this blog, but Documenting Everything takes priority right now.
