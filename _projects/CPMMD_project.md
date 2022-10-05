---
layout: page
title: Constant Potential Molecular Dynamics
description: Dynamic charges for accurate molecular dynamics of charged electrodes 
importance: 1
category: work
---

A better understanding of electrochemistry would help us build better technology and fight climate change.
In molecular dynamics simulations of electrochemical systems, however, history runs headlong into undergraduate electromagnetism.
Molecular dynamics practitioners overwhelmingly represent atoms in molecules as particles whose charge doesn't change -- but, at a conductive surface, charge always rearranges itself so that the potential across the conductor is zero.

As a developer of constant potential molecular dynamics, I strive to bring this dynamic charge rearrangement back into molecular dynamics.
I started doing this to improve then-existing constant potential code in LAMMPS, an effort which first resulted in [USER-CONP2](https://github.com/srtee/lammps-USER-CONP2).
Collaborating with researchers at the Technical University of Hamburg (who taught me, among other things, how to finally code properly) we then successfully included [ELECTRODE](https://docs.lammps.org/Packages_details.html#pkg-electrode) in LAMMPS, making documented and fast constant potential molecular dynamics available in a mainstream molecular dynamics package.

The constant potential project continues on two fronts: speeding it up, by taking further advantage of the simplicity of electrodes with fixed position, and making it more accurate, by matching results with deeper layers of theory such as quantum-mechanical density functional theory.
Our ultimate goal is to make constant potential molecular dynamics a simple and widely-used tool of choice for studying electrode-electrolyte interfaces.
