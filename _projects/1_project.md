---
layout: page
title: Computational Analysis of a Mach 4 Multi-Ramp External Compression Intake for Ramjet Integration
description: Analytical design and ANSYS Fluent CFD validation of a three-ramp mixed-compression supersonic intake at Mach 4.0 and 15,000 m altitude.
img: assets/img/mach_contour.png
importance: 1
category: work
related_publications: false
---

At Mach 4, you can't just ram air into an engine and hope for the best. The intake has to do something clever first - a series of oblique shocks that progressively squeeze the flow down from supersonic to subsonic before it even reaches the combustor. Do it badly and you lose most of your total pressure before combustion ever starts. Do it well and you've got an efficient, controllable compression system that keeps the engine alive.

This project covers the full design and CFD validation of a three-ramp mixed-compression intake designed at an altitude of **15,000 m** (or **50,000 ft**) at **Mach 4.0**.

---

## The Design Problem

A single normal shock at Mach 4 gives you a Total Pressure Recovery (TPR) of about **0.14** - meaning you've thrown away 86% of the energy in your incoming flow before the engine sees it. That's essentially useless.

The fix is to use oblique shocks instead. Each ramp deflects the flow slightly, producing a weaker shock with much lower losses. Chain enough of them together and you can arrive at a subsonic Aerodynamic Interface Plane (AIP) with dramatically better pressure recovery.

The challenge is picking the right ramp angles. Too shallow and you're not compressing efficiently. Too steep and the shock detaches and thus, the whole compression system falls apart.

---

## Analytical Design

I ran a parametric sweep in **MATLAB** using the θ-β-M (theta-beta-Mach) oblique shock relations, solving iteratively for each ramp in the compression train. For Mach 4.0, the shock detachment limit on the third ramp sits at **39°**, so the optimisation finds the angle combination that maximises TPR while keeping a margin below that limit.

The optimal three-ramp configuration:

| Ramp | Deflection θ | Shock Angle β | Downstream Mach |
|------|-------------|---------------|-----------------|
| 1    | 10.0°       | 22.23°        | 3.288           |
| 2    | 22.5°       | 27.86°        | 2.599           |
| 3    | 38.0°       | 36.36°        | —               |
| Terminal (normal) | — | 90.0° | subsonic |

**Analytical TPR: 0.587** —> compared to 0.14 for a single normal shock. That's a significant improvement just from a change in intake geometry.

The intake geometry was built in **SolidWorks** from sequential shock intersection coordinates derived in MATLAB, realistic throat height of ~59.7 mm.

---

## CFD Simulation

The 2D geometry was imported into **ANSYS Fluent 2026 R1** and simulated with:

- **Density-based solver** (appropriate for compressible, high-speed flows)
- **k-ω SST turbulence model** — the standard choice for aerospace CFD, particularly good near adverse pressure gradient boundary layers on the ramp surfaces
- **Roe-FDS flux scheme** for accurate shock capturing
- Courant number of 0.5 for stability
- Pressure far-field boundaries at M=4.0, P=12,112 Pa, T=216.65 K (ISA at 15,000 m)

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mach_contour.png" title="Mach number contour" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/static_pressure.png" title="Static pressure contour" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: Mach number contour showing three distinct oblique shocks and the terminal normal shock near the cowl lip. Right: Static pressure distribution — the stepwise pressure rise across each shock is clearly visible.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/total_pressure.png" title="Total pressure contour" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/wall_pressure2.png" title="Wall pressure distribution" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: Total pressure contour, loss concentrated in the shock structures and the turbulent boundary layer on the ramp surfaces. Right: Static Pressure (Pa) along the ramps, with step increases at the three shock impingement locations.
</div>

---

## Results

| Metric | Analytical | CFD | Difference |
|--------|-----------|-----|------------|
| Freestream P₀ (Pa) | 1,839,000 | 1,843,087 | +0.2% |
| AIP P₀ (Pa) | 1,079,433 | 1,045,858 | — |
| **TPR** | **0.587** | **0.568** | **−3.2%** |

The CFD result comes in 3.2% below the analytical prediction, which is unnervingly close. The oblique shock theory is inviscid; it doesn't account for the turbulent boundary layer building up along the ramp surfaces. The total pressure contour makes this clear — there's a distinct low-P₀ wake clinging to the walls, which is the viscous loss the theory can't see.

The shock positions in the Mach contour land close to where MATLAB predicted them, which is a nice spatial validation of the whole workflow from analytical design through to SolidWorks geometry to CFD mesh. However, this can be improved in future by extendng the cowl to catch the intersection of these shocks and hopefully, provide a slightly more efficient intake. Following on from that, the introduction of bleed air channels to remove low pressure, viscous, "dirty" air could increase the efficiency once more. I plan to incorporate these changes soon!

---

## What's Next

A **mesh independence study** is planned at ~500,000 cells (currently constrained to 512k under the ANSYS Student Edition). After that, the natural follow-on is a **transient simulation** to investigate buzz instability — the self-excited shock oscillation that can occur at off-design conditions, which is a critical operability concern for ramjet intakes.

---

**Tools:** MATLAB · SolidWorks · ANSYS Fluent 2026 R1 · k-ω SST · Roe-FDS

---
**Disclaimer:**
Portions of this work were completed with the assistance of Claude (Anthropic), an AI language model. AI tools were used to support tasks including document reformatting, code debugging, and write-up drafting. All technical decisions; including intake geometry, ramp angle selection, CFD setup, boundary conditions, and interpretation of results, were made independently by myself. All AI assisted or generated content was reviewed, verified, and edited for technical accuracy prior to inclusion.
