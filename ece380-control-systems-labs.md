# Experimental Control Systems & Motor Characterization

### ECE 380 — Analog Control Systems Laboratory

**Course:** ECE 380 — University of Waterloo  
**Tools:** Oscilloscope, function generator, servo-motor training system, analog control modules  
**Topics:** Open/closed-loop response, bandwidth, motor characterization, P/PI/PD/PID control, experimental validation

> **Experimentally characterized open- and closed-loop control systems, identified physical motor parameters, and evaluated P, PI, PD, and PID controllers using measured transient response, bandwidth, steady-state error, overshoot, and settling time.**

---

## Overview

The ECE 380 laboratory sequence focused on connecting classical control theory with the behavior of real physical systems.

Across four laboratory experiments, I worked with analog control modules and a servo-motor system to:

- Characterize open- and closed-loop frequency response
- Measure system bandwidth and time constants
- Compare theoretical and experimental plant behavior
- Characterize a physical DC motor and tachometer
- Implement motor speed and position control
- Evaluate the effects of proportional gain
- Experimentally tune P, PI, PD, and PID controllers
- Measure transient-response characteristics including peak time, overshoot, settling time, and steady-state error

The primary emphasis was not simply obtaining a stable response, but observing how changing controller parameters altered the measured behavior of the physical plant.

---

# Open- vs Closed-Loop Dynamics

The first experiment compared the behavior of a plant in open- and closed-loop configurations.

Bandwidth was determined experimentally by reducing the output amplitude to approximately the system's bandwidth criterion while sweeping input frequency.

Measured results included:

**Open-loop bandwidth ≈ 722.6 rad/s**

**Closed-loop bandwidth ≈ 4712.4 rad/s**

The corresponding measured time constants were:

**τ<sub>open</sub> ≈ 1.20 ms**

**τ<sub>closed</sub> ≈ 0.274 ms**

The measurements demonstrated the significantly faster response obtained after closing the feedback loop.

---

![Open- and closed-loop response](figures/ece380_open_closed_loop.png)

*Figure 1 — Representative oscilloscope measurements used to characterize open- and closed-loop bandwidth and transient response.*

---

# Proportional Feedback

A later experiment examined how proportional gain affected the closed-loop step response.

The controller gain was increased through:

**K<sub>p</sub> = 1, 2, 4, 8**

Increasing proportional gain reduced measured steady-state error:

| Kp | Steady-State Error | Overshoot |
| ---: | ---: | ---: |
| 1 | 50.5% | 37.9% |
| 2 | 33.0% | 47.0% |
| 4 | 20.2% | 60.3% |
| 8 | 14.9% | 65.6% |

The experiment made the control tradeoff directly visible:

**higher K<sub>p</sub> → lower steady-state error, but greater overshoot**

Rather than treating gain as simply something to maximize, the measurements showed why controller selection requires balancing competing transient-response requirements.

---

![Proportional-controller response](figures/ece380_proportional_response.png)

*Figure 2 — Experimental closed-loop step response while varying proportional gain.*

---

# DC Motor Characterization

The third laboratory moved from an abstract plant to a physical servo-motor system.

Motor speed was measured experimentally and used to estimate the motor and tachometer constants.

The average measured motor speed was approximately:

**ω<sub>motor</sub> ≈ 1493 rpm**

The experimentally determined tachometer constant was:

**K<sub>tach</sub> ≈ 5.908 V/krpm**

compared with a nominal value of:

**K<sub>tach,nom</sub> = 6 V/krpm**

The motor constant was measured as:

**K<sub>M</sub> ≈ 0.1508 krpm/V**

compared with the nominal:

**K<sub>M,nom</sub> ≈ 0.1583 krpm/V**

The measured motor time constant was:

**τ<sub>M</sub> ≈ 4.8 s**

This experiment provided experience extracting physical model parameters from measured system behavior rather than relying only on nominal component values.

---

![Motor transient response](figures/ece380_motor_characterization.png)

