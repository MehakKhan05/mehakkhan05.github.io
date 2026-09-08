# PDK-Agnostic Subthreshold Bandgap Voltage Reference (Analog IC Tapeout)

**Project:** UWASIC (University of Waterloo Analog & Systems IC Design Team)
**Team:** Mehak Khan, Zoey Situ — with design review from Henry Wu
**Target:** Tiny Tapeout submission (bandgap reference + PLL skeleton)
**Tools:** Xschem, ngspice, SKY130 PDK

*Status: active — Verilog-A model and PSRR tuning in progress.*

## Problem

Design a bandgap voltage reference — a circuit that produces a fixed output
voltage independent of temperature — using a purely MOSFET-based subthreshold
topology (no BJTs), so the design stays portable across PDKs rather than
depending on process-specific bipolar devices. The circuit needed to hold a
stable reference voltage across a wide temperature range (−40°C to 120°C) on
a real fabrication process (SKY130), as part of a larger Tiny Tapeout
submission that also includes a PLL.

## Approach

**The core mechanism.** A bandgap reference works by summing two voltages
that move in opposite directions with temperature — a CTAT term (decreases
with temperature) and a PTAT term (increases with temperature) — tuned so
they cancel. In a MOSFET-only subthreshold design, the CTAT term comes from
the gate-source voltage of a diode-connected subthreshold device, and the
PTAT term comes from the ΔVGS between two differently-sized subthreshold
MOSFETs — the direct analog of the BJT ΔVBE trick, since subthreshold MOSFET
conduction is diffusion-dominated (like a diode) rather than drift-dominated,
which is exactly why the same exponential relationship applies.

**Characterizing the process before designing.** Before sizing the actual
reference, I ran subthreshold characterization sweeps on SKY130 devices to
extract the real subthreshold slope factor (η ≈ 1.48, measured directly from
simulation rather than assumed) and the CTAT slope (∂VGS/∂T ≈ −0.63mV/°C).
Both numbers feed directly into the resistor ratio that sets how strongly the
PTAT term is weighted — get η wrong and the whole cancellation is off. I also
swept drain current to find the ceiling of valid subthreshold operation
(around 3–5nA for this device sizing) — past that point the MOSFET moves into
strong inversion, the I-V relationship stops being exponential, and the
whole PTAT design equation no longer describes the transistor's actual
behavior.

**Iterating on temperature stability.** The first working schematic showed
PTAT dominating (temperature dependence not cancelling), which I traced to
the resistor ratio R1+R3 over R0 being off — the initial fix (increasing R0
to reduce the PTAT contribution) didn't move the slope at all, which led to
removing a PMOS device (M7) from the topology entirely and re-deriving the
resistor values from the measured η and CTAT slope rather than the original
paper's process constants.

**Error amplifier (Zoey).** The reference relies on an error amplifier
forcing two internal branch currents to match. Zoey's characterization work
took the amplifier through DC operating-point verification (making sure
every device stayed in saturation, correcting a tail-current-source biasing
issue), then DC sweep and AC analysis — measuring ~41dB low-frequency gain,
a first pole around 200–500kHz, and unity-gain frequency around 10MHz.

## Results

- Measured VREF ≈ 630mV, with a temperature coefficient of **~89 ppm/°C**
  across a −40°C to 120°C sweep (ΔV ≈ 9mV total)
- PSRR: ~65dB at low frequency (1–10Hz), comfortably beating the >40dB spec
- Design methodology built on real SKY130 device characterization (gm/ID and
  subthreshold sweeps) rather than reused constants from the reference paper,
  making the topology genuinely portable to other PDKs

## Open work

- PSRR shows a +2dB resonance bump around 100kHz and degrades to −3dB above
  1MHz — under active investigation, with review feedback pointing at PMOS
  gm and the feedback capacitor as the levers to widen the error amplifier's
  bandwidth without sacrificing phase margin
- Writing a Verilog-A behavioral model of the reference for use in the larger
  Tiny Tapeout system-level simulation
- Verifying the design against the project's tile-based physical/thermal/
  reliability budget constraints (particularly thermal gradient across the
  tile, which directly affects matched-pair accuracy between the two core
  subthreshold devices)