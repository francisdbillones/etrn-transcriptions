# Lecture 16 Notes: Bypass Capacitor AC Reactance, Amplifier Fidelity & Distortion, and Total Harmonic Distortion (THD) Analysis

---

## 1. Administrative Context, Classroom Interactions & Professor Tangents

### 1.1 Course Synchronization & Schedule Challenges
* **Desynchronization with Laboratory (LAM):** The sudden transition to online delivery on Friday of the previous week caused a significant desynchronization between lecture progress and laboratory sessions (LAM). The professor noted the difficulty of coping with differing lecture paces and aligning lab experiments under these conditions.
* **Recording & Desktop Sharing Rationale:** The professor shared the entire desktop rather than just the PowerPoint application window. This changed display dimensions on student screens but was necessary to allow seamless switching between PowerPoint slides and live external software demonstrations (LTSpice circuit simulation, control panels, and SPICE error logs).

### 1.2 Professor Tangents & Side Comments
* **Faulty Hardware Tangent (Fake Chinese Wireless Mouse):** During the LTSpice simulation demonstration, the professor experienced erratic mouse behavior while trying to select and drag window elements:
  > *"This is what I get for buying a cheap mouse. One of those Logitech clones, wireless mouse from China, which is a fake unit. Oh well. It still works, unfortunately."*
* **Low Attendance & Class Interactions:**
  * The professor noted surprisingly low online attendance (*"Bakit marami kayong absent today?"*).
  * **Adonis Solomon:** Called on by the professor to answer a question (*"Mr. Solomon. Adonis, are you there?"*). The student immediately logged out of the meeting (*"Ay, nawala. Ang galing, ah. Nag-logged out... Chickened out."*).
  * **Ashley Lee:** Called on next; responded via Direct Message (*"For those who aren't seeing it, she just answered by direct message"*). Discussed whether shape deviation can be quantified.
  * **Gabriel:** Answered earlier in the lecture, suggesting checking input/output periods to measure distortion.

---

## 2. Bypass Capacitor AC Reactance and Voltage Gain Discrepancy

### 2.1 The Mystery: Hand Calculations vs. Initial LTSpice Simulation
In a standard single-stage BJT Common-Emitter (CE) voltage amplifier circuit:
* **Circuit Parameters:**
  * Supply Voltage: $V_{CC} = 12\text{ V}$
  * Emitter Resistor: $R_E = 560\ \Omega$
  * Emitter Bypass Capacitor: $C_E = 10\ \mu\text{F}$
  * Transistor Part Number: **2N2222** (NPN Silicon BJT)
  * Input Signal: $V_{in} = 15\text{ mV}$ peak ($30\text{ mV}_{p-p}$) at $f = 1\text{ kHz}$ ($1000\text{ Hz}$)
* **Gain Discrepancy:**
  * **Hand-Calculated Voltage Gain ($A_v$):** $\approx 140$
  * **Initial Simulated Output Voltage:** $+1.15\text{ V}$ to $-1.196\text{ V}$ $\approx 2.4\text{ V}_{p-p}$ (or $2.346\text{ V}_{p-p}$)
  * **Initial Simulated Gain:** 
    $$A_v = \frac{V_{out(p-p)}}{V_{in(p-p)}} = \frac{2.4\text{ V}}{30\text{ mV}} \approx 80$$
  * **The Problem:** The simulated gain ($\approx 80$) is almost **half** of the hand-calculated expectation ($\approx 140$). Why does this massive difference exist?

---

### 2.2 Mathematical Derivation of Emitter Reactance Impact
Hand calculations typically rely on the simplifying assumption that bypass capacitors act as **ideal AC short circuits** ($Z_C = 0\ \Omega$). However, at $f = 1\text{ kHz}$, the actual capacitive reactance $X_C$ of a $10\ \mu\text{F}$ capacitor is significant:

$$X_C = \frac{1}{2\pi f C_E} = \frac{1}{2\pi (1000\text{ Hz})(10 \times 10^{-6}\text{ F})} = \frac{1}{0.062832} \approx 15.92\ \Omega$$