*Figure 3 — Experimental motor response used to estimate angular speed, motor gain, tachometer gain, and motor time constant.*

---

# Motor Speed & Position Control

The motor was then operated under feedback for both speed and position control.

For speed control, increasing proportional gain reduced steady-state error.

For example:

**K<sub>p</sub> = 1 → e<sub>ss</sub> ≈ 28.7%**

**K<sub>p</sub> = 2 → e<sub>ss</sub> ≈ 14.7%**

**K<sub>p</sub> = 4 → e<sub>ss</sub> ≈ 7.3%**

Position-control experiments were also used to measure:

- Peak output
- Steady-state output
- Peak time
- Overshoot
- Steady-state error
- Onset of amplifier saturation

This exposed another practical control-system issue: increasing gain can improve tracking while eventually being limited by the physical saturation of the electronics and actuator.

---

# Plant Model Validation

The final laboratory compared simulated plant behavior against experimental measurements.

For the assigned plant, the simulated and experimental transient characteristics were close in several measurements.

For example:

| Metric | Simulation | Experiment |
| --- | ---: | ---: |
| Peak time | 56.5 ms | 54.8 ms |
| Settling time | 116.2 ms | 116 ms |
| Overshoot | 13.47% | 16.3% |

This provided a direct comparison between an analytical/simulated model and the actual physical implementation.

The discrepancy between modeled and measured overshoot also illustrated why experimentally validating a model remains important even when the underlying transfer function is known.

---

# P, PI, PD & PID Control

The plant was then characterized with several controller structures.

## P Control

Increasing proportional gain reduced steady-state error but increased overshoot.

For example:

**K<sub>p</sub> = 1 → e<sub>ss</sub> ≈ 53.4%, OS ≈ 13.5%**

**K<sub>p</sub> = 2.5 → e<sub>ss</sub> ≈ 26.3%, OS ≈ 38.9%**

---

## PI Control

Integral action was introduced to reduce steady-state error.

With:

**K<sub>p</sub> = 1**

and

**K<sub>i</sub> = 40**

the measured steady-state error was approximately:

**e<sub>ss</sub> = 0%**

while maintaining approximately:

**13.5% overshoot**

This experimentally demonstrated the primary benefit of integral action: eliminating persistent tracking error.

---

## PD Control

Derivative action was investigated by varying **K<sub>d</sub>** while keeping proportional gain constant.

The measurements showed how derivative action altered transient behavior and damping without eliminating the underlying steady-state error produced by proportional control alone.

---

## PID Control

The final controller combined proportional, integral, and derivative action.

A heuristic tuning experiment used approximately:

**K<sub>p</sub> = 1**

**K<sub>i</sub> = 3.83**

**K<sub>d</sub> = 0.015**

with a measured settling time of approximately:

**T<sub>s</sub> ≈ 96 ms**

and near-zero steady-state tracking error.

---

![P PI PD PID comparison](figures/ece380_controller_comparison.png)

*Figure 4 — Representative experimental responses used to compare proportional, integral, derivative, and combined PID control.*

---

# What I Learned

The most useful part of these experiments was seeing control-system tradeoffs on real hardware rather than only through transfer functions.

The laboratory work reinforced several practical observations:

**Increasing proportional gain**

→ improves tracking  
→ reduces steady-state error  
→ can increase overshoot and eventually cause saturation

**Adding integral action**

→ eliminates persistent steady-state error  
→ can increase oscillation or settling time if tuned aggressively

**Adding derivative action**

→ changes damping and transient behavior  
→ does not independently remove steady-state error

Most importantly, the experiments showed that a simulated plant model can provide a strong prediction of system behavior, but physical measurements remain necessary to expose nonidealities, saturation, parameter variation, and model mismatch.

---

# Skills Demonstrated

**Experimental controls • Feedback systems • DC motor characterization • P/PI/PD/PID tuning • Oscilloscope measurements • Function-generator testing • Frequency response • Step-response analysis • System identification • Simulation-versus-measurement validation**