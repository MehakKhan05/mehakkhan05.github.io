# Neuromorphic Stress-Responsive Wearable
### Low-Power Physiological Stress Detection + Heartbeat-Like Haptic Feedback

**Project:** Neuromorphic stress-responsive wearable for physiological stress monitoring  
**Current Phase:** Phase 1 — quantized SNN + SKY130 analog-compute proof of concept  
**Long-Term Application:** A discreet wearable intended to detect sustained physiological stress and trigger a heartbeat-like haptic cue at the wrist  
**Tools:** Python, snnTorch, NeuroKit2, Xschem, ngspice, SKY130 PDK  
**Architecture Inspiration:** Neuromorphic and low-data-movement accelerators including IBM HERMES and NorthPole  

> **I am developing the inference architecture for a closed-loop wearable that detects sustained physiological stress locally and responds with a private heartbeat-like haptic stimulus at the wrist. Phase 1 focuses on the low-power neuromorphic inference engine: physiological feature extraction, a quantized spiking neural network, and mapping trained weights onto physically characterized SKY130 analog conductance cells.**

---

## Motivation

The long-term motivation for this project is a discreet wearable intended for children exposed to chronic or traumatic stress, including displaced and refugee children.

Periods of acute physiological arousal may not always be communicated verbally. A wearable capable of continuously observing physiological signals could potentially detect sustained changes associated with stress and provide a private tactile response without requiring the user to interact with a phone or cloud service.

The proposed intervention is a **gentle heartbeat-like haptic pattern at the wrist**.

The intended system is therefore not only a stress detector:

**Physiology → local inference → sustained stress detection → heartbeat-like haptic feedback**

The haptic response is currently a proposed intervention and has not been clinically validated as a treatment for CPTSD or stress.

Likewise, the current system detects physiological stress patterns rather than diagnosing CPTSD.

---

## Full System Architecture

The final wearable is planned as a closed-loop system with three major stages:

1. Physiological sensing
2. Neuromorphic stress inference
3. Haptic response

![Full wearable architecture](figures/wearable_system_architecture.png)

*Figure 1 — Proposed end-to-end architecture. ECG/PPG and EDA measurements are converted into physiological features and spike representations, processed by a low-power neuromorphic inference engine, and used to trigger a heartbeat-like haptic pattern when sustained stress is detected.*

The intended signal path is:

**ECG / PPG + EDA**  
↓  
**Analog front end + ADC**  
↓  
**Heart-rate / HRV + phasic EDA features**  
↓  
**Spike encoding**  
↓  
**Quantized spiking neural network**  
↓  
**Analog / neuromorphic vector-matrix multiplication**  
↓  
**Stress-state decision**  
↓  
**Persistence / confidence logic**  
↓  
**Haptic waveform generator**  
↓  
**Wrist-mounted actuator**

---

## Why Neuromorphic Computing?

A continuously operating wearable has substantially different constraints from a desktop machine or cloud-based ML system.

The eventual system must prioritize:

- Low power consumption
- Continuous operation
- Small battery capacity
- Local inference
- Low latency
- Privacy
- Sparse computation
- Minimal data movement

Conventional neural-network inference repeatedly moves weights and activations between memory and arithmetic units.

Neuromorphic and in-memory-compute architectures attempt to reduce this cost by placing computation closer to where information is represented.

This project investigates two complementary ideas.

### Spiking Neural Networks

An SNN represents information through discrete spike events and maintains temporal state using neuron dynamics.

Sparse spike activity creates the possibility of performing computation only when physiologically relevant activity occurs.

### Analog Conductance-Based Compute

A neural-network multiplication can be represented electrically using:

**I = GV**

where:

- **V** represents an input
- **G** represents a weight
- **I** represents the multiplication result

Multiple conductances connected to a common column can naturally sum currents through Kirchhoff's current law:

**I<sub>out</sub> = Σ G<sub>i</sub>V<sub>i</sub>**

This is the physical basis of analog vector-matrix multiplication.

---

## Phase 1 Scope

The complete wearable is intentionally divided into phases.

### Phase 1 — Neuromorphic Inference Proof of Concept

