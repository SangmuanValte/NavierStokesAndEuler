# Finite time blowup for Navier–Stokes and Euler equations

[![Verify Navier–Stokes Contribution](https://github.com/SangmuanValte/NavierStokesAndEuler/actions/workflows/verify-navier-stokes.yml/badge.svg)](https://github.com/SangmuanValte/NavierStokesAndEuler/actions/workflows/verify-navier-stokes.yml)

This repository contains Lean 4 formalizations of the results presented in
“Finite time blowup for Navier–Stokes” and
“Finite time blowup for the Euler equation” by OpenAI.

## Navier Stokes

For every positive viscosity, the current comparator submission targets two results:

- **Whole space $\mathbb{R}^3$:** a breakdown alternative in which there exist smooth initial data and forcing for which no global smooth solution with uniformly bounded kinetic energy exists.
- **Periodic torus $\mathbb{R}^3/\mathbb{Z}^3$:** a breakdown alternative in which there exist smooth periodic initial data and forcing for which no global smooth solution exists.

These are alternatives [**(C)**](https://www.claymath.org/wp-content/uploads/2022/06/navierstokes.pdf#page=2) “Breakdown of Navier–Stokes solutions on ℝ³”
and [**(D)**](https://www.claymath.org/wp-content/uploads/2022/06/navierstokes.pdf#page=2) “Breakdown of Navier–Stokes Solutions on ℝ³/ℤ³”
in the Clay Mathematics Institute’s [official problem description](https://www.claymath.org/wp-content/uploads/2022/06/navierstokes.pdf)
of the [Navier–Stokes existence and smoothness](https://www.claymath.org/millennium/navier-stokes-equation/)
[Millennium Prize Problem](https://www.claymath.org/millennium-problems/).

The repository separates the reference challenge from the independent solution module. The configured comparator manifest names `ComparatorChallenges.NavierStokes` as the challenge and `NavierStokes.ComparatorSolution` as the solution. fileciteturn11file0

## Verification workflow

Every relevant push or pull request runs the public GitHub Actions verification workflow. It:

1. installs the pinned Lean toolchain and restores the Mathlib cache;
2. builds the complete Lake project;
3. checks that `NavierStokes.ComparatorSolution` does not import the challenge module;
4. rejects `sorry` and `admit` in the solution module;
5. records the Lean kernel axiom report and rejects `sorryAx` in that report; and
6. publishes a run-specific verification report as a GitHub Actions artifact.

The workflow verifies the formal build and the separation between challenge and solution. It is **not, by itself, a claim of independent mathematical verification of the Navier–Stokes Millennium Prize Problem**.

## Building the formalizations

The project uses Lean 4.34.0-rc2, Mathlib, and Lake. With
[elan](https://github.com/leanprover/elan) installed, fetch the mathlib cache and build the formalizations with:

```sh
lake exe cache get
lake build
```

## Independent proof checking

For instructions on checking the formalizations with Comparator, see the
[ComparatorChallenges README](ComparatorChallenges/README.md).
