# Signals, Spectrum Analysis & Analog Communications

### ECE 318 — Communication Systems Laboratory

**Course:** ECE 318 — University of Waterloo  
**Tools:** Oscilloscope, FFT, spectrum analysis, function generators, Emona TIMS communications hardware  
**Topics:** Fourier analysis, filtering, VCOs, DSB-SC, DSB-LC, SSB-SC, quadrature multiplexing

> **Investigated signals in both the time and frequency domains using laboratory instrumentation, progressing from sinusoidal/Fourier analysis and filtering to practical amplitude-modulation and coherent-demodulation systems.**

---

# Overview

The ECE 318 laboratory sequence explored how communication-system concepts appear on real instrumentation.

Across the experiments, I used oscilloscopes, FFT measurements, signal generators, filters, multipliers, phase shifters, and communications-training hardware to investigate:

- Time- and frequency-domain signal representation
- Sinusoidal addition and multiplication
- Fourier-spectrum measurements
- VCO behavior and frequency response
- Triangle, pulse, and square-wave spectra
- Band-pass and low-pass filtering
- Harmonic reconstruction
- Nonlinear signal processing
- DSB-SC and DSB-LC amplitude modulation
- Coherent and envelope demodulation
- Single-sideband modulation
- Quadrature multiplexing

The experiments connected mathematical signal representations with directly measured waveforms and spectra.

---

# Time & Frequency Domain Measurements

The first laboratory focused on measuring basic signals in both domains.

A sinusoid was observed directly on an oscilloscope and then through its FFT representation.

Frequency-domain amplitudes were represented in **dBV**, using:

**dBV = 20 log<sub>10</sub>(V<sub>RMS</sub> / 1 V)**

where:

**0 dBV ↔ 1 V RMS**

This provided experience moving between oscilloscope amplitude measurements and frequency-domain magnitude measurements.

---

![Single sinusoid time and frequency domain](figures/ece318_sinusoid_fft.png)

*Figure 1 — Representative sinusoidal signal viewed in the time domain and through the oscilloscope FFT.*

---

# Linearity & Frequency Translation

The Fourier linearity property was investigated by summing two sinusoids at different frequencies and observing the resulting spectrum.

The combined waveform contained both original frequency components.

Signal multiplication was then examined experimentally.

Multiplying two sinusoidal signals produced spectral components at:

**f<sub>sum</sub> = f<sub>1</sub> + f<sub>2</sub>**

and

**f<sub>difference</sub> = |f<sub>1</sub> − f<sub>2</sub>|**

The experiment provided a physical demonstration of the frequency-translation operation that later becomes central to modulation and demodulation.

---

# Speech & Real Signals

A recorded speech waveform was observed in both the time and frequency domains.

Unlike a simple sinusoid, speech contains many simultaneously varying frequency components.

This experiment provided an early example of why the frequency-domain representation can reveal information that is difficult to interpret from the time waveform alone.

---

# Voltage-Controlled Oscillator

The operation of a VCO was also characterized experimentally.

Its output frequency followed the relationship:

**f = f<sub>0</sub> + K V<sub>in</sub>**

where the applied control voltage changes the oscillator frequency.

The VCO was then used with the oscilloscope's X-Y mode to sweep a device under test and produce a magnitude-versus-frequency response.

This provided a practical method of observing frequency response without manually stepping the source through individual frequencies.

---

# Signal Spectra & Fourier Series

The second laboratory focused more deeply on spectral analysis.

A triangle wave was measured in both the time and frequency domains, and its harmonic structure was compared with the expected Fourier-series behavior.

The experiment then investigated Fourier-transform duality and periodic pulse signals.

For a periodic pulse train, the discrete harmonic spectrum followed an overall envelope related to the pulse width.

Changing the duty cycle changed the distribution of energy across the spectrum.

---

# Square-Wave Harmonics

A square wave was measured in both domains.

Its spectrum contained a sequence of odd harmonics.

The experiment then reconstructed the waveform progressively by allowing additional harmonics through a low-pass filter.

The progression was approximately:

**fundamental only → sinusoid**

**1st + 3rd harmonic → closer to square wave**

**1st + 3rd + 5th → sharper transitions**

**additional odd harmonics → increasingly accurate square-wave reconstruction**

This provided a particularly clear physical demonstration that waveform shape in the time domain is determined by its frequency components.

---

![Square-wave harmonic reconstruction](figures/ece318_harmonic_reconstruction.png)

*Figure 2 — Time- and frequency-domain measurements while progressively reconstructing a square wave from additional odd harmonics.*

