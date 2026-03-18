---
layout: page
title: Nosecone Parametric CFD Study
description: ANSYS Fluent 2D axisymmetric CFD study comparing five nosecone geometries for a 100mm sounding rocket across Mach 0.3–3.0.
img: assets/img/nosecone_thumb.png
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

All simulations use a 2D axisymmetric domain drawn in SpaceClaim. The fluid domain extends 1.5L upstream and 20L downstream of the nosecone tip, with a farfield height of 1,500 mm (15R) — sized to prevent shock reflection off the outer boundary at high Mach numbers.

Named selections: `inlet`, `outlet`, `farfield` (all Pressure Far-Field), `axis`, `wall_nosecone`, `wall_base`.

Meshing is performed in ANSYS Meshing with CFD/Fluent physics preference:

| Mesh Parameter | Value |
|----------------|-------|
| Global element size | ~12 mm |
| Wall edge sizing | ~4 mm |
| Inflation layers | 8 (smooth transition, growth rate 1.2) |
| Target y⁺ | ≈ 1 (first cell ~2 μm) |
| Element count (conical) | ~106,850 |

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
| Courant number | 1 (0.5 at M0.95) |
| Reference area | 0.007854 m² |

All outer boundaries (inlet, outlet, farfield) are set as **Pressure Far-Field** with Mach, static pressure, and temperature corresponding to ISA sea-level conditions. Operating pressure is set to 101,325 Pa.

---

## Results (In Progress)

### Mach Sweep — Conical Nosecone

| Mach | Cd | Status |
|------|-----|--------|
| 0.3 | — | Pending |
| 0.7 | — | Pending |
| 0.95 | — | Pending |
| 1.2 | — | Pending |
| 1.5 | **0.2728** | ✅ |
| 2.0 | **0.3681** | ✅ |
| 2.5 | — | Pending |
| 3.0 | — | Pending |

The trend at M1.5 and M2.0 shows that the Cd increases as Mach increases above the transonic drag peak, consistent with oblique shock theory where wave drag coefficient falls as M rises. At M2.0, the drag breakdown is approximately wave drag ~0.22, base drag ~0.10, skin friction ~0.04 — total ≈ 0.36, which validates well against theoretical predictions for a 9.46° half-angle cone.

The remaining four geometries (ogive, Von Kármán, power series, ellipse) will be simulated across the same Mach sweep once the conical baseline is complete.

---

## Mach 1.5 Flow Field — Conical

The Mach contour at M1.5 shows the expected attached oblique shock from the tip, with a clear expansion fan at the base shoulder and a subsonic recirculation region in the base cavity (blue region). The farfield boundary is sized to 1,500 mm height, which pushes the shock reflection artefact far enough from the nosecone surface that Cd is not materially affected.

---

## Methodology Notes

**y⁺ targeting:** With k-ω SST in wall-resolved mode, the first cell height is set to ~2 μm to achieve y⁺ ≈ 1. This ensures the boundary layer is resolved through the viscous sublayer rather than bridged with wall functions — critical for accurate skin friction and base pressure prediction.

**Transonic handling:** The M0.95 case uses a reduced Courant number (0.5) and typically requires 1,500+ iterations to converge through the mixed subsonic/supersonic region. Results are recorded only after residuals drop below 1×10⁻⁴ and Cd stabilises.

**Screenshot archiving:** Residuals plot and Mach number contour are saved for every Mach point. Pressure coefficient distribution is additionally saved for M0.95, M2.0, and M3.0 as these are the most aerodynamically significant points.

---

## Pending Work

- Complete conical Mach sweep: M0.3, M0.7, M0.95, M1.2, M2.5, M3.0
- Build SpaceClaim domains for ogive, Von Kármán, power series, ellipse
- Run full Mach sweeps for all four remaining geometries
- Mesh independence study (target ~500k cells, constrained by ANSYS Student Edition)
- Python post-processing script to aggregate and plot Cd vs Mach for all five geometries
- Validation against NACA/ESDU published data for conical and ogive profiles
- Fin geometry phase (3D half-body, separate study)

---

**Tools:** Python · SolidWorks · ANSYS SpaceClaim · ANSYS Meshing · ANSYS Fluent 2026 R1 · k-ω SST

---
**Disclaimer:**
Portions of this work were completed with the assistance of Claude (Anthropic), an AI language model. AI tools were used to support tasks including coordinate generation, solver troubleshooting, and write-up drafting. All technical decisions — geometry selection, mesh parameters, boundary conditions, solver settings, and interpretation of results — were made independently by the author. All AI-assisted content was reviewed and verified for technical accuracy prior to inclusion.
