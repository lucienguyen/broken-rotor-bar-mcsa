# Can the motor tell you it's broken?
### Detecting broken rotor bars from stator current alone

## 1. Introduction

A broken rotor bar makes an induction motor's rotor magnetically asymmetric. That puts two small sidebands into the stator current at

$$f_{sb} = f\,(1 \pm 2s)$$

where $f$ is the supply frequency and $s$ is the slip. A variable-speed drive already measures the stator current, so if these sidebands are reliable, the drive can diagnose its own motor without an extra sensor.

**Question:** can broken bars be detected, and their number estimated, from stator current alone, and at what load does that stop working?

**Data:** open-access lab recordings of a 1 hp, 4-pole, 60 Hz induction motor loaded by a DC generator [1]. The set covers 5 rotors (healthy and 1–4 broken bars) × 8 loads (12.5–100 %) × 10 repetitions. Each run records three-phase voltage and current plus five accelerometers.


**Method:**
1. Cut 15 s of steady-state current (0.067 Hz resolution) and take a Hann-windowed FFT.
2. Measure the supply frequency in each run.
3. Estimate slip from the current alone, using the eccentricity lines $f \pm f(1-s)/2$.
4. Read the sidebands in dB below the supply peak.
5. Train logistic regression and a random forest on current-only, vibration-only and combined features, splitting by repetition.

## 2. Results

**Current alone detects broken bars and counts them (0–4) with ≥ 95 % balanced accuracy from 25 % load upwards. At 12.5 % load it drops to about 90 %.**

| Load | 12.5 % | 25 % | 50 % | 75 % | 100 % |
|---|---|---|---|---|---|
| Detection | 0.89 | 1.00 | 1.00 | 1.00 | 1.00 |
| Severity (0–4 bars) | 0.90 | 1.00 | 1.00 | 1.00 | 0.98 |

*Current-only random forest, balanced accuracy, cross-validated by repetition.*

**Key findings**

* **Why it fails at light load:** at 12.5 % load the slip is only 0.4 %, so the sidebands sit 0.5 Hz from the main peak, and healthy and 1-bar rotors overlap.
* **No speed sensor needed:** slip estimated from the current matches the vibration-based slip to within 0.04 percentage points.
* **Longer signals are needed at light load:** reliable detection takes 2 s of steady signal at 50 % load, 8 s at 25 % and 15 s at 12.5 %.
* **Train across the whole load range:** a detector trained only at high load misses faults at light load.
* **Vibration's perfect scores are not trustworthy:** vibration features hit 100 % even at 12.5 %, but each fault level is a single rotor, so they likely recognise the installation, not the fault.

**Limitations:** one motor with one rotor per fault level, fed straight from the mains at a fixed frequency. A drive-fed motor would need the sidebands tracked as the supply frequency changes.

## References

[1] A. E. Treml, R. A. Flauzino, M. Suetake, and N. A. R. Maciejewski, "Experimental database for detecting and diagnosing rotor broken bar in a three-phase induction motor," IEEE Dataport, Sep. 16, 2020. [Online]. Available: https://dx.doi.org/10.21227/fmnm-bn95
