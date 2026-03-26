---
layout: page
title: CFD Methodology & Validation
permalink: /cfd-methodology/
description: The reasoning behind solver choices, turbulence modelling, meshing strategy, and validation approaches used across my CFD projects.
nav: false
nav_order: 4
---

Good CFD results require more than running a simulation — they require understanding why every setting is correct for the specific problem. This page documents the reasoning behind the methodology I use across my projects, so that the choices are transparent and defensible rather than inherited from a tutorial.

---

## Solver Formulation

### Density-Based vs Pressure-Based

I use a **density-based solver** for cases involving Mach numbers above approximately M0.5, and a **pressure-based coupled solver** for purely subsonic cases below that threshold.

The distinction matters because the two formulations handle compressibility differently. At low Mach numbers the flow is nearly incompressible — pressure and density are weakly coupled — and the pressure-based solver handles this naturally. The density-based solver becomes numerically stiff at low Mach because the acoustic and convective timescales diverge, leading to slow convergence or instability without preconditioning.

Above M0.5, and especially in transonic and supersonic flow where density changes are large and shocks are present, the density-based formulation is the correct choice. The governing equations are solved in their fully compressible form, capturing shock discontinuities and strong density gradients accurately.

For my nosecone parametric study, which spans M0.3–3.0, I use density-based for M0.95 and above, and will switch to pressure-based coupled for the M0.3 and M0.7 cases where compressibility effects are negligible.

---

## Turbulence Modelling

### Why k-ω SST

All of my external aerodynamics simulations use the **k-ω SST (Shear Stress Transport)** turbulence model, developed by Menter (1994).

SST is a blended two-equation model that addresses the fundamental weaknesses of its predecessors:

**Pure k-ε** performs well in the freestream and in fully attached boundary layers, but breaks down in adverse pressure gradient regions and near separation points — precisely the regions that matter most in external aerodynamics.

**Pure k-ω** handles adverse pressure gradients and near-wall flow accurately, including separation onset, but is sensitive to freestream turbulence intensity — the freestream boundary conditions pollute the interior solution.

**SST** blends both: k-ω is active near walls where accurate boundary layer and separation prediction is critical, while k-ε governs in the freestream where k-ω's sensitivity would cause problems. The transition between the two is controlled automatically by a blending function based on wall distance.

For my nosecone study this matters in three specific regions:

- **Base shoulder** — adverse pressure gradient as the nosecone transitions to the cylindrical body; separation prediction is critical for base drag accuracy
- **Base cavity** — recirculating separated flow behind the blunt base face; the dominant source of base drag
- **Shock-boundary layer interaction** — at M1.5+ the oblique shock interacts with the surface boundary layer; SST is the industry standard for this phenomenon

---

## Mesh Strategy

### Wall Resolution & y⁺ Targeting

I use **wall-resolved** meshing with a target y⁺ ≈ 1, corresponding to a first cell height of approximately [FILL IN: your first cell height, e.g. 2 μm] at the wall.

This ensures the viscous sublayer is fully resolved rather than bridged with wall functions. Wall functions (y⁺ ~ 30–300) are acceptable for attached boundary layers but introduce significant error near separation and reattachment. Since base drag and shock-boundary layer interaction are central to my results, wall-resolved meshing is the correct choice despite the additional cell count.

Inflation layers use [FILL IN: your layer count, growth rate, transition type] applied to all solid wall surfaces.

### Domain Sizing

For external supersonic simulations, farfield boundaries are placed at a minimum of 15 body radii from the centreline. This is driven by the need to prevent shock reflections off the farfield boundary from corrupting surface pressure results.

Fluent's Pressure Far-Field boundary condition uses Riemann invariants to allow disturbances to leave the domain — but for strong oblique shocks the characteristic decomposition is first-order accurate and produces weak spurious reflections. Sufficient domain height ensures these reflections dissipate before reaching the body surface.

For my nosecone study the farfield is set at 1,500 mm (30 body radii) — conservative, but necessary given the Student Edition cell count constraint means I cannot use the additional interior refinement that would otherwise compensate for a closer boundary.

---

## Boundary Conditions

### Pressure Far-Field

All outer boundaries in my compressible flow simulations — inlet, outlet, and farfield — use **Pressure Far-Field** rather than velocity inlet or pressure outlet.

A velocity inlet over-constrains the problem in supersonic flow. In supersonic regions, disturbances cannot propagate upstream — information travels only downstream. Prescribing velocity at an inlet effectively forces information in the wrong direction, leading to divergence or non-physical results.

Pressure Far-Field uses Riemann invariants: at each boundary point, the characteristic wave decomposition determines which information is incoming (imposed from freestream conditions) and which is outgoing (determined by the interior solution). This makes the boundary physically correct regardless of whether the local flow is subsonic or supersonic — critical for a simulation spanning M0.3–3.0 where the boundary may see different flow regimes depending on the Mach number being simulated.

Freestream conditions are set to ISA sea-level: P∞ = 101,325 Pa, T∞ = 288.15 K, with Mach number varied per simulation run.

---

## Transonic Convergence

The M0.95 case requires special handling. In the transonic regime, mixed subsonic and supersonic regions exist simultaneously on the body surface, with local shocks that oscillate rather than sitting at a fixed location. A steady-state solver is effectively chasing a moving target.

To manage this:

- Courant number is reduced from [FILL IN: your standard value] to [FILL IN: your transonic value, e.g. 0.5]
- Iteration count is increased to [FILL IN: ~1500+]
- Convergence is judged on Cd stabilisation in addition to residual drop

Reducing the Courant number forces the solver to take smaller steps per iteration, limiting the error introduced by each oscillation cycle and preventing divergence. The cost is significantly more iterations to reach a converged solution — in my experience roughly [FILL IN: your ratio, e.g. 3–4×] more iterations than the supersonic cases.

---

## Validation Approach

Results are validated at two levels:

**Qualitative:** Flow features are checked against known physics — attached oblique shock from the tip at supersonic speeds, expansion fan at the base shoulder, subsonic recirculation in the base cavity. Unexpected flow features are investigated before results are recorded.

**Quantitative:** Cd values are compared against [FILL IN: your specific validation source — e.g. NACA TN 1135, ESDU data, oblique shock theory]. At M2.0 the conical nosecone Cd of 0.3681 compares to a theoretical prediction of approximately [FILL IN: your theoretical value] — a difference of [FILL IN: %], consistent with the additional viscous drag not captured by inviscid theory.

A mesh independence study is planned at [FILL IN: target cell counts] to quantify discretisation error. Results will be reported with Richardson extrapolation to estimate the grid-converged value.

---

## Known Limitations

All simulations are 2D axisymmetric at zero angle of attack. This assumption is valid for the nosecone study where rotational symmetry holds, but cannot capture:

- Angle of attack effects on pressure distribution
- Fin-body junction phenomena including horseshoe vortex formation
- Asymmetric flow separation at high AoA

The fin phase of the nosecone study will require a 3D quarter-body domain, which at adequate resolution (estimated 500k–1.5M cells) exceeds the ANSYS Student Edition limit. Academic licensing or HPC access through Queen's University Belfast is being explored for this phase.

---

*Solver: ANSYS Fluent 2026 R1. Mesh: ANSYS Meshing. Geometry: SolidWorks / SpaceClaim*