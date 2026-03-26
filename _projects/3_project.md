---
layout: page
title: Nosecone Parametric CFD Study
description: ANSYS Fluent 2D axisymmetric CFD study comparing five nosecone geometries for a 100mm sounding rocket across Mach 0.3–3.0.
img: assets/img/nosecone/conical/M0_95_staticpressure.png
importance: 3
category: work
related_publications: false
---
 
Drag is the enemy of every sounding rocket. At subsonic speeds it's friction and pressure drag; cross the transonic regime and wave drag dominates everything. The choice of nosecone geometry — conical, tangent ogive, Von Kármán, power series, elliptical — makes a measurable difference across the full flight envelope, but most teams pick a shape based on precedent rather than data. This study generates that data.
 
The goal is a systematic CFD comparison of five nosecone profiles on a common 100mm diameter, 3:1 fineness ratio body, swept across eight Mach numbers from deep subsonic to Mach 3.0. All five geometries, the same mesh methodology, the same solver settings, the same reference conditions — a controlled comparison.
 
---
 
## Vehicle & Reference Parameters
 
| Parameter | Value |
|-----------|-------|
| Body diameter | 100 mm |
| Nosecone length | 300 mm (L/D = 3) |
| Reference area | 7,854 mm² (π/4 × D²) |
| Motor | Cesaroni (15–20 kg vehicle class) |
| ISA conditions | P∞ = 101,325 Pa, T∞ = 288.15 K |
 
---
 
## Nosecone Geometries
 
Five profiles were modelled, covering the full design space from the simplest (conical) to the analytically drag-optimised (Von Kármán):
 
**Conical** — straight-sided, half-angle 9.46°. Sketched directly in SolidWorks. Strong oblique shock at the tip; highest wave drag supersonic but simple to manufacture.
 
**Tangent Ogive** — tangent arc construction, R_ogive = 925 mm. The most common amateur rocketry choice; smooth curvature transition to body diameter.
 
**Von Kármán (LD-Haack)** — analytically minimises wave drag for a given length and base diameter. Profile coordinates generated in Python from the Haack series and imported as a spline.
 
**Power Series n = 0.5** — blunted at the tip, maximum volume for a given length. Python-generated coordinates. Typically optimised for subsonic/transonic rather than high supersonic flight.
 
**Elliptical** — quarter-ellipse profile, constructed in SolidWorks using the ellipse tool and trim. Smooth but not analytically optimised.
 
Profile coordinates for the Von Kármán and Power Series geometries were generated in Python and imported into SolidWorks via the *Curve Through XYZ Points* tool (3-column X/Y/Z text format).
 
---
 
## CFD Setup
 
### Domain & Meshing
 
All simulations use a 2D axisymmetric domain drawn in SpaceClaim. The fluid domain extends 1.5L upstream and 20L downstream of the nosecone tip, with a farfield height of 1,500 mm (15R), sized to prevent shock reflection off the outer boundary at high Mach numbers.
 
Named selections: `inlet`, `outlet`, `farfield` (all Pressure Far-Field), `axis`, `wall_nosecone`, `wall_base`.
 
Meshing is performed in ANSYS Meshing with CFD/Fluent physics preference:
 
| Mesh Parameter | Value |
|----------------|-------|
| Global element size | 5 mm |
| Wall edge sizing (nosecone) | 0.5 mm |
| Wall edge sizing (base) | 2 mm |
| Inflation layers | 15 (first layer thickness 2×10⁻⁶ m, growth rate 1.2) |
| Capture curvature | Yes — 18° normal angle |
| Target y⁺ | ≈ 1 |
| Element count | ~314,000 |
| Node count | ~315,000 |
 
### Fluent Solver Settings
 
| Setting | Value |
|---------|-------|
| Solver | Density-based, Steady |
| Geometry | 2D Axisymmetric |
| Turbulence | k-ω SST |
| Energy | On |
| Density | Ideal gas |
| Viscosity | Sutherland |
| Flux scheme | Roe-FDS |
| Spatial discretisation | Second Order Upwind |
| Courant number | 5 (subsonic), 1 (supersonic), 0.5 (transonic) |
| Reference area | 0.007854 m² |
 
All outer boundaries (inlet, outlet, farfield) are set as **Pressure Far-Field** with Mach, static pressure, and temperature corresponding to ISA sea-level conditions. Turbulence is specified via intensity (5%) and viscosity ratio (10).
 
---
 
## Results — Conical Nosecone (Complete)
 
### Drag Coefficient vs Mach Number
 
| Mach | Regime | Cd | Cd Wave | Cd Base |
|------|--------|----|---------|---------|
| 0.3 | Subsonic | 0.0072 | 0.0065 | 0.0004 |
| 0.7 | Subsonic | 0.0411 | 0.0379 | 0.0006 |
| 0.95 | Transonic | 0.1228 | 0.0949 | 0.0225 |
| 1.2 | Supersonic | 0.2069 | 0.1491 | 0.0498 |
| 1.5 | Supersonic | 0.2776 | 0.2003 | 0.0637 |
| 2.0 | Supersonic | 0.3807 | 0.2624 | 0.0958 |
| 2.5 | Supersonic | 0.4692 | 0.3038 | 0.1334 |
| 3.0 | Supersonic | 0.5453 | 0.3234 | 0.1809 |
 
