---
layout: post
title: "Spack Time 2: Pigz flying off glyphs of doom"
date: 2022-10-04 10:32:00 +1000
tags: spack doco
---

## GCC, aka Getting Code Compiled

Last I wrote, I'd typed in:

`spack install lammps`

An hour of computer time later, everything had Just Worked! Amazing!

Excitedly, I fired up my new install with a `spack load lammps` and confirmed that I had ... the 2021 stable version of LAMMPS. That was exciting in itself, because this confirmed that I could smoothly switch between my custom-compiled LAMMPS versions (based off the Sept 2022 patch) and what Spack had built. Time to build the latest version:

`spack install lammps@20220623`

and again, everything Just Worked!

## Time to get my OneAPI on

Following [Spack's instructions](https://spack.readthedocs.io/en/latest/build_systems/inteloneapipackage.html), I installed the latest versions of Intel's OneAPI and linked it up with Spack. That didn't cause any trouble, which is notable because using Intel compilers on clusters has caused me grief in the past.

So now it was time to get Spack going on my next big job:

`spack install lammps@20220915 %oneapi ^intel-oneapi-mkl ^intel-oneapi-mpi`

Let's unspack[^1] the "sigils" Spack uses:

1. `@` denotes a version string -- LAMMPS uses patch dates, I'm not sure what Spack does with other packages.
2. `%` denotes the compiler used.
3. `^` denotes a specific dependency, because Spack can track abstract dependencies -- for example, Spack knows that LAMMPS can use any MPI, so `^intel-oneapi-mpi` will tell Spack to use Intel MPI in that slot.

And of course, everything compiled perfectly, and Shern coded happily ever after ... *not*.

## The glyphs of doom!

The first package which refused to compile was ... `font-util`! This was an unpleasant surprise. If you're familiar with LAMMPS you will be wondering, *why*? LAMMPS is after all a command-line-only molecular dynamics simulator, so what on earth is it doing looking for fonts? I haven't looked at the dependency graphs but my guess is that some of the output modes probably feature fonts and therefore ask for `font-util` -- or perhaps some other package in line has some odd dependency there.

In any case, I was staring this right in the face:

```
     12934    /home/shernren/anaconda3/bin/fc-cache /home/shernren/spack/opt/spack/linux-ubuntu22.04-zen/oneapi-2022.1.0/font-util-1.3.2-yvqiyb6jnjnpo5ocw5nh4bfcplh2yqhq/share/fonts/X11/cyrillic
     12935    /home/shernren/spack/opt/spack/linux-ubuntu22.04-zen/oneapi-2022.1.0/font-util-1.3.2-yvqiyb6jnjnpo5ocw5nh4bfcplh2yqhq/share/fonts/X11/cyrillic: failed to write cache
  >> 12936    make[2]: *** [Makefile:755: install-data-hook] Error 1
     12937    make[2]: Leaving directory '/tmp/shernren/spack-stage/spack-stage-font-util-1.3.2-yvqiyb6jnjnpo5ocw5nh4bfcplh2yqhq/spack-src/font-cronyx-cyrillic/font-cronyx-cyrillic-1.0.3'
  >> 12938    make[1]: *** [Makefile:681: install-data-am] Error 2
     12939    make[1]: Leaving directory '/tmp/shernren/spack-stage/spack-stage-font-util-1.3.2-yvqiyb6jnjnpo5ocw5nh4bfcplh2yqhq/spack-src/font-cronyx-cyrillic/font-cronyx-cyrillic-1.0.3'
  >> 12940    make: *** [Makefile:632: install-am] Error 2
```

Urgh! Argh! What on Earth! I duly googled "failed to write cache" and dived into a morass of old, odd bug reports. Eventually, it turned out that (1) this was [an old bug in fontconfig](https://gitlab.freedesktop.org/fontconfig/fontconfig/-/issues/107); (2) it was being triggered by *fontconfig from my Anaconda*.

Of course Spack can't solve my snake issues. But what I like about Spack is that, to me at least, it seems more transparent about the whole process. Instead of downloading wheels which magically go round and round and end up making my bus go forward, Spack is just (!) downloading source, compiling, and installing, and it happens to keep track of all its work with the hashes to show for it. I tacked on a few extra options: `-v --keep-prefix` to, respectively, give verbose output and keep all its temporary files around after a failed install. The install files eventually let me confirm that, yes, my Anaconda `fc-cache` was bugged while `/usr/bin/fc-cache` was updated to the latest version. After a quick and very dirty:

```
mv ~/anaconda/bin/fc-cache ~/anaconda/bin/fc-cache-old
ln -s /usr/bin/fc-cache ~/anaconda/bin/fc-cache
```

I was on my way!

## Pigz go fly

Almost immediately another package decided to lie down and not compile, and it was [`pigz`](https://zlib.net/pigz/). Another unpleasant surprise with an even more cryptic error message:

```
==> [2022-10-04-21:59:46.995345] 'make' '-j8'
gcc -O3 -Wall -Wextra -Wno-unknown-pragmas -Wcast-qual   -c -o pigz.o pigz.c
make: gcc: Permission denied
make: *** [<builtin>: pigz.o] Error 127
==> Error: ProcessError: Command exited with status 2:
    'make' '-j8'

2 errors found in build log:
  >> 6    make: gcc: Permission denied
  >> 7    make: *** [<builtin>: pigz.o] Error 127
```

Well, not very cryptic, in that I wasn't able to use GCC while wanting to build everything with Intel's ICPX! That again is a good sign that Spack really was going to compile everything with ICPX or die trying.

But why was `pigz` asking for GCC? Again, Spack helpfully saved the source tar for me to peruse, and what should I find at the top of the Makefile but `CC=gcc`! Well, I don't know how to get Spack to override the source it downloads[^2], but I knew I was never going to ask "but what if I compiled a parallel GZ compressor with Intel instead of GCC?", so off we go changing the dependencies again:

`spack install lammps@20220915 %oneapi ^intel-oneapi-mkl ^intel-oneapi-mpi ^pigz%gcc`

And ... it all worked! This last command has been running the whole time I've been typing this entry, and it's built (glances at other terminal) an entire new Python, LLVM for AMD GPU (which, whoops, it's just tripped over), and (hilariously) *a new fontconfig*, which would have been nice to have while font-util was tripping over itself!

And instead of deciding to stop here (I can see it's already decided that it can't build Intel MPI without that mysterious AMD GPU LLVM, and therefore that it can't build LAMMPS -- sensible), it's gone on now to build *Rust*!

## If only this were a conclusion

It seems that the journey goes on and on. But what I can tell is that Spack really, really does prioritize documenting a reproducible build. Of course it takes a lot of disk space to rebuild everything just to make sure it's the same compiler throughout -- but in the intended HPC use case, that totally makes sense.

Even though I'm laughing (and crying) and some of the more ridiculous errors here, I don't think they're Spack's fault. If I were building LAMMPS from ground up, especially with a list of different compilers and some of the more esoteric sub-packages, I'd be running headfirst into these speed bumps too. Spack is simply doing what I want it to do -- and doing an admirable job keeping track of it all, and giving me the information I need to --

dear heavens above, why is it building me *a brand new NumPy*???

[^1]: I love my puns and I will not lie.

[^2]: Spack does have a "develop mode" where it will build from my specified source directory instead of downloading from a Git repository. But I want to reserve that for developing LAMMPS, not pigz, and so I'll wait until I have all my reference binaries compiled before tinkering with that.