#### Impact on Effective AC Emitter Impedance ($Z_E$)
1. **Dynamic AC Emitter Resistance ($r'_e$):** Determined by the DC emitter bias current $I_E$:
   $$r'_e = \frac{26\text{ mV}}{I_E}$$
   For typical small-signal biasing, $r'_e$ is on the order of $15\text{--}20\ \Omega$.
2. **Corrected AC Voltage Gain Formula:**
   Because $C_E$ is in parallel with $R_E$, the AC impedance from emitter to ground is $r'_e + X_C$ (rather than just $r'_e$):
   $$A_v = \frac{R_C}{r'_e + X_C}$$
3. **Quantitative Consequence:** Adding $X_C = 15.92\ \Omega$ to $r'_e \approx 16\ \Omega$ approximately **doubles the denominator**, cutting the resulting AC voltage gain in **half** (from $\approx 140$ down to $\approx 70\text{--}80$).

---

### 2.3 AC Frequency Sweep & Decibel Analysis
To verify this behavior, an LTSpice AC Analysis sweep was conducted from $10\text{ Hz}$ to $100\text{ kHz}$ (50 data points per frequency increment), positioning $1\text{ kHz}$ at the geometric center:

* **High Frequency Gain (where $X_C \to 0\ \Omega$):** $+5.63\text{ dB}$
* **1 kHz Gain:** $-1.96\text{ dB}$
* **Gain Difference:** $\Delta A_v \approx 5.63 - (-1.96) \approx 7.59\text{ dB} \approx 6\text{ dB}$

> [!KEY FINDING]
> **Decibel Rule of Thumb:** A **$6\text{ dB}$ drop in voltage gain corresponds to exactly a $50\%$ reduction (half)** of the linear voltage gain. This confirms that the $10\ \mu\text{F}$ capacitor fails to act as an AC short at $1\text{ kHz}$.

---

### 2.4 Circuit Optimization and LTSpice Re-simulation
To eliminate unwanted emitter degeneration and achieve maximum voltage gain:

1. **Upgrade Emitter Bypass Capacitor ($C_E$):** Change $C_E$ from $10\ \mu\text{F}$ to **$250\ \mu\text{F}$**.
   $$X_C = \frac{1}{2\pi (1000\text{ Hz})(250 \times 10^{-6}\text{ F})} \approx 0.636\ \Omega \approx 0\ \Omega$$
   This $0.636\ \Omega$ reactance is negligible compared to $r'_e$.
2. **Upgrade Input Coupling Capacitor ($C_{in}$):** Change $C_{in}$ from $1\ \mu\text{F}$ to **$10\ \mu\text{F}$** to extend flat frequency response down to lower frequencies ($300\text{ Hz}$ and below) without early low-end drooping.
3. **Re-simulated Transient Results:**
   * Output Voltage Swing: $+2.5\text{ V}$ to $-3.1\text{ V} \implies 5.6\text{ V}_{p-p}$
   * Re-calculated Simulated Gain:
     $$A_v = \frac{5.6\text{ V}}{30\text{ mV}} = 186.6$$
   * **Conclusion:** The simulated gain ($186.6$) now aligns closely with theoretical hand-calculated expectations ($\approx 140$), with slight differences accounted for by the specific transistor model beta ($\beta$) parameters in LTSpice.

---

## 3. General Amplifier Performance Characteristics & Semiconductor Limits

### 3.1 Overview of Performance Criteria

| Characteristic | Ideal Metric | Real-World Limitation |
| :--- | :--- | :--- |
| **Output Impedance ($Z_{out}$)** | $0\ \Omega$ | Finite internal output resistance |
| **Input Impedance ($Z_{in}$)** | $\infty\ \Omega$ | Limited by biasing resistor network and $r'_e$ |
| **Gain ($A_v, A_i$)** | Maximum achievable | Constrained by transistor $g_m, \beta,$ and load impedance |
| **Q-Point Thermal Stability** | Zero drift | $I_C$ and $V_{CE}$ shift with temperature ($\Delta T$) (referenced in lab heating experiment) |
| **Bandwidth (BW)** | Infinite ($\text{DC}$ to $\infty$) | Low-end cut-off set by $C_{in}, C_{out}, C_E$; high-end cut-off set by semiconductor physics |
| **Fidelity / Distortion** | Perfect shape preservation ($\text{THD} = 0\%$) | Non-linear $V_{out}\text{ vs }V_{in}$ transfer curve introduces distortion |

---

### 3.2 Semiconductor High-Frequency Roll-Off & Simulation Limits
* **High-Frequency Sweep Demonstration:** When pushing the LTSpice AC simulation of the **2N2222** transistor to extreme frequencies ($3\text{ MHz}$ and $20\text{ MHz}$), a noticeable gain drop-off appears at the high-frequency limit.
* **Semiconductor Physics Cause:** Transistor $\beta$ naturally degrades at high frequencies due to minority carrier transit time and internal junction capacitance (base-emitter $C_{be}$ and base-collector $C_{bc}$).
* **2N2222 Capability:** The 2N2222 is a fast switching/RF transistor; its high-end drop-off is well beyond the audible frequency range ($20\text{ Hz}\text{--}20\text{ kHz}$, or up to $50\text{--}100\text{ kHz}$).
* **SPICE Simulation Limitation Warning:** Standard SPICE simulations do **not** capture all real-world parasitic effects. Physical layout resistances, parasitic trace inductances, and stray capacitances on a physical PCB will degrade high-frequency performance earlier than predicted by SPICE.
* **Amplifier Bandwidth Example:** The simulated CE amplifier demonstrated a usable bandwidth from approximately **$230\text{ Hz}$ to $10\text{ MHz}$**.

---

## 4. Signal Fidelity vs. Distortion

### 4.1 Concept and Marriage/Couple Analogy
* **Human/Animal Couple Analogy:** The professor explained fidelity using the analogy of human or animal relationships:
  * **Fidelity:** Remaining faithful between two partners.
  * **Infidelity:** One or both partners straying away with someone else.
* **Electronic Definition:** Fidelity describes how faithfully the **geometric shape** of the output signal reproduces the shape of the input signal (a strict pairing/comparison of output vs. input).

### 4.2 What Fidelity Is vs. What It Is Not

> [!IMPORTANT]
> **Fidelity is strictly concerned with SHAPE, not amplitude or phase!**

1. **Amplitude / Gain:** Voltage gain ($A_v$) is a design specification. Having high or low voltage gain does **NOT** constitute infidelity or distortion.
2. **Phase Inversion:** A $180^\circ$ phase shift ($A_v < 0$, standard for common-emitter amplifiers) turns the signal upside down but preserves exact geometric shape. **Phase inversion is NOT infidelity or distortion.**

---

### 4.3 Definition of Distortion
* **Ideal Amplifier Transfer Function:** $V_{out} = K \cdot V_{in}$ where $K = A_v$ is a constant. The plot of $V_{out}$ vs $V_{in}$ is a perfectly straight line (incline steepness represents gain).
* **Real-World Amplifier Transfer Function:** $K$ is non-constant across the signal swing. The transfer curve is non-linear (curved or "lazy" S-shape).
* **Distortion:** Any deviation in output signal shape relative to the input signal shape caused by transfer curve non-linearities.

```
       Ideal Transfer Curve              Real-World Non-Linear Transfer Curve
          V_out                              V_out
            ^  /                               ^   / (S-shaped / "lazy")
            | /                                |  /
            |/                                 | /
  ----------+----------> V_in        ----------+----------> V_in
           /|                                 /|
          / |                                / |
         /  |                               /  |
```

---

## 5. The Engineering Challenge of Quantifying Distortion

### 5.1 Historical Context
Before modern digital oscilloscopes, MATLAB, and SPICE tools, engineers in the 1940s through 1980s required a standardized, scalar numerical metric (a figure of merit) to quantify amplifier distortion.

---

### 5.2 Visualizing Non-Linearity in LTSpice
To the naked eye, a distorted output waveform may appear to be a normal sine wave. However, detailed examination reveals asymmetry:
* **Vertical Asymmetry:** Splitting the output cycle vertically down the middle shows that the top half-cycle is **fat/wide** while the bottom half-cycle is **narrow/compressed**.
* **LTSpice Mathematical Overlay Method:**
  To highlight distortion, LTSpice was commanded to plot an ideal linearly scaled input waveform alongside the actual output waveform:
  $$\text{Trace Equation} = -185 \cdot V(N005) + 0.35\text{ V}$$
  * Scaling by $-185$ represents a pure linear mathematical gain factor (zero distortion).
  * Adding $+0.35\text{ V}$ aligns the vertical DC centers.
  * **Visual Result:** The bottom peak of the real output is visibly narrower than ideal, while the top peak is visibly wider than ideal.

---

### 5.3 Evaluating Proposed Quantifying Methods

1. **Method 1: Checking Signal Period / Frequency (Proposed by student Gabriel)**
   * **Verdict: FAILS.**
   * **Reason:** Any semi-linear transfer function outputs a signal at the exact same fundamental period/frequency as the input, regardless of how badly distorted the shape becomes (unless the amplifier is completely unstable and self-oscillating).
2. **Method 2: Graphical Area Difference**
   * **Verdict: IMPRACTICAL HISTORICALLY.**
   * **Reason:** Measuring the geometric area between the ideal sinusoid and distorted output waveform requires image processing/graphical integration tools unavailable in the mid-20th century.
3. **Method 3: Time-Domain Subtraction (Error Voltage $V_e(t)$)**
   * **Formula:** $V_e(t) = V_{out}(t) - K \cdot V_{in}(t)$
   * **Verdict: IMPRACTICAL FOR SCALAR METRIC.**
   * **Reason:** Electronically subtracting input from output yields a complex, non-standard time-domain waveform $V_e(t)$. Because $V_e(t)$ is continuously changing over time and not a standard wave shape (like a pure sine, square, or triangle wave), it cannot easily be reduced to a single descriptive scalar number.

---

## 6. The DSP Solution: Total Harmonic Distortion (THD)

### 6.1 Excitation Signal Selection: The Pure Sinusoid
The fundamental breakthrough in distortion measurement relies on digital signal processing (DSP) and Fourier Analysis principles:

> [!NOTE]
> **DSP Fundamental Property:** A pure sinusoidal (or cosinusoidal) wave is the **ONLY periodic time-domain signal whose frequency spectrum consists of a SINGLE spectral line (single frequency component)** at fundamental frequency $f_1$.

* **Pure Sinusoid Spectrum:** Single discrete peak at $f_1$; zero amplitude at all other frequencies.
* **Non-Sinusoidal Waveforms (for comparison):**
  * **Square Wave Spectrum:** Fundamental $f_1$ plus odd harmonics ($3f_1, 5f_1, 7f_1, \dots$).
  * **Triangle Wave Spectrum:** Fundamental $f_1$ plus odd harmonics ($3f_1, 5f_1, 7f_1, \dots$).

Therefore, if an amplifier is excited with a pure sinusoid:
* **Ideal Output:** Pure single spectral line at $f_1$.
* **Distorted Output:** Any alteration in shape generates extra discrete frequency peaks at integer multiples of $f_1$ ($2f_1, 3f_1, 4f_1, \dots$). These generated frequencies are called **Harmonics**.

---

### 6.2 Spectral Fingerprints of Distortion Types

1. **Asymmetric Distortion (Unsymmetric / DC Offset):**
   * Occurs when clipping or non-linearity is unequal on positive vs. negative half-cycles.
   * **Spectral Signature:** Generates a DC offset component ($0\text{ Hz}$) and **EVEN harmonics** ($2f_1, 4f_1, 6f_1, \dots$).
   * In extreme overload/rectification cases, the fundamental $f_1$ may be missing entirely while strong $2f_1, 4f_1, 6f_1$ components dominate.
2. **Symmetric Distortion (Top/Bottom Mirror Symmetry):**
   * Occurs when clipping or non-linearity affects positive and negative half-cycles equally.
   * **Spectral Signature:** Zero DC offset ($0\text{ Hz}$) and **ODD harmonics** ($3f_1, 5f_1, 7f_1, \dots$).

---

### 6.3 Mathematical Definition of THD
Total Harmonic Distortion (THD) is defined as the ratio of the geometric (RMS) sum of all harmonic voltage magnitudes to the voltage magnitude of the fundamental component:

$$\text{THD} = \frac{\sqrt{V_2^2 + V_3^2 + V_4^2 + \dots + V_n^2}}{V_1} = \frac{\sqrt{\sum_{k=2}^{n} V_k^2}}{V_1}$$

Expressed as a percentage ($\%\text{THD}$):

$$\%\text{THD} = \frac{\sqrt{\sum_{k=2}^{n} V_k^2}}{V_1} \times 100\%$$

Where:
* $V_1$ = Magnitude of fundamental frequency $f_1$
* $V_2, V_3, \dots, V_n$ = Magnitudes of 2nd, 3rd, ..., $n$-th harmonics ($2f_1, 3f_1, \dots, n f_1$)

---

### 6.4 Why THD is an Outstanding Figure of Merit

1. **Unitless Ratio:** Voltage units cancel ($\text{V}/\text{V}$). Absolute signal amplitudes do not alter the THD ratio.
2. **Gain Independent:** Amplifier voltage gain ($A_v$) has zero effect on the measured THD percentage.
3. **Phase Invariant:** Fourier magnitude calculations ignore phase angles. Inverting amplifiers ($180^\circ$ shift) or phase-lagged circuits do not skew THD.
4. **Isolates Pure Shape Deviation:** THD focuses exclusively on geometric non-linearity and shape fidelity.

---

## 7. Performing THD and Fourier Analysis in LTSpice

### 7.1 Required Simulation Setup Steps
To perform an accurate Fourier/THD calculation in LTSpice:

1. **Excitation Source:** Use a clean sinusoidal voltage source (`SINE` function) at fundamental frequency $f_{in}$ (e.g., $1\text{ kHz}$).
2. **Cycle Count for FFT Resolution:** FFT quality degrades severely if only 1 or 2 cycles are analyzed. You must collect data across **10 to 50 cycles**.
   * Example Transient Command:
     ```spice
     .tran 0 1.05s 1.0s
     ```
     This instructs LTSpice to simulate up to $1.05\text{ s}$ but only save data from $1.0\text{ s}$ to $1.05\text{ s}$ ($50\text{ ms}$ duration $= 50$ complete cycles of a $1\text{ kHz}$ signal).

---

### 7.2 SPICE Directive `.four` Syntax
Place a SPICE text directive on the schematic using the following syntax:

```spice
.four <fundamental_frequency> V(<output_node>) [V(<input_node>)]
```

**Classroom Example Command:**
```spice
.four 1kHz V(N003) V(N005)
```
* `1kHz`: Fundamental frequency parameter. **MUST match the input signal generator frequency exactly**, or the THD calculation will be invalid.
* `V(N003)`: Output node being evaluated for distortion.
* `V(N005)`: Input reference node (ideal mathematical sine source, expected $\approx 0\%$ THD).

---

### 7.3 Viewing Results & Common Simulation Pitfalls

1. **Accessing Output Data:**
   * After running `.tran`, navigate to: **`View` $\rightarrow$ `SPICE Error Log`** (or press `Ctrl + L`).
   * The SPICE Error Log displays the Fourier analysis table, listing harmonic amplitudes, relative phases, and computed total THD percentage.
2. **Classroom Demonstration Result:**
   * Output Node `N003`: Reported $\approx 10\%\text{--}11.78\%$ THD (indicating severe non-linear distortion).
   * Input Reference Node `N005`: Unexpectedly reported $\approx 1\%$ THD (instead of pure $0\%$).
3. **Simulation Gotcha / Warning:**
   * An input reference showing $>0\%$ THD indicates incomplete LTSpice transient parameters.
   * **Required Fixes:** Restricting max step size (`maxstep`), disabling data compression (`.options plotwinsize=0`), and allowing sufficient settling time before data capture. The professor ran out of time at 9:00 PM and scheduled detailing these control panel settings for the next lecture (Thursday).

---

## 8. Summary of Formulas & Quick Reference Guide

| Parameter / Metric | Mathematical Formula | Key Description |
| :--- | :--- | :--- |
| **Capacitive Reactance** | $X_C = \frac{1}{2\pi f C}$ | AC opposition of capacitor; inversely proportional to frequency $f$ and capacitance $C$ |
| **Total AC Emitter Impedance** | $Z_E = r'_e + X_C$ | Total AC resistance at emitter terminal |
| **Corrected CE Voltage Gain** | $A_v = \frac{R_C}{r'_e + X_C}$ | Voltage gain accounting for non-zero bypass capacitor reactance $X_C$ |
| **Dynamic Emitter Resistance** | $r'_e = \frac{26\text{ mV}}{I_E}$ | Internal thermal emitter diode resistance at room temperature |
| **Decibel Gain Drop** | $\Delta A_v(\text{dB}) = 20 \log_{10}\left(\frac{A_{v1}}{A_{v2}}\right)$ | A $6\text{ dB}$ reduction corresponds to a $50\%$ drop in linear voltage gain |
| **Total Harmonic Distortion** | $\text{THD} = \frac{\sqrt{\sum_{k=2}^{n} V_k^2}}{V_1}$ | Ratio of RMS sum of harmonic magnitudes to fundamental magnitude |
| **Time-Domain Error Signal** | $V_e(t) = V_{out}(t) - K \cdot V_{in}(t)$ | Point-by-point shape difference between output and scaled ideal input |
