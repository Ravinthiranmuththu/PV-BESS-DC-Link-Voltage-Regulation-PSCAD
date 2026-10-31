# PV–BESS DC-Link Voltage Regulation | PSCAD/EMTDC

**Preliminary electromagnetic-transient simulation and feedback-control study** using PSCAD Free 5.1 (64-bit).

![PSCAD simulation and output plots](docs/images/pscad-run-results.png)

## Overview

This project models a simplified photovoltaic (PV) source and battery energy storage system (BESS) connected to a common DC bus. A programmed PV-source disturbance occurs at **t = 2 s**. The BESS is represented by an **ideal, externally controlled DC voltage source**, with voltage feedback used to adjust its command. This is a proof-of-concept DC-bus control model, **not** a switching converter, electrochemical battery, or validated grid-connected PV/BESS installation.

## Model and simulation settings

| Item | Setting in supplied PSCAD project |
|---|---|
| Simulation software | PSCAD Free 5.1 / EMTDC |
| Fortran compiler used for local build | GFortran 13.2 (user's Windows installation) |
| Simulation duration | 10 s |
| Simulation time step | 25 µs |
| Plot step | 50 µs |
| Nominal PV source voltage | 0.78 kV |
| Programmed PV command change | -0.18 kV at 2 s (0.78 → 0.60 kV) |
| Nominal BESS equivalent source | 0.75 kV |
| DC-link capacitor | 1000 µF |
| DC load | 100 Ω |
| BESS feedback proportional gain | 4.0 |
| BESS control reference | 0.75 kV |

The PV source is a simplified voltage-source equivalent, not a physics-based irradiance-to-power PV array. Source impedance, sign conventions, and controlled-source settings can affect measured response.

## Control concept

The BESS control circuit reads the measured DC-bus voltage (`V_DC`) as a **signal**, compares it with a 0.75 kV reference, multiplies the error by a proportional gain, and combines that correction with a 0.75 kV nominal BESS command. The resulting signal drives the BESS source's external-voltage control terminal.

Conceptual command: `V_BESS_cmd = 0.75 kV + 4.0 × (0.75 kV − V_DC)`.

This expression describes the intended feedback structure; its numerical behavior depends on the PSCAD component wiring and solver settings.

## Observed simulation

The supplied screenshot shows an approximately flat DC-bus voltage near **0.75 kV**, while PV and BESS currents change around **t = 2 s**. The user's PSCAD session displayed **0 errors**, **0 warnings**, and **EMTDC run completed** for the repaired model. These observations are preliminary; they do not establish controller superiority, battery state of charge, energy efficiency, or closed-loop stability margins.

## How to run

1. Install PSCAD Free 5.1 (64-bit) and a compatible Fortran compiler. The original project was built with GFortran 13.2 on Windows.
2. Open `model/PV_BESS_S3_Control_Repaired.pscx` in PSCAD.
3. Select the project and click **Build**, then **Run**.
4. Inspect `V_DC`, `I_PV`, and `I_BESS` output plots, especially around **2 s**.
5. If graphs appear empty, verify that the project has run and use the graph's zoom-to-fit / autoscale controls.

## Repository structure

```text
model/PV_BESS_S3_Control_Repaired.pscx  PSCAD project
 docs/images/pscad-run-results.png     Screenshot of successful simulation
README.md                              Project documentation
.gitignore                             Excludes generated build/output artifacts
```

## Limitations and planned work

- The BESS is an ideal controlled voltage source; no battery SOC, current limit, bidirectional converter, or switching losses are modeled.
- No grid-side AC inverter or fault-ride-through assessment is included in this version.
- Closed-loop vs fixed-voltage BESS comparison, disturbance recovery metrics, and parameter sensitivity remain future work.
- The screenshot is evidence of one completed run, not a formal validation dataset.

## Status

**v0.1 — Working preliminary model.** Suitable as an educational and portfolio demonstration; not for equipment sizing or protection design.