The conical nosecone shows a smooth monotonic Cd increase from 0.007 at Mach 0.3 to 0.545 at Mach 3.0. Wave drag dominates at all speeds, contributing roughly 60% of total drag throughout the supersonic range. Base drag grows in significance with Mach — from about 5% of total drag at Mach 0.3 to 33% at Mach 3.0 — reflecting the increasingly severe low-pressure recirculation behind the blunt base at higher speeds.
 
The transonic jump between Mach 0.7 and Mach 1.2 is the steepest gradient in the curve, with Cd increasing fivefold as the flow transitions through the sonic regime and wave drag switches on.
 
---
 
### Mach 0.3 — Subsonic Baseline
 
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M0_3_mach.png" title="Mach contour at M0.3" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M0_3_staticpressure.png" title="Static pressure at M0.3" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M0_3_drag.png" title="Cd convergence at M0.3" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Mach 0.3 — Cd = 0.0072. At deep subsonic speeds, the flow field is dominated by the stagnation point at the nose tip and the low-pressure base recirculation. No shocks are present. Drag is almost entirely pressure drag on the nosecone surface, with a negligible base contribution.
</div>
 
### Mach 0.95 — Transonic
 
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M0_95_mach.png" title="Mach contour at M0.95" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M0_95_staticpressure.png" title="Static pressure at M0.95" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M0_95_drag.png" title="Cd convergence at M0.95" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Mach 0.95 — Cd = 0.1228. The transonic regime marks the onset of wave drag. Local supersonic pockets form over the nosecone surface, terminated by weak shocks. The Cd convergence history shows the characteristic slow settling typical of transonic cases, requiring 2,500+ iterations at Courant 0.5.
</div>
 
### Mach 2.0 — Supersonic
 
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M2_0_mach.png" title="Mach contour at M2.0" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M2_0_staticpressure.png" title="Static pressure at M2.0" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M2_0_drag.png" title="Cd convergence at M2.0" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Mach 2.0 — Cd = 0.3807. A clean attached oblique shock is visible from the cone tip. The static pressure contour shows the sharp pressure rise across the shock and the low-pressure expansion at the base shoulder. Wave drag accounts for roughly 69% of total drag (Cd_wave = 0.262), with base drag contributing 25% (Cd_base = 0.096).
</div>
 
### Mach 3.0 — High Supersonic
 
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M3_0_mach.png" title="Mach contour at M3.0" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M3_0_staticpressure.png" title="Static pressure at M3.0" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/nosecone/conical/M3_0_drag.png" title="Cd convergence at M3.0" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Mach 3.0 — Cd = 0.5453. The oblique shock angle tightens compared to M2.0, hugging the cone surface more closely as expected at higher Mach. The base drag fraction reaches its highest at 33% of total drag (Cd_base = 0.181), driven by the severe low-pressure recirculation behind the blunt base. The drag convergence shows stable settling within 700 iterations.
</div>
 
---
 
## Methodology Notes
 
**y⁺ targeting:** With k-ω SST in wall-resolved mode, the first cell height is set to ~2 μm to achieve y⁺ ≈ 1. This ensures the boundary layer is resolved through the viscous sublayer rather than bridged with wall functions — critical for accurate skin friction and base pressure prediction.
 
**Transonic handling:** The Mach 0.95 case uses a reduced Courant number (0.5) and requires 2,500+ iterations to converge through the mixed subsonic/supersonic region. Results are recorded only after Cd stabilises to within ±0.005 over the final 200 iterations.
 
**Convergence criteria:** Simulations are considered converged when residuals drop below 1×10⁻³ (with most quantities reaching 1×10⁻⁴) and the monitored drag coefficient reaches a stable asymptote. For cases with mild oscillation in the base region (typical of steady RANS with separated flow), the final Cd is taken as the mean of the last 200 iterations.
 
---
 
## Remaining Work
 
- Run full Mach sweeps for tangent ogive, Von Kármán, power series, and elliptical geometries
- Resolve skin friction drag reporting (currently returning near-zero values due to a force report setup issue)
- Revisit subsonic cases (M0.3, M0.7) with pressure-based solver for improved convergence
- Mesh independence study at Mach 2.0 (constrained to 512k elements under ANSYS Student Edition)
- Python post-processing script to aggregate and plot Cd vs Mach for all five geometries
- Validation against published data for conical and ogive profiles
 
---
 
**Tools:** Python · SolidWorks · ANSYS SpaceClaim · ANSYS Meshing · ANSYS Fluent 2026 R1 · k-ω SST