# Analog Amplifier Characterization

### ECE 340 — Analog Electronics Laboratory

**Course:** ECE 340 — University of Waterloo  
**Tools:** LTspice, SCOPY, oscilloscope / lab instrumentation  
**Topics:** MOS amplifiers, DC biasing, common-source and common-drain stages, differential pairs, common-mode response, transconductance, bias-current dependence

> **Experimentally characterized MOS amplifier circuits and compared measured behavior against LTspice and analytical transistor models, including common-source, common-drain, and differential-amplifier configurations.**

---

## Overview

The ECE 340 laboratory sequence focused on connecting MOSFET circuit theory with both SPICE simulation and physical measurements.

The work included:

- LTspice simulation of common-source and common-drain amplifier stages
- DC operating-point analysis
- Input- and output-resistance investigation
- Differential-amplifier bias measurements
- Common-mode transfer characterization
- Differential-mode transfer characterization
- Differential gain extraction
- Transconductance extraction
- Input common-mode range measurement
- Bias-current sweeps
- Comparison of measured results with square-law MOS expectations

The main objective was to understand where simple transistor models accurately predict circuit behavior and where real-device measurements begin to deviate.

---

# LTspice Amplifier Analysis

I used LTspice to simulate MOS amplifier stages including:

- Common-source amplifier
- Common-drain / source-follower amplifier
- Input-resistance configurations
- Output-resistance configurations
- DC operating points

The LTspice operating-point solver was used to verify device bias conditions before comparing the circuit behavior against laboratory measurements.

The common-source and common-drain configurations provided contrasting amplifier characteristics:

**Common-source**

→ voltage amplification  
→ phase inversion  
→ relatively high voltage gain

**Common-drain**

→ approximately unity voltage gain  
→ high input resistance  
→ low output resistance  
→ useful as a voltage buffer

---

![LTspice common-source amplifier](figures/ece340_common_source_ltspice.png)

*Figure 1 — LTspice common-source amplifier model used for operating-point and small-signal analysis.*

---

![LTspice common-drain amplifier](figures/ece340_common_drain_ltspice.png)

*Figure 2 — LTspice common-drain / source-follower model used for operating-point and resistance analysis.*

---

# Differential Amplifier

The main experimental work characterized a MOS differential amplifier.

Measured DC bias conditions included approximately:

| Device | VGS | VDS |
| --- | ---: | ---: |
| M1 | 0.714 V | 1.60 V |
| M2 | 0.714 V | 1.60 V |
| MCM1 | 0.36 V | 0.36 V |

The measured drain currents of the differential pair were approximately:

**I<sub>D1</sub> ≈ 99.4 µA**

**I<sub>D2</sub> ≈ 99.4 µA**

The nearly equal drain currents are consistent with the balanced operating condition of the differential pair near zero differential input. :contentReference[oaicite:0]{index=0}

---

# Common-Mode Characterization

The common-mode response was measured by sweeping the common-mode input voltage and observing:

- Common-source node voltage
- Individual output voltage
- Differential output voltage

The experimentally determined input common-mode range was approximately:

**1.0 V ≤ V<sub>CM</sub> ≤ 2.47 V**

The measured common-mode gain from input to output common mode was approximately:

**A<sub>CM</sub> ≈ 0.051 V/V**

The gain from the common-mode input to the common-source node was approximately:

**A<sub>CMSRC</sub> ≈ 0.81 V/V**

These measurements demonstrated that the differential amplifier strongly attenuates common-mode variation at the output compared with the input variation. :contentReference[oaicite:1]{index=1}

---

![Differential amplifier common-mode sweep](figures/ece340_common_mode.png)

*Figure 3 — Measured common-mode transfer characteristic obtained using SCOPY.*

---

# Differential-Mode Response

The differential transfer characteristic was measured by sweeping:

**V<sub>ID</sub> = V<sub>INP</sub> − V<sub>INN</sub>**

and observing:

**V<sub>OD</sub> = V<sub>OUTP</sub> − V<sub>OUTN</sub>**

The measured response exhibited three main operating regions:

1. Negative saturation
2. Approximately linear amplification around zero differential input
3. Positive saturation

Near zero differential input, both MOSFETs share the tail current and operate in their active region.

