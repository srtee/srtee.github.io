---
layout: post
title: "Spack Time 3: Wait, you don't have to Spack everything?"
date: 2022-10-06 10:40:00 +1000
tags: spack doco
---

## Locating the culprit

Last time, I crashed and burned while trying to install LAMMPS with OneAPI, like so:

`spack install lammps@20220915 %oneapi ^intel-oneapi-mkl ^intel-oneapi-mpi`

The install was an incredible disaster, with Spack installing (or trying to!) almost an entire OS's worth of extraneous packages, including Cython, Rust, and LLVM for AMD GPU.
Here's what I *should* have done before rushing off to try to build LAMMPS with OneAPI:

`spack spec lammps@20220915 %oneapi ^intel-oneapi-mkl ^intel-oneapi-mpi`

Take a look:

{% include figure.html path="assets/img/cond.png" class="img-fluid rounded z-depth-1" zoomable=true
   alt="Terminal output from the `spark spec` command arranged into six columns,
   showing that almost all dependencies came from Intel OneAPI MPI"
   caption="Terminal output from the `spark spec` command arranged into six columns,
   showing that almost all dependencies came from Intel OneAPI MPI"
   %}

Everything below the double line in the printout is a dependency of `intel-oneapi-mpi` -- a whopping *166* lines, compared to the 17 lines of dependencies for LAMMPS itself!
The main burden in turn comes from OneAPI MPI trying to build [UCX](https://github.com/openucx/ucx), which in turn tries to build *a brand new hwloc*, which then demands fresh builds of Python, SQLite, XRandr, GLX, Perl, Rust, Wayland ...

## Fine, I'll do it myself

Of course I shouldn't have to do that! It turns out that Spack *will* let me install OneAPI MPI from elsewhere, as long as I can show it that I know what I'm doing.
So I installed OneAPI MPI on my own (which did not require me to build a brand new Linux), created a clean Spack environment, and then used `spack config edit` to set up my preferences:

```
...
   packages:
     all:
       providers:
         mpi: [intel-oneapi-mpi]
     ...
     intel-oneapi-mpi:
       externals:
       - spec: intel-oneapi-mpi@2021.6.0
         prefix: /opt/intel/oneapi/mpi
       buildable: false
```

With that set up (and a quick jaunt to GitHub to grab Spack's `develop` branch), Spack simply downloaded the LAMMPS code and compiled it in about ten minutes. Win!

## Conclusion: Installation Workflow

With all 
