# Mehak Khan

Third-year Electrical Engineering (BASc Honours) at the University of Waterloo, graduating April 2028. President's Scholarship of Distinction. Focused on analog/mixed-signal IC design.

[LinkedIn](#) · [Email](#) · [Resume](#)

---

## About

I work across the stack from transistor-level analog IC design up through digital hardware and, most recently, into the ML side of hardware-software co-design. My projects lean toward proving things out from first principles — measured device characterization over textbook constants, real debugging over polished demos, and being upfront about what's finished versus still in progress.

Currently targeting Winter 2027 co-ops in analog/mixed-signal design and verification.

## Skills & Tools

**Analog/IC Design:** Cadence Virtuoso, Xschem, ngspice, SKY130 PDK, subthreshold/analog circuit design, bandgap references, error amplifiers

**Digital Hardware:** SystemVerilog, Vivado, Vitis, FPGA timing closure and pipelining, FSM design

**PCB Design:** Altium, KiCad, EasyEDA, signal integrity, power management circuits

**Software/ML:** Python, PyTorch, snnTorch, NeuroKit2, hardware-aware quantization

**Industry:** IEC 61850, protection relay testing, UPS/power electronics validation, power quality analysis

---

## Featured Projects

### [Pipelined Matrix-Vector Multiplication Engine (SystemVerilog / FPGA)](./ece327-mvm-engine-portfolio.md)
An 8-lane pipelined MVM engine — the same class of datapath used in accelerators like Microsoft's BrainWave — scaled to 64 output lanes and timing-closed at ~500–600MHz on a Kria KV260 FPGA.

### [PDK-Agnostic Subthreshold Bandgap Voltage Reference (Analog IC Tapeout)](./uwasic-bandgap-portfolio.md)
*Active — final PSRR tuning and Verilog-A model in progress.*
A MOSFET-only (no BJT) subthreshold bandgap reference for a UWASIC Tiny Tapeout submission, built on real SKY130 device characterization. Measured 89 ppm/°C temperature coefficient and 65dB low-frequency PSRR.

### [Neuromorphic Wearable Stress Detector — SNN + Analog In-Memory Compute (Phase 1)](./cptsd-wearable-portfolio.md)
A spiking neural network trained on real WESAD physiological data, with trained weights physically mapped onto and validated against a simulated analog synapse cell in SKY130 — inspired by IBM's HERMES and NorthPole in-memory-compute accelerators.

### [Watolink — Main PCB for a Wearable EMG-Controlled Prosthetic Hand](./watolink.md)
*Active — board fabrication and impedance-mismatch fix in progress.*
2-layer main PCB (power management, ESP32-S3, sensor ADCs, servo control) for a wearable EMG biosignal control system, as Hardware Lead.

---

## Coming Soon
- UWASIC bandgap reference — final PSRR results, Verilog-A model, hand-derivation writeup
- Watolink — fabrication results and validated impedance-buffering fix
- ECE 373 (Microwave Circuits) — HF PCB design
- ECE 320 (Computer Architecture) project