The current phase demonstrates the bridge between:

**physiological data → SNN → quantized weight → physical conductance → analog MAC**

The goal is not yet to fabricate a complete analog accelerator.

Instead, Phase 1 asks:

> Can a physiological stress classifier be trained in software and then map its learned numerical weights onto a physically characterized CMOS analog-compute primitive?

The Phase 1 architecture is:

![Phase 1 architecture](figures/phase1_architecture.png)

*Figure 2 — Phase 1 hardware/software co-design flow. Physiological data is converted into features and spikes, processed by a quantized SNN, and trained weights are mapped onto measured SKY130 transistor conductances for analog multiplication.*

---

# Algorithm Layer

## Dataset

Initial development uses the **WESAD physiological stress dataset**.

ECG and EDA signals are used as the physiological inputs for the current proof of concept.

The present implementation uses a subset of subjects during development; expanding to subject-independent evaluation across the full dataset is part of the next stage.

---

## Initial Attempt — Raw Physiological Amplitudes

The first implementation directly encoded raw ECG and EDA amplitudes into spike rates.

This exposed two distinct SNN failure modes.

### Dead Network

With some combinations of initialization and LIF threshold, the neurons produced essentially no spikes.

The binary classification loss remained close to:

**ln(2) ≈ 0.693**

indicating that the network was behaving approximately like a random classifier.

### Saturated Network

Other parameter combinations produced the opposite failure mode: neurons fired almost continuously.

This removed useful temporal selectivity and again resulted in poor classification.

Rather than adjusting parameters blindly, I instrumented the model to monitor **firing rates throughout training**.

This made it possible to distinguish:

- No neuronal activity
- Useful sparse activity
- Saturated activity

directly.

---

## Physiologically Informed Features

Raw ECG amplitude itself is not necessarily the most informative representation of physiological stress.

The preprocessing pipeline was therefore changed to extract more meaningful physiological variables.

### ECG

R-peaks are detected from the ECG signal.

Successive R-peaks produce RR intervals:

**RR<sub>i</sub> = t<sub>R,i+1</sub> − t<sub>R,i</sub>**

These intervals are converted into instantaneous heart rate:

**HR = 60 / RR**

### EDA

EDA is decomposed into tonic and phasic components.

The **phasic component** captures faster sympathetic electrodermal responses and is therefore used as the primary EDA feature in the current model.

The current neural-network input is therefore based on:

- Instantaneous heart rate
- Phasic EDA

rather than raw ECG or raw skin-conductance amplitude.

---

![Physiological preprocessing](figures/physiology_pipeline.png)

*Figure 3 — Physiological preprocessing pipeline. ECG is converted into R-peak-derived heart rate while EDA is decomposed to isolate the faster phasic component before spike encoding.*

---

# Spiking Neural Network

The classifier is implemented using **snnTorch** and leaky-integrate-and-fire neurons.

A simplified LIF neuron evolves according to accumulated input and membrane leakage until its membrane potential crosses a threshold.

Conceptually:

**U[t+1] = βU[t] + I[t]**

When:

**U[t] ≥ U<sub>th</sub>**

the neuron emits a spike and resets according to the selected neuron model.

This makes the network inherently temporal rather than treating each physiological sample as an independent static observation.

---

## Quantization-Aware Training

The eventual analog hardware cannot represent arbitrary floating-point neural-network weights.

The SNN is therefore trained with **fake quantization**.

Weights are constrained during forward propagation to discrete levels while a straight-through estimator allows gradients to continue flowing during backpropagation.

The current target is:

**4-bit weights**

corresponding to:

**2<sup>4</sup> = 16 possible weight levels**

This creates a direct hardware requirement:

> The analog synapse architecture should eventually support approximately 16 distinguishable effective conductance states if the 4-bit model is to be reproduced physically.

---

![SNN training results](figures/snn_training_results.png)

*Figure 4 — SNN training and validation behavior. Firing-rate telemetry was used alongside loss and accuracy to diagnose both dead-neuron and saturated-neuron regimes during model development.*

---

# Analog Compute Primitive

## Why Start With One Synapse?