---

# Band-Pass Filtering

A square wave was passed through a band-pass filter and observed in both domains.

Because the original waveform contains many harmonics, the filter selectively passed the components located near its passband while attenuating the others.

The resulting time-domain waveform changed significantly because much of the original harmonic content had been removed.

This experiment made the relationship between:

**filter frequency response**

and

**time-domain waveform distortion**

directly visible.

---

# Nonlinear Signal Processing

A nonlinear rectifier was also used to process a sinusoidal input.

Unlike linear filtering, nonlinear processing introduced additional frequency components that were not present in the original sinusoid.

The resulting waveform and spectrum were observed experimentally.

---

# Amplitude Modulation

The third laboratory applied the previous signal-processing concepts to communication systems.

The modulation techniques investigated included:

- Double-Sideband Suppressed Carrier (**DSB-SC**)
- Double-Sideband Large Carrier (**DSB-LC**)
- Single-Sideband Suppressed Carrier (**SSB-SC**)
- Quadrature multiplexing / **QAM**

---

# DSB-SC & Coherent Demodulation

A message signal was multiplied by a carrier to produce a DSB-SC signal.

The modulated waveform was observed in both the time and frequency domains.

At the receiver, coherent demodulation required multiplying the received signal by a locally generated carrier and low-pass filtering the result.

The carrier phase was adjusted experimentally to maximize recovery of the original message.

The signal path was:

**Message → multiplier + carrier → DSB-SC → coherent multiplier → LPF → recovered message**

This demonstrated the importance of carrier synchronization in coherent receivers.

---

![DSB-SC modulation and demodulation](figures/ece318_dsb_sc.png)

*Figure 3 — DSB-SC signal and spectrum together with the coherently recovered message waveform.*

---

# DSB-LC & Envelope Detection

A DC offset was then introduced before modulation so that a carrier component remained in the transmitted waveform.

This created a **DSB-LC / conventional AM** signal.

Unlike DSB-SC, the retained carrier allowed the message to be recovered using an envelope detector.

The experiment compared the time- and frequency-domain behavior of the transmitted signal and examined how the carrier level affected modulation.

A simple AM receiver was also configured to frequency-shift a broadcast AM signal into the operating range of a **100 kHz band-pass filter** before demodulation.

---

# Single-Sideband Modulation

The laboratory then investigated **SSB-SC**.

Phase-shift networks and carrier adjustment were used to suppress one of the two sidebands.

The suppression could be observed directly in the frequency-domain measurement while the corresponding time-domain waveform changed.

The remaining sideband was then coherently demodulated to recover the original message.

SSB demonstrated how the same information can be transmitted while using less occupied spectrum than a double-sideband signal.

---

![SSB spectrum](figures/ece318_ssb.png)

*Figure 4 — Single-sideband suppressed-carrier measurement showing one sideband attenuated in the measured spectrum.*

---

# Quadrature Multiplexing

The final experiment transmitted two independent message signals using carriers of the same frequency but separated in phase.

The transmitted signal consisted of two DSB-SC signals using orthogonal carriers.

Conceptually:

**m<sub>1</sub>(t) → in-phase carrier**

**m<sub>2</sub>(t) → quadrature carrier**

The two modulated paths were summed for transmission and separated again at the receiver using phase-aligned coherent demodulators and low-pass filters.

Phase adjustment was critical to separating the recovered channels.

The experiment also showed how low-pass filtering affects a non-sinusoidal recovered message: removing higher-order harmonics caused the recovered square wave to become smoother and less ideal.

---

![Quadrature multiplexing](figures/ece318_quadrature.png)

*Figure 5 — Recovered message channels from the quadrature multiplexing experiment.*

---

# What I Learned

The strongest takeaway from these laboratories was seeing that the time and frequency domains are two views of the same physical signal.

A change that appears simple in one domain can have a very different interpretation in the other:

**Adding sinusoids**

→ multiple spectral components

**Multiplying signals**

→ frequency translation

**Removing harmonics**

→ time-domain waveform distortion

**Nonlinear processing**

→ new spectral components

**Changing carrier phase**

→ changes coherent-demodulator output

These experiments also provided practical experience using laboratory instrumentation to debug communication systems rather than relying solely on equations or simulation.

---

# Skills Demonstrated

**Oscilloscope measurements • FFT / spectrum analysis • Fourier interpretation • Function generators • Filtering • VCO characterization • Frequency response • AM modulation/demodulation • DSB-SC • DSB-LC • SSB-SC • Quadrature multiplexing • Communications-system debugging**