# Watolink — Main PCB for a Wearable EMG-Controlled Prosthetic Hand

**Project:** Watolink (MindFlex) — wearable EEG/EMG biosignal control system
**Role:** Hardware Lead
**Team:** Mehak Khan, Fiona, Aidan
**Tools:** EasyEDA, 2-layer PCB fabrication
**Status:** Board designed and ready for fabrication; data-acquisition issue diagnosed, fix in progress

## Architecture

The system splits into three physical modules: an **EMG Armband** (electrodes → instrumentation amplifier → hardware filtering/amplification → ADC, plus a vibrotactile feedback motor driven back to the user), a **Main PCB** built around an ESP32-S3 (handling power management, I2C communication with both ADCs, PWM generation for six servo channels, and analog force-sensor readback), and the **Hand** itself (six servo motors for wrist/finger actuation, three force-sensitive resistors for grip feedback). The ESP32-S3 is the central coordinator: it reads EMG intent and force feedback over I2C, and drives the hand's servos over PWM, closing the loop between muscle signal and physical actuation.

I led hardware design on the Main PCB: a 2-layer board handling USB-C input, battery charging (BQ24040), a 6V boost converter for servo power, a 3.3V LDO for logic, the ESP32-S3 microcontroller circuit, and the I2C-connected ADCs (ADS1115) for both servo feedback and force-sensor data.

## Problem

Two real engineering problems came up during this design, one in the schematic-to-fabrication path and one in bring-up testing.

**Design constraint:** the originally-specified battery charger IC (BQ2560) came in a wafer-scale chip package that was impractical to route on a 2-layer board — a real fabrication-vs-design tradeoff, not just a component swap. I changed the charger IC to the BQ24040, which has a routable package, without compromising the charging functionality the design needed.

**Bring-up issue:** once the board was assembled and data acquisition was tested, the EMG channel data came back inaccurate. The first proposed fix — adding more channels for redundancy — was ruled out on cost grounds, which forced a proper root-cause investigation rather than a brute-force workaround. The actual cause: electrode-tissue interface impedance mismatch between the differential EMG inputs, which degrades common-mode rejection and shows up as noisy, motion-artifact-prone data — a well-known failure mode in biopotential acquisition, not a wiring defect.

## Approach

Rather than accept the "more channels" fix, I traced the noise to its electrical root cause: differing source impedance at each electrode-skin interface degrading CMRR at the instrumentation amplifier stage. The fix under active development is **active buffering** — a unity-gain voltage-follower placed at each electrode before the signal reaches the main differential instrumentation amplifier, transforming each electrode's high and variable source impedance into a low, consistent output impedance before mismatch has a chance to matter, rather than a passive matching network (which is the right tool for RF/transmission-line problems, not electrode-tissue interfaces at biosignal frequencies).

## Results

- Full 2-layer main PCB designed and schematic-reviewed as a team, covering power management, MCU, sensor ADCs, and servo control
- Resolved a real fabrication constraint (wafer-chip charger IC) by re-selecting a routable part without a functional compromise
- Diagnosed a real data-acquisition failure to its correct root cause (electrode impedance mismatch / CMRR degradation) rather than accepting a more expensive workaround
- Board is at the ordering/fabrication stage

## Open work

- Fabricate and debug the ordered board
- Implement and validate the active-buffering fix at each electrode, and confirm improved data accuracy against the original noisy baseline