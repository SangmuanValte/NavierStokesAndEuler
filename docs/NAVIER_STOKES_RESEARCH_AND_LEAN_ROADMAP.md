# Navier–Stokes Research and Lean Verification Roadmap

**Workstream:** NS-VERIFY-001  
**Document status:** Research specification / proposed theorem plan  
**Repository:** `SangmuanValte/NavierStokesAndEuler`  
**Last reviewed:** 2026-11-13  
**Verification status:** Source inspection only for this document; no Lean build or Comparator run was performed while preparing it.

> **Epistemic rule:** A mathematical statement, a proof sketch, a Lean declaration, a successful kernel check, a Comparator result, and independent mathematical validation are different evidence levels. Do not collapse them into one PASS.

## 1. Scope and problem separation

The three-dimensional incompressible Navier–Stokes equations with viscosity \(\nu>0\) and external force \(f\) are

\[
\partial_t u+(u\cdot\nabla)u-\nu\Delta u+\nabla p=f,
\qquad \nabla\cdot u=0.
\]

This workstream tracks three distinct questions.

### 1.1 Unforced global regularity

Set \(f=0\). The global regularity question asks whether every admissible smooth, divergence-free initial velocity with finite energy generates a global smooth solution.

**Status: OPEN.** Neither the CKN partial-regularity theorem nor the forced construction described below resolves this question.

### 1.2 Unforced finite-time blow-up

The question whether there exists admissible smooth initial data for the unforced three-dimensional equations that develops a finite-time singularity is also **NOT ESTABLISHED**.

Do not infer an unforced singularity from a theorem whose witness includes a nonzero external force.

### 1.3 Forced finite-time breakdown

