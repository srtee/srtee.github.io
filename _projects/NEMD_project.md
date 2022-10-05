---
layout: page
title: Non-Equilibrium Molecular Dynamics
description: Theory and thermostats for molecular dynamics of flowing systems
importance: 2
category: work
---

Molecular dynamics focuses on systems at equilibrium -- for example, by simulating a box of water molecules just "sitting there", we can learn about how the properties of a cup or an ocean of water derive from the behaviour of individual water molecules.
However, non-equilibrium systems are both vital and fascinating to study.
From 3D printing to nanofluidic friction to electrolyte conductivity, non-equilibrium systems are everywhere in daily life!

At the [Bernhardt Group](https://aibn.uq.edu.au/bernhardt) we focus on making non-equilibrium simulations more rigorous and informative.
Out of equilibrium, familiar daily concepts like temperature and pressure become harder to define and measure, since we must distinguish between the thermal random motion of particles and concerted flows encoding the twisting, shearing, or stretching we have imposed.
Furthermore, a non-equilibrium simulation usually produces [dissipation](https://en.wikipedia.org/wiki/Fluctuation_theorem#Dissipation_function), which describes the irreversibility of the process and holds clues to unlocking computationally-efficient non-equilibrium molecular dynamics methods.

We are currently building native molecules-first internals for [LAMMPS](https://www.lammps.org/) which will enable properly molecular thermostats and barostats for accurate non-equilibrium simulation.
I am also studying how [constant potential molecular dynamics](/projects/CPMMD_project) affects the nanoscale friction measured between a flowing electrolyte and conductive electrodes, especially if they are charged to a specific potential difference.
We ultimately want to accelerate non-equilibrium molecular dynamics in LAMMPS using [Kokkos](https://kokkos.org/about) to harness GPUs or other heterogeneous computing setups, in work funded by the [Pawsey Centre for Extreme scale Readiness](https://pawsey.org.au/pacer/) project VISCOUS.