As the magnitude of the differential input increases, current is progressively steered into one side of the pair until one transistor carries nearly all of the tail current.

The measured linear input range was approximately:

**−0.09 V ≤ V<sub>ID</sub> ≤ +0.09 V**

and the measured differential voltage gain was approximately:

**A<sub>DD</sub> ≈ 5.29 V/V** :contentReference[oaicite:2]{index=2}

---

![Differential amplifier transfer characteristic](figures/ece340_differential_transfer.png)

*Figure 4 — Measured differential transfer characteristic showing the linear amplification region around zero input and saturation at larger differential voltages.*

---

# Experimental Transconductance Extraction

The differential-amplifier transconductance was estimated from the measured voltage gain and load resistance.

Using:

**g<sub>m</sub> ≈ A<sub>DD</sub> / R<sub>L</sub>**

with:

**A<sub>DD</sub> = 5.29 V/V**

and:

**R<sub>L</sub> = 4.7 kΩ**

gives:

**g<sub>m</sub> ≈ 1.13 mS**

This provided a direct experimental estimate of transistor transconductance from the amplifier transfer characteristic rather than relying only on a MOSFET model. :contentReference[oaicite:3]{index=3}

---

# Effect of Bias Current

The differential amplifier was then characterized at multiple bias-current conditions:

| Bias Current | Load Resistance | Measured ADD | Estimated gm |
| ---: | ---: | ---: | ---: |
| 100 µA | 4.7 kΩ | 5.29 V/V | 1.13 mS |
| 200 µA | 2.35 kΩ | 3.88 V/V | 1.65 mS |
| 400 µA | 1.175 kΩ | 2.20 V/V | 1.87 mS |

The extracted transconductance increased with bias current.

This is qualitatively consistent with the strong-inversion square-law relationship:

**g<sub>m</sub> ∝ √I<sub>D</sub>**

However, the measured behavior increasingly deviated from the idealized model at higher current.

At **200 µA**, the measured:

**g<sub>m</sub> ≈ 1.65 mS**

was close to the square-law expectation of approximately:

**1.60 mS**

At **400 µA**, the measured:

**g<sub>m</sub> ≈ 1.87 mS**

was below the approximately:

**2.26 mS**

predicted by the normalized square-law trend. :contentReference[oaicite:4]{index=4} :contentReference[oaicite:5]{index=5}

---

![Measured and expected gm versus bias current](figures/ece340_gm_vs_bias.png)

*Figure 5 — Measured transconductance versus bias current compared with the square-law MOS prediction.*

---

# Model vs Measurement

One of the main lessons from the experiment was that simple MOS equations capture the overall device trend but do not perfectly describe the measured transistor behavior.

The idealized model predicts:

**g<sub>m</sub> ∝ √I<sub>D</sub>**

The measured data followed the same general trend, but the difference increased at higher bias current.

Possible causes discussed in the analysis include departure from ideal strong-inversion square-law operation and nonideal transistor behavior at larger overdrive.

This experiment reinforced an important analog-design principle:

> **Analytical models are extremely useful for understanding trends and initial design, but measured or model-based device behavior is needed for accurate circuit prediction.**

That same principle now carries into my transistor-level IC work, where device characterization and simulation are used instead of relying only on ideal square-law assumptions.

---

# What I Learned

The laboratory work helped connect transistor equations with real analog-circuit behavior.

The most important takeaways were:

- How MOS bias conditions determine amplifier operating region
- How common-source and common-drain stages differ in gain and impedance behavior
- How a differential pair steers current in response to differential input
- How common-mode and differential-mode behavior are measured separately
- How the linear input range of a differential amplifier is experimentally determined
- How voltage gain can be used to estimate transistor transconductance
- How bias current affects gm and differential-pair behavior
- Why measured device behavior increasingly differs from simple square-law predictions
- How LTspice and bench measurements complement analytical circuit models

---

# Skills Demonstrated

**Analog circuit analysis • MOSFET amplifiers • Differential pairs • LTspice • DC operating-point analysis • Common-mode characterization • Differential-mode characterization • Gain extraction • Transconductance extraction • Bias-current analysis • Model-versus-measurement validation • Laboratory instrumentation**