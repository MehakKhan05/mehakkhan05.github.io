# Pipelined Matrix-Vector Multiplication Engine (SystemVerilog / FPGA)

**Course:** ECE 327 — Digital Hardware Systems, University of Waterloo
**Team:** Mehak Khan & Hiya Patel
**Tools:** SystemVerilog, AMD Vivado/Vitis, Kria KV260 (Zynq UltraScale+ MPSoC)

## Problem

Build a hardware matrix-vector multiplication (MVM) engine — the same class of
datapath used in real deep learning accelerators like Microsoft's BrainWave —
that meets 350MHz+ timing closure on an FPGA, and scales to matrices and vectors
of arbitrary size. The engine had to be built from three components designed
from scratch: a fully pipelined 8-lane dot product unit, an accumulator, and a
finite-state-machine controller sequencing memory reads and control signals
across an arbitrary number of parallel output lanes (`NUM_OLANES`).

## Approach

**Dot product unit.** An 8-element dot product breaks naturally into a tree:
8 parallel multipliers feeding a 3-level binary adder reduction (4→2→1). To hit
high frequency, every stage of that tree — inputs, each multiply, each level of
addition — needed its own pipeline register, so a single dot product op takes
5 cycles to flow through but the unit can accept a new pair of vectors every
cycle. Getting the bit-width bookkeeping right through each stage (multiply
output width, then adder tree growth) was most of the actual work here.

**Accumulator + controller.** The accumulator sums partial dot products across
a matrix row using `first`/`last` control bits to mark accumulation boundaries.
The controller is a 2-state FSM (`IDLE`/`COMPUTE`) that generates the read
address sequence into the vector and matrix memories and the corresponding
`accum_first`/`accum_last`/`ovalid` signals, matching the round-robin data
layout across `NUM_OLANES` matrix memories specified by the lab.

**Timing closure.** With an artificially aggressive 1GHz constraint pushing
Vivado to optimize as hard as possible, the critical path ran through DSP
blocks and carry-chain adders. Beyond registering every stage of the datapath,
the highest-leverage optimization was explicitly targeting the DSP48E2 hard
block's *internal* pipeline registers (the A/B input register, the M register
after the multiplier array, and the P register after the post-adder) instead
of relying on separate fabric flip-flops — folding pipeline stages into the
DSP primitive itself cut total pipeline depth while keeping frequency high.
This technique, first proven out on the standalone tanh circuit (Lab 3, where
it pushed timing from 34MHz unpipelined to 574MHz), carried directly into the
MVM engine's dot product datapath.

**Scaling for throughput.** For the bonus component, we set `NUM_OLANES=64`,
mapping as many parallel output lanes as the FPGA fabric would support to
maximize operations/cycle at the achieved clock frequency.

## Results

- Fully functional MVM engine, verified against the provided golden-result
  testbench for arbitrary matrix/vector dimensions
- 64 output lanes (`NUM_OLANES=64`), earning the lab's throughput bonus
- Achieved clock frequency in the ~500–600MHz range, well above the 350MHz
  requirement
- Resource utilization: ~5% LUT, ~1% LUTRAM, ~1% FF, ~6% BRAM on the Kria
  KV260 — confirming the design scaled efficiently rather than just brute-forcing
  lane count
- Deployed and demoed on real FPGA hardware (Kria KV260 board)

## What I'd do differently

Debugging the controller against the MVM top-level was the hardest part of
integration — a single mismatched wire name between `ctrl.sv` and `mvm.sv`
silently created a floating wire rather than throwing an error, and the
resulting bug (output going off-track partway through a computation) took
real waveform tracing to catch. Next time I'd build a lightweight
sanity-check testbench for the controller in isolation before wiring it into
the top-level module, rather than debugging address sequencing and
integration errors simultaneously.