Real analog in-memory-compute accelerators can contain very large crossbar arrays.

Reproducing an architecture of that scale is not meaningful for the first stage of this project.

Instead, Phase 1 begins with the smallest useful computational primitive:

> **One voltage-controlled conductance representing one neural-network weight**

The primitive is implemented using a SKY130 NMOS transistor biased in its approximately linear / triode operating region.

For sufficiently small **V<sub>DS</sub>**, the device can approximately behave as a voltage-controlled conductance:

**I<sub>D</sub> ≈ G(V<sub>GS</sub>)V<sub>DS</sub>**

The input voltage performs the multiplication while the transistor conductance represents the weight magnitude.

---

## Important Limitation — Weight Storage

The current cell is an **AIMC-inspired analog conductance primitive**, not yet a nonvolatile memory cell.

The neural-network weight is represented through the externally applied gate voltage:

**w → G → V<sub>GS</sub>**

The NMOS itself does not permanently store the trained weight.

A scalable implementation would require an additional method of retaining or generating each weight state, such as:

- Nonvolatile resistive memory
- Floating-gate storage
- Local sample-and-hold
- Digitally stored weights with compact DAC generation

This distinction is important when comparing the proof of concept with true PCM/RRAM-based analog in-memory-compute systems.

---

# Device Characterization

## Initial Minimum-Length Device

The first synapse experiments used a minimum-length SKY130 NMOS:

**L = 0.15 µm**

Unexpected drain current remained present even with low gate bias and increased substantially with drain voltage.

Because short-channel devices can exhibit effects including drain-induced barrier lowering and increased subthreshold conduction, the minimum-length device did not behave like the simple long-channel voltage-controlled resistor assumed by the initial model.

The design was therefore changed to:

**L = 1 µm**

The longer-channel device produced significantly cleaner off-state behavior and a more useful approximately linear operating region.

---

## Measured Rather Than Idealized Conductance

The SKY130 MOS model uses a detailed BSIM device description.

Rather than relying on a simplified square-law MOS equation to estimate conductance, I extracted the behavior directly from ngspice simulations.

For each selected gate voltage:

**G ≈ ΔI<sub>D</sub> / ΔV<sub>DS</sub>**

within the chosen low-**V<sub>DS</sub>** read region.

This produced a measured:

**G(V<sub>GS</sub>)**

lookup table.

---

![NMOS IV characterization](nmos_iv_characterization.png)

*Figure 5 — SKY130 NMOS drain-current characterization across multiple gate biases. The low-VDS region is used to determine the useful approximately linear conductance range.*

---

![Conductance LUT](conductance_lut.png)

*Figure 6 — Extracted conductance versus gate voltage. Rather than relying on an ideal MOS square-law model, the neural-network weight mapping uses conductances measured directly from the SKY130 BSIM simulation.*

---

# Weight-to-Hardware Mapping

The trained SNN produces numerical weights:

**w<sub>i</sub>**

The hardware needs conductances:

**G<sub>i</sub>**

The magnitude of each trained weight is therefore scaled into the characterized hardware conductance range.

Conceptually:

**|w<sub>i</sub>| → G<sub>i</sub> → V<sub>GS,i</sub>**

Interpolation through the measured conductance lookup table determines the gate voltage required to approximately produce that target conductance.

This creates the bridge between the software neural network and the analog circuit.

---

## Signed Weights

Physical conductances are non-negative, while neural-network weights can be positive or negative.

The proposed architecture therefore uses differential conductance encoding:

**w<sub>i</sub> ∝ G<sub>i</sub><sup>+</sup> − G<sub>i</sub><sup>−</sup>**

A positive weight is represented primarily in the **G<sup>+</sup>** branch.

A negative weight is represented primarily in the **G<sup>−</sup>** branch.

For multiple inputs:

**I<sup>+</sup> = Σ V<sub>i</sub>G<sub>i</sub><sup>+</sup>**

**I<sup>−</sup> = Σ V<sub>i</sub>G<sub>i</sub><sup>−</sup>**

and the signed result is:

**I<sub>out</sub> = I<sup>+</sup> − I<sup>−</sup>**