The OpenAI paper [Finite Time Blowup for Navier–Stokes](https://openai.com/index/navier-stokes-solution/) reports a construction with smooth external forcing. Its stated whole-space behavior includes zero initial velocity, bounded kinetic energy up to the singular time, and unbounded velocity supremum as the singular time is approached.

The repository formalizes the corresponding whole-space and periodic breakdown alternatives. This is a **forced** result. It is not an unforced global-regularity proof or an unforced blow-up construction.

The formal theorem's negation of a global-solution predicate must be interpreted using the exact fields of that predicate. The analytical paper supplies the more specific finite-time blow-up behavior; the Lean negation alone does not identify which field would fail.

## 2. CKN scale-invariant quantities

Let
\[
Q_r(z_0)=B_r(x_0)\times(t_0-r^2,t_0).
\]
For a suitable weak solution, define
\[
A(r)=\frac1r\operatorname*{ess\,sup}_{t_0-r^2<t<t_0}
\int_{B_r(x_0)}|u(x,t)|^2\,dx,
\]
\[
E(r)=\frac1r\int_{Q_r(z_0)}|\nabla u|^2\,dx\,dt,
\qquad
C(r)=\frac1{r^2}\int_{Q_r(z_0)}|u|^3\,dx\,dt,
\]
and
\[
D(r)=\frac1{r^2}\int_{Q_r(z_0)}
|p(x,t)-(p)_{B_r(x_0)}(t)|^{3/2}\,dx\,dt,
\]
where
\[
(p)_{B_r(x_0)}(t)=\frac1{|B_r|}\int_{B_r(x_0)}p(x,t)\,dx.
\]

These quantities are invariant under the Navier–Stokes parabolic scaling
\[
u^{(\lambda)}(x,t)=\lambda u(\lambda x,\lambda^2t),
\qquad
p^{(\lambda)}(x,t)=\lambda^2p(\lambda x,\lambda^2t).
\]
The pressure is considered modulo a spatially constant function of time; subtracting its spatial mean removes that gauge freedom in \(D(r)\).

## 3. Velocity interpolation inequality: statement and proof

### 3.1 Zero-boundary / global Sobolev version

For almost every time, assume \(u(t)\in H_0^1(B_r;\mathbb R^3)\), with the required measurability and finite energy/dissipation. The three-dimensional Gagliardo–Nirenberg inequality gives
\[
\|u(t)\|_{L^3(B_r)}
\leq K\|u(t)\|_{L^2(B_r)}^{1/2}
\|\nabla u(t)\|_{L^2(B_r)}^{1/2}.
\]
Cubing,
\[
\int_{B_r}|u|^3
\leq K
\left(\int_{B_r}|u|^2\right)^{3/4}
\left(\int_{B_r}|\nabla u|^2\right)^{3/4}.
\]
Integrate in time and use the essential supremum:
\[
\int_{Q_r}|u|^3
\leq K
\left(\operatorname*{ess\,sup}_t\int_{B_r}|u|^2\right)^{3/4}
\int_{t_0-r^2}^{t_0}g(t)^{3/4}\,dt,
\]
where \(g(t)=\int_{B_r}|\nabla u|^2\). Hölder in time yields
\[
\int_{t_0-r^2}^{t_0}g(t)^{3/4}\,dt
\leq (r^2)^{1/4}
\left(\int_{Q_r}|\nabla u|^2\right)^{3/4}.
\]
Thus
\[
\int_{Q_r}|u|^3
\leq K r^{1/2}
\left(\operatorname*{ess\,sup}_t\int_{B_r}|u|^2\right)^{3/4}
\left(\int_{Q_r}|\nabla u|^2\right)^{3/4}.
\]
Substituting \(\operatorname*{ess\,sup}_t\int|u|^2=rA(r)\) and \(\int|\nabla u|^2=rE(r)\), then dividing by \(r^2\), gives
\[
\boxed{C(r)\leq K A(r)^{3/4}E(r)^{3/4}.}
\]

This proof is valid under the stated Sobolev boundary hypothesis (or the corresponding whole-space Sobolev assumptions). It must not be applied to a general interior cylinder as though the solution had zero trace on its boundary.

### 3.2 Interior-cylinder cutoff version

For a general interior suitable weak solution, choose a spatial cutoff \(\eta\) supported in \(B_{2r}\), equal to one on \(B_r\), with \(|\nabla\eta|\leq K/r\). Apply the Sobolev interpolation estimate to \(\eta u\). The cutoff derivative contributes a lower-order term. With quantities centered at the same point and the larger cylinder contained in the solution domain, the resulting form is
\[
\boxed{
C(r)\leq K A(2r)^{3/4}
\big(A(2r)+E(2r)\big)^{3/4}.
}
\]
The constant depends on the cutoff construction and dimension, not on \(u\) or \(r\). The precise localization, time interval, and cutoff support must be encoded in the formal statement.

### 3.3 Scaling check

The normalized factors scale as
\[
\operatorname*{ess\,sup}_t\int_{B_r}|u|^2\sim r,
\qquad
\int_{Q_r}|\nabla u|^2\sim r.
\]
The time Hölder factor contributes \(r^{1/2}\). Hence the unnormalized right-hand side scales as \(r^2\), matching \(\int_{Q_r}|u|^3\). The normalized inequality is dimensionless.

## 4. Local energy inequality and CKN implication

For a suitable weak solution and nonnegative smooth compactly supported test function \(\phi\), the local energy inequality is
\[
\begin{aligned}
&\int_{\mathbb R^3}|u(t)|^2\phi(t)\,dx
+2\nu\int_0^t\int_{\mathbb R^3}|\nabla u|^2\phi\,dx\,ds\\
&\leq \int_{\mathbb R^3}|u_0|^2\phi(0)\,dx
+\int_0^t\int_{\mathbb R^3}|u|^2(\partial_s\phi+\nu\Delta\phi)\,dx\,ds\\
&\quad+\int_0^t\int_{\mathbb R^3}(|u|^2+2p)u\cdot\nabla\phi\,dx\,ds.
\end{aligned}
\]
The local energy inequality is a hypothesis/property of suitable weak solutions; it is not an automatic property of every Leray–Hopf weak solution.

CKN combines this inequality with pressure estimates and scale-invariant iteration to prove an epsilon-regularity criterion and the parabolic Hausdorff bound
\[
\mathcal H^1_{\mathrm{par}}(S)=0
\]
for the interior singular set \(S\), in the appropriate theorem formulation.

Do not claim that this bound proves \(S=\varnothing\), and do not claim that a singular point exists. The epsilon-regularity theorem must be stated with its exact cylinder, force hypotheses (if any), and constants. Pressure estimates must control both the local singular-integral component and the harmonic component; pressure normalization is spatial at each time.

## 5. Repository source map

The following paths were inspected on the repository's default branch:

| Path | Observed role |
|---|---|
| `NavierStokes/ComparatorDefinitions.lean` | Independent copy of the comparator definitions and predicates |
| `ComparatorChallenges/NavierStokes.lean` | Reference challenge definitions and theorem statements; intentional `sorry` placeholders |
| `NavierStokes/ComparatorBridge.lean` | Coordinate/operator correspondence and viscosity normalization |
| `NavierStokes/ComparatorR3Theorem.lean` | Whole-space option (C) bridge |
| `NavierStokes/ComparatorTheorem.lean` | Periodic option (D) bridge |
| `NavierStokes/ComparatorSolution.lean` | Public theorem adapters and `#print axioms` commands |
| `ComparatorChallenges/NavierStokes.json` | Comparator challenge/solution module mapping and permitted axioms |
| `lakefile.toml`, `lean-toolchain` | Project targets and pinned Lean/Mathlib/Comparator versions |
| `.github/workflows/verify-navier-stokes.yml` | Build, proof-boundary, axiom-report and Comparator workflow |
| `formalization.yaml` | Formalization metadata and claimed status |

The checked-in whole-space predicate `NavierStokesExistenceAndSmoothnessRn` extends the base solution structure. Its visible fields require:
- the forced PDE on \(t\geq0\);
- incompressibility;
- the initial condition;
- velocity and pressure smoothness on the closed time half-space;
- \(L^2\) velocity at each time; and
- a uniform-in-time upper bound on total kinetic energy.

The whole-space breakdown statement quantifies over admissible decaying initial velocity and force and negates existence of a pair satisfying that entire predicate. The theorem statement alone does not specify which predicate field fails. The analytical construction is needed to establish the finite-time unbounded-velocity mechanism.

The repository's solution adapter imports the bridge theorem modules; the reference challenge is configured separately in Comparator. Keep this import boundary intact.

## 6. Lean theorem ladder

The following are theorem contracts and implementation milestones, not assertions that these declarations already exist or compile. Each theorem must be accompanied by explicit definitions, assumptions, source references, and a test/build record.

| ID | Proposed Lean theorem | Required assumptions | Conclusion / acceptance evidence |
|---|---|---|---|
| NS-L01 | `parabolic_scaling_maps_solution` | Defined PDE, differentiability, \(\nu>0\), scaling map | Scaled fields satisfy the same-viscosity PDE with correspondingly scaled force; prove by chain rule |
| NS-L02 | `normalized_quantities_scale_invariant` | Defined cylinders, integrals, \(r,\lambda>0\) | Exact transformation laws for \(A,E,C,D\) |
| NS-L03 | `sobolev_L6_bound_3d` | Correct \(H_0^1\) or whole-space Sobolev domain | \(\|u\|_6\leq K\|\nabla u\|_2\) |
| NS-L04 | `gagliardo_nirenberg_L3_3d` | Same domain and integrability assumptions | \(\|u\|_3\leq K\|u\|_2^{1/2}\|\nabla u\|_2^{1/2}\) |
| NS-L05 | `time_holder_three_quarters` | Measurable nonnegative \(g\in L^1(I)\), finite interval | \(\int_I g^{3/4}\leq |I|^{1/4}(\int_Ig)^{3/4}\) |
| NS-L06 | `velocity_interpolation_zero_boundary` | NS-L03/04/05, time-dependent Sobolev membership and finite energy | \(C(r)\leq K A(r)^{3/4}E(r)^{3/4}\) |
| NS-L07 | `velocity_interpolation_interior_cutoff` | Cutoff support/derivative bounds and larger-cylinder energy | Interior estimate with \(A(2r)+E(2r)\) lower-order term |
| NS-L08 | `suitable_solution_local_energy_inequality` | Exact suitable-weak-solution definition and admissible test function | Local energy inequality; do not assume it for arbitrary Leray–Hopf solutions |
| NS-L09 | `pressure_local_harmonic_decomposition` | Pressure distribution, spatial domain and normalization | Local singular-integral part plus harmonic remainder |
| NS-L10 | `pressure_decay_nested_cylinders` | NS-L09 and exact pressure/velocity integrability | Precisely sourced nested-cylinder pressure estimate |
| NS-L11 | `ckn_epsilon_regularity` | Suitable solution, force assumptions, pressure and local-energy estimates | Smallness criterion implies regularity on a smaller cylinder |
| NS-L12 | `ckn_parabolic_singular_set_bound` | NS-L11 plus covering/iteration argument | \(\mathcal H^1_{\mathrm{par}}(S)=0\) |
| NS-L13 | `forced_breakdown_R3_source_correspondence` | Exact paper theorem, source-faithful definitions and candidate properties | Formal statement corresponds to the paper's forced whole-space result |
| NS-L14 | `unforced_global_regular_or_singular_dichotomy` | Precisely defined solution class and maximal-lifespan notion | A correctly stated dichotomy; not a proof of either branch |
| NS-L15 | `unforced_global_regular_or_blowup` | Exact unforced hypotheses and a proved continuation/blow-up alternative | Only prove the alternative theorem if all hypotheses and continuation criteria are formalized; neither global regularity nor blow-up existence follows from the alternative |

### 6.1 Lean design constraints

1. Prefer actual Mathlib measure, Bochner integral, Sobolev and function-space definitions over custom predicates that merely have familiar names.
2. State whether functions are scalar- or vector-valued and which norm is used.
3. Encode the domain and boundary condition explicitly. Do not hide a zero-trace assumption inside a theorem name.
4. Define parabolic cylinders and time slices explicitly, including whether the time endpoint is open or closed.
5. Distinguish almost-everywhere statements from pointwise statements and essential suprema from ordinary suprema.
6. Keep pressure defined modulo functions of time and state the chosen normalization.
7. Do not introduce the desired inequality, epsilon-regularity criterion, or breakdown theorem as an axiom to make a downstream theorem close.
8. Every `sorry`, `admit`, custom axiom, opaque imported theorem, and nonstandard axiom must be inventoried. A theorem with an unreviewed assumption is not an independently checked theorem.
9. Use exact source theorem names and line/file references in the paper-to-Lean correspondence table.
10. A successful Lean kernel check verifies the encoded proposition from its dependencies. It does not by itself prove that the encoding faithfully represents the intended PDE theorem.

## 7. Existing repository verification workflow: source-review finding

The README describes the `sorry`/ `admit` check as a solution-module check. The current workflow command instead searches every tracked Lean file:

```bash
matches=$(git ls-files '*.lean' | xargs -r grep -nE '\b(sorry|admit)\b' || true)
```

The reference file `ComparatorChallenges/NavierStokes.lean` visibly contains intentional `sorry` theorem placeholders. Therefore, the current repository-wide grep includes the challenge module and is inconsistent with the documented intention to reject placeholders in the solution module only. This is a static source finding; no workflow run was executed as part of this document.

**Recommended correction (separate CI change):**
- scan the production solution import closure or an explicit allowlist of solution files;
- exclude the reference challenge module only from the solution-placeholder scan;
- retain the independent compilation of the solution module;
- retain the kernel axiom report and reject `sorryAx`;
- retain the Comparator run against the reference challenge;
- report challenge placeholders and solution placeholders separately.

The root-level `formalization.yaml` `sorry_count` field should state whether it counts the solution closure or every Lean file in the repository. Do not label a repository-wide count as zero while a tracked challenge file contains intentional placeholders.

No CI workflow was changed by this documentation task.

## 8. Reproducible verification protocol

Run from a clean checkout of the exact commit under review:

```sh
lake exe cache get
lake build
lake env lean NavierStokes/ComparatorSolution.lean
lake exe comparator ComparatorChallenges/NavierStokes.json
lake exe comparator ComparatorChallenges/Euler.json
```

Record:
- repository and exact commit SHA;
- Lean toolchain and Lake manifest hash;
- command, exit code and complete log for each step;
- theorem names and `#print axioms` output;
- all imported modules and any custom axioms;
- Comparator version and complete output;
- artifact hashes;
- independent reviewer, review date and scope;
- exact paper version and theorem/lemma correspondence.

A CI PASS means the configured commands passed on that exact commit. It does not establish independent mathematical validation, semantic equivalence to every intended Clay formulation, or acceptance by the Clay Mathematics Institute.

## 9. Acceptance gates

| Gate | Requirement | Status for this document |
|---|---|---|
| G0 — Scope | Forced/unforced and CKN claims separated | DOCUMENTED |
| G1 — Source map | Existing repository modules inspected | SOURCE-INSPECTED |
| G2 — Interpolation proof | Mathematical proof with hypotheses stated | DOCUMENTED; Lean proof pending |
| G3 — Functional analysis | Sobolev/GN/Hölder theorems formalized | NOT IMPLEMENTED |
| G4 — Suitable solution | Weak-solution and local-energy definitions audited | NOT IMPLEMENTED |
| G5 — CKN | Pressure, epsilon regularity and Hausdorff proof formalized | NOT IMPLEMENTED |
| G6 — Forced result | Paper-to-Lean correspondence independently reviewed | PENDING |
| G7 — Reproduction | Clean build and Comparator run on exact commit | NOT RUN FOR THIS DOCUMENT |
| G8 — Independent review | External mathematical/formal review | PENDING |
| G9 — Unforced problem | Accepted proof of either global regularity or blow-up | OPEN |

## 10. Primary references

1. Caffarelli, Kohn and Nirenberg, *Partial regularity of suitable weak solutions of the Navier–Stokes equations*, Communications on Pure and Applied Mathematics (1982).
2. Lin, *A new proof of the Caffarelli–Kohn–Nirenberg theorem*, Communications on Pure and Applied Mathematics (1998).
3. OpenAI, [Finite Time Blowup for Navier–Stokes](https://openai.com/index/navier-stokes-solution/), paper and accompanying Lean formalization.
4. Clay Mathematics Institute, [Navier–Stokes Existence and Smoothness](https://www.claymath.org/millennium/navier-stokes-equation/).
5. Repository reference definitions: `ComparatorChallenges/NavierStokes.lean`, adapted from the pinned Google DeepMind Formal Conjectures source commit identified in that file's header.

---

**Final disposition:** This document records an analytical interpolation proof under explicit Sobolev hypotheses, a separate interior cutoff estimate, the forced/unforced distinction, a source map for the existing Lean repository, and a staged formalization/verification plan. It does not claim a Lean proof of CKN, a new Navier–Stokes theorem, independent verification of the OpenAI construction, or a solution to the unforced Millennium Problem.
