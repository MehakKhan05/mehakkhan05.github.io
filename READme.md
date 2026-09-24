# Mehak Khan

Third-year Electrical Engineering (BASc Honours) at the University of Waterloo, graduating April 2028. President's Scholarship of Distinction. Focused on analog/mixed-signal IC design.

[LinkedIn](https://www.linkedin.com/in/mehakkhan05/) · [Email](mailto:m225khan@uwaterloo.ca) · [Resume](assets/Resume.pdf)

---

## About

I work across the stack from transistor-level analog IC design up through digital hardware and, most recently, into the ML side of hardware-software co-design. 

Currently targeting Winter 2027 co-ops in analog/mixed-signal design and verification.

## Skills & Tools

**Analog/IC Design:** Cadence Virtuoso, Xschem, ngspice, SKY130 PDK, subthreshold/analog circuit design, bandgap references, error amplifiers

**Digital Hardware:** SystemVerilog, Vivado, Vitis, FPGA timing closure and pipelining, FSM design

**PCB Design:** Altium, KiCad, EasyEDA, signal integrity, power management circuits

**Software/ML:** Python, PyTorch, snnTorch, NeuroKit2, hardware-aware quantization

**Industry:** IEC 61850, protection relay testing, UPS/power electronics validation, power quality analysis

---

## Featured Projects

### [Pipelined Matrix-Vector Multiplication Engine (SystemVerilog / FPGA)](./mvm-engine-portfolio.md)
An 8-lane pipelined MVM engine — the same class of datapath used in accelerators like Microsoft's BrainWave — scaled to 64 output lanes and timing-closed at ~500–600MHz on a Kria KV260 FPGA.

### [PDK-Agnostic Subthreshold Bandgap Voltage Reference (Analog IC Tapeout)](./uwasic-bandgap-portfolio.md)
*Active — final PSRR tuning and Verilog-A model in progress.*
A MOSFET-only (no BJT) subthreshold bandgap reference for a UWASIC Tiny Tapeout submission, built on real SKY130 device characterization. Measured 89 ppm/°C temperature coefficient and 65dB low-frequency PSRR.

### [Neuromorphic Stress-Responsive Wearable (Phase 1)](./cptsd-wearable-portfolio.md)
A spiking neural network trained on real WESAD physiological data, with trained weights physically mapped onto and validated against a simulated analog synapse cell in SKY130 — inspired by IBM's HERMES and NorthPole in-memory-compute accelerators.

### [Watolink — Main PCB for a Wearable EMG-Controlled Prosthetic Hand](./watolink.md)
*Active — board fabrication and impedance-mismatch fix in progress.*
2-layer main PCB (power management, ESP32-S3, sensor ADCs, servo control) for a wearable EMG biosignal control system, as Hardware Lead.

### [STM32 Reservoir Control System — Custom PCB & Embedded Firmware](./stm32-reservoir-control-system.md)

A custom 2-layer STM32F401RE control PCB integrating DC-motor drive, servo actuation, ultrasonic reservoir-level sensing, RPM measurement, ADC acquisition, RGB status indication, and external display interfaces. Developed timer- and interrupt-driven firmware using PWM, ADC, UART, input capture, and external interrupts, then assembled, soldered, and integrated the board with the physical system.

## Selected Experimental Work

### [Control Systems & Motor Characterization — ECE 380](./ece380-control-systems-labs.md)

Experimental characterization of open- and closed-loop systems, DC-motor dynamics, and P/PI/PD/PID controllers using measured bandwidth, transient response, steady-state error, overshoot, and settling time.

### [Signals, Spectrum Analysis & Analog Communications — ECE 318](./ece318-signals-communications-labs.md)

Hands-on signal and communications experiments spanning oscilloscope/FFT measurements, Fourier analysis, filtering, VCO characterization, DSB/SSB amplitude modulation, coherent demodulation, and quadrature multiplexing.

### [Analog Amplifier Characterization — ECE 340](./ece340-analog-electronics-labs.md)

Experimental and LTspice characterization of MOS common-source, common-drain, and differential amplifiers, including DC biasing, common- and differential-mode transfer characteristics, gain and transconductance extraction, input common-mode range, and comparison of measured device behavior against square-law MOS models.


---

## Coming Soon
- UWASIC Low-Power 64Gb/s PAM4 Transmitter in 65nm CMOS
- Watolink — fabrication results and validated impedance-buffering fix
- ECE 373 (Microwave Circuits) — HF PCB design
- ECE 320 (Computer Architecture) project
- SYDE 556 Neural Engineering Project