---

![Differential weight architecture](differential_synapse.png)

*Figure 7 — Differential representation of signed neural-network weights using positive and negative conductance branches.*

---

# Analog Multiply-Accumulate Validation

The next step was to verify that multiple analog synapse cells could perform a summed multiplication.

For two independently driven cells:

**I<sub>ideal</sub> = G<sub>1</sub>V<sub>1</sub> + G<sub>2</sub>V<sub>2</sub>**

The devices share a common output column so their drain currents naturally sum.

This reproduces the core operation required for vector-matrix multiplication.

---

![Two-cell MAC](two_cell_mac.png)

*Figure 8 — Two-element analog MAC proof of concept. Independently controlled input voltages drive two weight-programmed conductance cells whose currents sum at the output node.*

---

## Initial MAC Result

Using conductances independently extracted from the transistor characterization:

**|I<sub>SPICE</sub>| = 31.15 µA**

compared with an idealized hand calculation of:

**I<sub>calc</sub> = 37.88 µA**

giving a magnitude difference of approximately:

**17.8%**

The remaining error is useful rather than being hidden.

It demonstrates that the MOS devices are not ideal linear resistors.

Possible contributors include:

- Finite V<sub>DS</sub>
- Conductance dependence on V<sub>DS</sub>
- Nonlinear device behavior between LUT points
- Movement of the shared summing node
- Coarse conductance interpolation

This motivates both denser device characterization and a virtual-ground column readout in the next phase.

---

![MAC comparison](mac_validation.png)

*Figure 9 — Comparison between ideal conductance-based MAC prediction and transistor-level ngspice simulation.*

---

# From Stress Detection to Haptic Response

The neuromorphic accelerator forms only the detection portion of the final wearable.

The stress output should not directly activate the haptic motor from a single classification sample.

Instead, the final system will include persistence and confidence logic.

For example, haptic feedback could require:

**P(stress) > T**

for several consecutive inference windows.

This reduces the chance that a brief physiological fluctuation causes unnecessary activation.

A future controller will therefore implement:

- Stress-confidence threshold
- Minimum persistence time
- Hysteresis
- Maximum haptic duration
- Refractory period
- User override / stop

Once the condition is satisfied, the controller drives a wrist-mounted haptic actuator with a heartbeat-like pulse pattern.

---

![Stress decision and haptic control](haptic_control_architecture.png)

*Figure 10 — Proposed stress-decision and haptic-response architecture. Sustained elevated stress confidence triggers a controlled heartbeat-like tactile waveform rather than responding immediately to individual classifier outputs.*

---

# Phase 1 Results

Phase 1 currently demonstrates:

- Physiological preprocessing of real WESAD ECG and EDA data
- R-peak-derived heart-rate extraction
- Phasic EDA extraction using NeuroKit2
- LIF-based SNN implementation in snnTorch
- Direct firing-rate telemetry for diagnosing dead and saturated SNN states
- 4-bit quantization-aware weight training
- SKY130 NMOS characterization as a voltage-controlled conductance
- Identification of minimum-channel-length behavior that violated the intended simple linear-cell assumption
- Transition to a longer-channel device with more suitable analog behavior
- Direct extraction of a conductance-versus-V<sub>GS</sub> lookup table
- Mapping of real trained SNN weights into physical conductance targets
- Differential G<sup>+</sup>/G<sup>−</sup> architecture for signed weights
- Small-scale transistor-level analog MAC validation

The current SNN result on the limited four-subject development subset is:

**Best validation accuracy: 60.6%**

with a majority-class baseline of approximately:

**65%**

This means the current classifier does **not yet outperform the trivial baseline**.

Rather than presenting this as a successful stress classifier, I treat it as an unresolved Phase 1 algorithm result.

The next algorithm experiments will determine whether the limitation comes primarily from:

- Insufficient subject diversity
- Feature representation
- Normalization
- SNN architecture
- Temporal encoding
- Class imbalance
- Hyperparameters

---

# What Phase 1 Establishes

The most important result of Phase 1 is not classification accuracy alone.

It establishes an end-to-end hardware/software path:

**Physiological data**  
↓  
**Physiological features**  
↓  
**Spike encoding**  
↓  
**Quantized SNN**  
↓  
**Trained numerical weights**  
↓  
**Measured SKY130 conductances**  
↓  
**Analog multiplication / accumulation**

This provides the foundation for evaluating whether a neuromorphic hardware architecture could eventually support the wearable's continuous stress-inference workload.

---

# Phase 2 — Hardware-Aware Neuromorphic Accelerator

The next phase will extend the single-cell / small-MAC proof of concept into a more realistic accelerator architecture.

Planned work includes:

- 16 distinguishable conductance states for true 4-bit mapping
- Denser G(V<sub>GS</sub>) characterization
- Differential signed-weight MAC implementation
- Independently driven MAC inputs
- Virtual-ground / transimpedance column readout
- 4×4 vector-matrix multiplication array
- Python-versus-SPICE VMM comparison
- Device nonlinearity characterization
- Process-corner analysis
- Temperature analysis
- Analog-error-aware SNN inference
- Quantization precision comparison

---

# Phase 3 — Wearable Sensor Electronics

Once the inference architecture is established, the project will move from prerecorded dataset input toward physical sensing.

Planned hardware includes:

- ECG or PPG sensing
- EDA acquisition
- Low-noise analog front end
- ADC
- Embedded controller
- Power-management system
- Battery
- Neuromorphic inference interface

---

# Phase 4 — Heartbeat-Like Haptic Feedback

The detection output will be connected to a wearable haptic subsystem.

The planned signal path is:

**Stress-state decision → haptic waveform controller → actuator driver → wrist-mounted haptic actuator**

Rather than producing continuous vibration, the actuator will generate a deliberate pulse pattern intended to resemble a calm heartbeat.

---

# Phase 5 — Closed-Loop Wearable

The final architectural goal is:

**Sense → infer → respond → reassess**

Physiological signals continue to be monitored after haptic activation, allowing the system to observe whether the user's physiological state changes.

This creates a closed-loop architecture rather than a one-shot alarm.

Any eventual testing involving vulnerable populations or minors would require appropriate research ethics approval, safeguarding procedures, and relevant clinical/research supervision.

The current work is an engineering proof of concept and does not constitute a medical diagnostic or treatment device.

---

# Current Open Questions

## Algorithm

- Does performance improve substantially when training across more WESAD subjects?
- How does subject-independent performance compare with subject-specific performance?
- Are heart rate and phasic EDA sufficient?
- Would HRV and additional EDA features improve separability?
- Can event-based encoding reduce spike activity while preserving classification?

## Hardware

- How many distinct conductance states remain separable across process and temperature variation?
- How strongly does V<sub>DS</sub> nonlinearity affect MAC accuracy?
- Can a virtual-ground column architecture substantially reduce error?
- How much classification degradation results from real analog weight error?
- What is the most practical mechanism for storing or generating synaptic weight voltages?

## Wearable System

- What stress-confidence threshold should trigger haptic feedback?
- How long should elevated stress persist before activation?
- What haptic waveform is most appropriate?
- What sensing modality gives the best power / robustness tradeoff for a wrist-worn implementation?

---

# Phase 1 Completion Checklist

Before marking Phase 1 complete:

- [ ] SNN trains and performs inference on WESAD-derived physiological features
- [ ] Accuracy and balanced classification metrics reported
- [ ] Firing-rate behavior characterized
- [ ] 4-bit quantized weights exported
- [ ] SKY130 NMOS analog operating region characterized
- [ ] Dense G(V<sub>GS</sub>) lookup table generated
- [ ] Real trained SNN weights mapped to conductance
- [ ] Positive and negative weight mapping demonstrated
- [ ] Two independent analog MAC inputs simulated
- [ ] SPICE MAC result compared numerically against expected value
- [ ] Wearable system architecture diagram completed
- [ ] Phase 1 architecture diagram completed
- [ ] Device-characterization plots added
- [ ] Code and simulation repository linked

---

# Repository

Source code, ngspice simulations, SKY130 characterization scripts, SNN training code, and generated results will be available in the associated project repository.