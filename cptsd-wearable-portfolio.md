# Neuromorphic Wearable Stress Detector — SNN + Analog In-Memory Compute (Phase 1)

**Project:** CPTSD/stress-detection wearable — hybrid SNN + analog in-memory-compute (AIMC) accelerator
**Status:** Phase 1 of a 12-week roadmap (algorithm layer + single synapse-cell proof of concept)
**Tools:** Python, snnTorch, NeuroKit2, Xschem, ngspice, SKY130 PDK
**Inspiration:** IBM HERMES (analog PCM in-memory compute) and IBM NorthPole (digital compute-in-memory)

## Architecture

*[Your architecture explanation goes here — block diagram, how the algorithm layer and analog synapse cell fit into the larger 12-week roadmap, and how this compares structurally to HERMES/NorthPole.]*

## Problem

Real-time stress/CPTSD-relevant physiological monitoring needs to run on a wearable's tight power budget — the kind of budget conventional digital ML inference struggles to hit. Analog in-memory compute (AIMC), where a trained neural network's weights are physically represented as device conductances and the multiply-accumulate operation happens directly in the analog domain via Ohm's law and Kirchhoff's current law, is the architecture real accelerators like IBM's HERMES chip use to hit orders-of-magnitude better energy efficiency than digital inference. This project builds a small-scale, honest proof-of-concept of that same mechanism: a spiking neural network trained on real physiological stress data, with its weights physically mapped onto a simulated analog synapse cell.

## Approach

**Algorithm layer.** WESAD chest-sensor data (ECG + EDA) was preprocessed and used to train a LIF-based spiking neural network in snnTorch, with weights fake-quantized to 4-bit precision during training (a straight-through estimator keeps gradients flowing through the quantization step) so the exported weights are already snapped to the discrete levels the analog hardware will need to represent.

Getting a working, non-degenerate model took real iteration: an initial pass on raw rate-coded ECG/EDA amplitude produced a network that either never fired (dead network, loss frozen at exactly ln(2)) or fired constantly (saturated, equally uninformative) depending on weight initialization and LIF threshold — both diagnosed directly from firing-rate telemetry rather than guessed at. Once the network was reliably alive, raw amplitude still proved too weak a signal to classify well, which led to replacing it with physiologically meaningful features via NeuroKit2: instantaneous heart rate (from R-peak/RR-interval detection) in place of raw ECG, and the phasic (fast, sympathetic-response) component of EDA in place of raw skin conductance.

**Synapse cell design.** Rather than attempting HERMES's 256×256 PCM crossbar, this project scopes down to the essential primitive: a single NMOS device biased in the triode/linear region, where drain current is proportional to V_DS at a given V_GS — literally Ohm's law with a settable conductance, since V_GS controls the channel conductance. An early attempt at this characterization surfaced a real device-physics issue: at minimum channel length (L=0.15µm), the device showed substantial current even at V_GS=0, growing with V_DS — a classic short-channel DIBL (drain-induced barrier lowering) signature, not a wiring bug. Moving to L=1µm (consistent with the long-channel assumption used successfully in an earlier bandgap-reference project) resolved it, confirming true off-state behavior and a clean triode region.

With the device confirmed in triode, conductance was characterized directly (not from a theoretical square-law model, since SKY130's BSIM4 device has no single clean K_n — it's a 63-bin process model) across 5 gate bias points (V_GS = 0.8–1.2V), giving a real, measured conductance-vs-V_GS lookup table.

**Weight-to-hardware bridge.** Real trained weights from the SNN were mapped onto this measured LUT: each weight's magnitude scaled onto the measured conductance range and interpolated to find the exact V_GS needed to physically realize it, with sign handled via the standard differential (G⁺/G⁻) scheme used in real PCM-based accelerators to represent negative weights with physically-positive-only conductances.

**MAC validation.** Two synapse cells, sharing a common output node (a two-element crossbar column), were simulated with real weight-derived V_GS values and a common input voltage. The summed current was compared against a hand calculation using the same measured conductances.

## Results

- Working SNN trained on real WESAD ECG/EDA data via physiologically-informed feature extraction (heart rate, phasic EDA)
- Best validation accuracy: 60.6% (4 subjects), trailing the ~65% majority-class baseline — an honest result flagged as an open question (data ceiling vs. further tuning) rather than concealed
- Diagnosed and resolved a "dead network" and a "saturated network" training failure mode via direct firing-rate telemetry, and a short-channel DIBL circuit artifact via device-physics reasoning back to first principles
- Measured a real 5-point conductance-vs-V_GS characterization on a SKY130 NMOS device in the triode region
- Built a complete, working weight → conductance → V_GS mapping for real trained SNN weights
- Validated a real (simulated) 2-cell analog MAC circuit: -31.15µA measured vs. 37.88µA hand-calculated from independently measured device conductances — a ~18% difference attributed to real device nonlinearity beyond the two-point linear approximation used for the LUT, not a mechanism failure

## What I'd do differently / open work

- The SNN's accuracy ceiling with only 4 of WESAD's 15 subjects is unresolved — more subjects is the obvious next experiment
- The LIF neuron's leak/threshold/reset dynamics (the cap + comparator circuit) were scoped out of Phase 1, following the same analog-MVM/digital-nonlinearity split IBM's NorthPole and HERMES both use (in-memory compute for the linear operation, separate circuitry for the nonlinearity) — a real next step, not an oversight
- The readout in this project is direct current-domain simulation, versus HERMES's GHz-speed CCO-based ADCs — appropriate for proof-of-concept scale, and a deliberate scoping decision rather than a limitation I wasn't aware of
- Scaling from a 2-cell proof-of-concept to a small (4×4 or 8×8) crossbar array is the natural next phase, along with a denser conductance LUT (the current 5-point linear interpolation is a simplification of real, slightly nonlinear device behavior)