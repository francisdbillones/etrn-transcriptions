# Lecture 13 Exhaustive Academic Notes: BJT Inverter Switch Design, LTSpice AC Simulation, & Amplifier Impedance Loading

---

## 1. Course Administration, Exam Logistics, & Quiz Scope

> [!IMPORTANT]
> **Quiz 1 Scope & Exclusions:** 
> - **Quiz 1 Cutoff:** Quiz 1 strictly ends at **Transistor DC Biasing**.
> - **Quiz 2 Topics:** All content from the end of the previous lecture (**Transistor as a Switch**) onwards—including today's LTSpice AC simulation, small-signal linear amplification, input/output impedance calculations, and loading effects—is **EXCLUDED** from Quiz 1 and reserved exclusively for **Quiz 2**.

> [!NOTE]
> **Quiz 1 Tentative Schedule & Logistics:**
> - **Target Day:** Saturday
> - **Target Time Slot:** Early morning, specifically requested for **8:00 AM – 9:30 AM** (1.5 hours duration).
> - **Room Reservation:** Subject to final confirmation of room availability (professor anticipates no scheduling conflicts). Official announcement to be posted.

---

## 2. BJT Transistor as a Switch (Review & Practical Engineering Design)

### 2.1 Operating Regions & Common Emitter Phase Inversion

When a BJT is used as an electronic switch, it is driven between two non-linear boundary regions:
1. **Cutoff Region (OFF State):**
   - Base current $I_B = 0$, leading to collector current $I_C = 0$.
   - The transistor acts as an open switch; output voltage at the collector rises to supply level: $V_{out} = V_{CE(cutoff)} = V_{CC}$.
2. **Saturation Region (ON State):**
   - Base current $I_B \ge I_{B(min)}$, forcing maximum collector current flow $I_{C(sat)}$.
   - The transistor acts as a closed switch with a small saturation voltage drop: $V_{CE(sat)} \approx 0.3\text{ V}$ (academic standard approximation; worst-case ideal models approximate $V_{CE(sat)} \approx 0\text{ V}$).

```
          VCC (+10V)
           │
          [RC]
           │
  Vin ──[RB]──┤ BJT (Common Emitter Switch)
           │
           ┴ GND
```

#### Phase Inversion in Common Emitter (CE) Configuration
* **Fundamental Characteristic:** Regardless of whether the BJT is operated as a switch (saturation/cutoff) or as a linear amplifier (active region), the Common Emitter (CE) configuration inherently introduces a **$180^\circ$ phase inversion** between the base input signal and collector output signal.
* **Switching Logic:**
  - $V_{in} = \text{HIGH}$ ($V_{in} > V_{BE} + \text{overdrive voltage}$) $\implies$ Base current flows $\implies$ BJT turns ON $\implies V_{out} = V_{CE(sat)} \approx 0.3\text{ V}$ ($\text{LOW}$).
  - $V_{in} = \text{LOW}$ ($V_{in} < 0.7\text{ V}$) $\implies I_B = 0 \implies$ BJT turns OFF $\implies V_{out} = V_{CC}$ ($\text{HIGH}$).
* **Linear Amplification Analogy:** If biased in the active region with a sinusoidal input wave at the base, the output sinusoid at the collector is magnified and inverted by $180^\circ$.

---

### 2.2 Mathematical Formulas for Transistor Inverter Design

1. **Saturated Collector Current ($I_{C(sat)}$):**
   $$I_{C(sat)} = \frac{V_{CC} - V_{CE(sat)}}{R_C} \approx \frac{V_{CC}}{R_C} \quad (\text{assuming } V_{CE(sat)} \approx 0\text{ V})$$

2. **Collector Resistance ($R_C$):**
   $$R_C = \frac{V_{CC} - V_{CE(sat)}}{I_{C(sat)}} \approx \frac{V_{CC}}{I_{C(sat)}}$$
   > [!NOTE]
   > **Professor's Practical Critique:** Slide formulations that say "choose desired $I_{C(sat)}$ from output characteristics" present an artificial academic view. In real-world engineering, $I_{C(sat)}$ and $R_C$ are dictated by the **external load** being switched (or by the required output voltage swing).

3. **Theoretical Minimum Base Current ($I_{B(min)}$):**
   $$I_{B(min)} = \frac{I_{C(sat)}}{\beta}$$

4. **Theoretical Base Resistance ($R_B$):**
   $$R_B = \frac{V_{in} - V_{BE}}{I_{B(min)}} = \frac{V_{in} - 0.7\text{ V}}{I_{B(min)}}$$

---

### 2.3 Real-World Engineering Rule of Thumb: Base Overdrive Factor

> [!WARNING]
> **Datasheet $\beta$ ($h_{FE}$) Pitfall & Professor Warning:**
> Designing a transistor switch using the published datasheet $\beta$ (also labeled $h_{FE}$) at exact $I_{B(min)}$ is dangerous engineering practice.
> - **Datasheet $\beta$ is Peak/Optimal:** Manufacturers report $\beta$ measured under ideal, optimal test conditions at the peak of the $\beta$ vs. $I_C$ curve.
> - **$\beta$ Non-Linearity:** Transistor $\beta$ is not constant! It tumbles downward at both low currents and high saturation currents, and varies significantly across individual device production batches and operating temperatures.

#### Professor's Practical Switching Rule:
* **Overdrive Base Current by at least $2\times$:** Always push **at least twice** the calculated theoretical minimum base current into the base ($I_{B(practical)} \ge 2 \times I_{B(min)}$) to guarantee deep saturation ("saturation insurance").
* **Halve the Base Resistance:**
  $$R_{B(practical)} \le \frac{R_{B(theoretical)}}{2}$$
* **Transistor Safety:** Injecting extra base current (e.g., $80\ \mu\text{A}$ or $100\ \mu\text{A}$) does **not** harm the transistor, provided the driving source can deliver the small additional current.

---

### 2.4 Worked Numerical Example: Transistor Inverter Design

**Given Parameters from Slide:**
- Saturation Collector Current target: $I_{C(sat)} = 10\text{ mA}$
- Supply Voltage: $V_{CC} = 10\text{ V}$
- Input Signal High Level: $V_{in} = 10\text{ V}$
- Transistor Beta: $\beta = 250$ *(noted by professor as missing from main slide face, uncovered on subsequent slide)*
- Base-Emitter Drop: $V_{BE} = 0.7\text{ V}$

**Step-by-Step Calculations:**

1. **Calculate Collector Resistor ($R_C$):**
   $$R_C = \frac{V_{CC}}{I_{C(sat)}} = \frac{10\text{ V}}{10\text{ mA}} = 1\text{ k}\Omega$$

2. **Calculate Theoretical Minimum Base Current ($I_{B(min)}$):**
   $$I_{B(min)} = \frac{I_{C(sat)}}{\beta} = \frac{10\text{ mA}}{250} = 40\ \mu\text{A}$$

3. **Calculate Theoretical Base Resistor ($R_{B(theoretical)}$):**
   $$R_{B(theoretical)} = \frac{V_{in} - V_{BE}}{I_{B(min)}} = \frac{10\text{ V} - 0.7\text{ V}}{40\ \mu\text{A}} = \frac{9.3\text{ V}}{40\ \mu\text{A}} = 232.5\text{ k}\Omega \quad (\text{Slide rounds to } 230\text{ k}\Omega)$$

4. **Apply Practical Engineering Design Rule ($2\times I_B$ Overdrive):**
   - Practical Base Current Target:
     $$I_{B(practical)} = 2 \times 40\ \mu\text{A} = 80\ \mu\text{A}$$
   - Practical Base Resistor Target:
     $$R_{B(practical)} = \frac{9.3\text{ V}}{80\ \mu\text{A}} = 116.25\text{ k}\Omega$$
   - **Commercial Component Selection:** Since $230\text{ k}\Omega$ and $116.25\text{ k}\Omega$ do not exist in standard E-series resistor values (standard commercial value is $220\text{ k}\Omega$ or $110\text{ k}\Omega$), the professor explicitly recommends selecting a standard **$100\text{ k}\Omega$** base resistor in real-world implementations.

> [!NOTE]
> **Classroom Exchange Tangent (Prof. & Mr. Infante):**
> When student Mr. Infante asked for confirmation regarding halving $R_B$, the professor emphasized (partly in Tagalog): *"Oh my God. I have to repeat myself... Mr. Infante, makinig ka ng mabuti. Sinabi ko na..."* Re-explaining that assumed datasheet $\beta = 250$ cannot be counted on in practice; actual operational $\beta$ can easily drop to half or less, making $R_B / 2$ ($100\text{ k}\Omega$) essential for reliable switching.

---

## 3. LTSpice Simulation & AC Linear Amplification Walkthrough

### 3.1 Voltage-Divider Biased Common Emitter Circuit Setup

To demonstrate active region linear amplification, the professor constructed and simulated a standard voltage-divider biased BJT amplifier circuit in LTSpice.

```
                  VCC (+22V)
                   │
           ┌───────┴───────┐
          [R1]            [RC] (10k)
         (39k)             │
           │     Cin       ├─────── Cout ──┐
Vin ───┤├───┬───┤ BJT      │ (10uF)        │
(1kHz) (10uF) │  (2N2222) ├───────┐       [RL] (100k)
              [R2]         │      [CE]      │
             (3.9k)       [RE]   (100uF)    ┴ GND
              │          (1.5k)   │ (Case 2/3)
              ┴────────────┴──────┴───────── GND
```

#### Exact Component Values & Models:
- **Supply Voltage ($V_{CC}$):** $22\text{ V}$ DC
- **Voltage Divider Bias:** $R_1 = 39\text{ k}\Omega$, $R_2 = 3.9\text{ k}\Omega$
- **Collector Resistor ($R_C$):** $10\text{ k}\Omega$
- **Emitter Resistor ($R_E$):** $1.5\text{ k}\Omega$
- **Transistor Part Number:** **2N2222** NPN BJT *(Professor: "All-time favorite 2N2222, readily available." LTSpice model $\beta = 200$, compared to slide problem default $\beta = 140$).*
- **Coupling Capacitors:** $C_{in} = 10\ \mu\text{F}$, $C_{out} = 10\ \mu\text{F}$
- **Output Load Resistor ($R_L / R_5$):** $100\text{ k}\Omega$ (pull-down load added to allow AC coupling capacitor settling).
- **Input Signal Source:** Sinusoidal input voltage with angular frequency $\omega = 2000\pi\text{ rad/s} \implies f = \frac{2000\pi}{2\pi} = 1000\text{ Hz} = 1\text{ kHz}$.

---

### 3.2 DC Operating Point (`.op`) Simulation Results

Before applying AC signals, a DC operating point command (`.op`) was executed to evaluate the Q-point:
- **Collector Current ($I_{CQ}$):** $\approx 8\text{ mA}$
- **DC Collector Node Voltage ($V_C$):** $\approx 13.0\text{ V} - 13.2\text{ V}$
- **DC Emitter Node Voltage ($V_E$):** $\approx 1.3\text{ V}$
- **DC Collector-Emitter Voltage ($V_{CEQ}$):** $V_C - V_E = 13.2\text{ V} - 1.3\text{ V} \approx 11.9\text{ V}$
- **Biasing Evaluation:** With $V_C \approx 13.2\text{ V}$ relative to $V_{CC} = 22\text{ V}$, the transistor Q-point sits close to the realistic center of the voltage swing, confirming stable **Active Region** operation for linear amplification.

---

### 3.3 Comparative AC Performance: Transient Analysis (`.tran`)

To eliminate transient capacitor charging effects, transient simulation settings were specified as:
- **Start viewing time:** $500\text{ ms}$
- **Stop viewing time:** $510\text{ ms}$
- **Observed Duration:** $10\text{ ms}$ window, displaying exactly **10 complete cycles** of the $1\text{ kHz}$ signal.

#### Case 1: Unbypassed Emitter Resistor ($C_E$ omitted)
- **Input Signal:** $V_{\text{in}} = 0.2\text{ V}_{\text{peak}} \implies 0.4\text{ V}_{\text{peak-to-peak}}$ ($\pm 0.2\text{ V}$).
- **Output Signal:** $V_{\text{out}} = 1.2\text{ V}_{\text{peak}} \implies 2.4\text{ V}_{\text{peak-to-peak}}$ ($\pm 1.2\text{ V}$, AC-coupled centered at $0\text{ V}$).
- **Collector Node Swing (DC + AC):** Swings between lower peak $11.95\text{ V}$ and upper peak $14.30\text{ V}$ around DC Q-point $13.15\text{ V}$.
- **Voltage Gain Calculation ($A_v$):**
  $$A_v = \frac{V_{\text{out(p-p)}}}{V_{\text{in(p-p)}}} = \frac{2.4\text{ V}_{\text{p-p}}}{0.4\text{ V}_{\text{p-p}}} = 6$$
- **Waveform Characteristics:** Modest amplification factor ($A_v = 6$) due to negative feedback/degeneration from unbypassed $R_E$. Waveform maintains high linearity with negligible visual distortion.

#### Case 2: Bypassed Emitter Resistor ($C_E = 100\ \mu\text{F}$) — Overdriven Condition
Adding bypass capacitor $C_E = 100\ \mu\text{F}$ across $R_E = 1.5\text{ k}\Omega$ shorts AC signals to ground, removing emitter degeneration and driving AC voltage gain upward dramatically.
- **Input Signal:** $V_{\text{in}} = 0.4\text{ V}_{\text{p-p}}$ ($\pm 0.2\text{ V}$).
- **Result:** Amplifier is heavily **overdriven** into extreme non-linear clipping:
  - Driven into **Saturation:** Collector voltage hits lower limit $V_C \approx 1.5\text{ V} - 1.6\text{ V}$ (constrained by $V_E \approx 1.3\text{ V} + V_{CE(sat)}$).
  - Driven into **Cutoff:** Collector voltage hits upper limit near supply rail $V_{CC} = 22\text{ V}$.
  - Sinusoidal fidelity is completely destroyed (square-wave-like clipping).

#### Case 3: Bypassed Emitter Resistor ($C_E = 100\ \mu\text{F}$) — Small-Signal Input
Input signal amplitude reduced by a factor of 10 to keep output within linear boundaries:
- **Input Signal:** $V_{\text{in}} = 0.02\text{ V}_{\text{peak}} \implies 0.04\text{ V}_{\text{peak-to-peak}} = 40\text{ mV}_{\text{p-p}}$.
- **Output Signal Swing:** Upper peak at $+4.6\text{ V}$, lower peak at $-6.3\text{ V}$ relative to DC.
  $$V_{\text{out(p-p)}} = 4.6\text{ V} + 6.3\text{ V} = 10.9\text{ V}_{\text{p-p}}$$
- **Voltage Gain Calculation ($A_v$):**
  $$A_v = \frac{10.9\text{ V}_{\text{p-p}}}{0.04\text{ V}_{\text{p-p}}} = 272.5$$
- **Gain Comparison:** Adding $C_E$ boosted AC voltage gain from **$A_v = 6$** up to **$A_v = 272.5$**!

---

### 3.4 Detailed Non-Linearity & Distortion Analysis

Comparing the $A_v = 272.5$ output against an ideal reference sinusoid (magnified input by $272\times$):
* **Asymmetrical Distortion:** The top half of the output sinusoid appears wide/squashed while the bottom half appears narrow.
* **Device Physics Cause:** Transistor $\beta$ is not constant over large collector current ($I_C$) excursions. As large AC signal swings cause $I_C$ to vary dynamically, the instantaneous transistor $\beta$ shifts, causing the effective instantaneous AC gain to change across different phases of the AC cycle.
* **Engineering Trade-off:** High stage gain pushes transistor operation closer to supply limits, increasing non-linear harmonic distortion.

---

## 4. BJT AC Analysis Fundamentals & Impedance Loading Effects

### 4.1 Four Primary Amplifier AC Parameters
1. **Voltage Gain ($A_v$):** $A_v = \frac{V_{out}}{V_{in}}$
2. **Current Gain ($A_i$):** $A_i = \frac{I_{out}}{I_{in}}$ (prominent in Common Collector / Emitter Follower configurations).
3. **Input Impedance ($Z_{in}$ or $R_{in}$):** Equivalent AC resistance seen by a signal source looking into the amplifier input.
4. **Output Impedance ($Z_{out}$ or $R_{out}$):** Equivalent AC resistance looking back into the amplifier output terminals.

> [!TIP]
> **Impedance vs. Resistance Intuition:**
> Impedance ($Z$) is simply the complex frequency-dependent generalization of resistance ($R$). If struggling conceptually with impedance, substitute the term with **AC input/output resistance**.

---

### 4.2 Amplification & Loading Effect Circuit Model

Amplification is formally defined as increasing the **power** ($P = V \times I$) of an AC signal. Real signal sources possess internal source resistance ($R_s$), and real load devices possess finite load resistance ($R_L$).

```
      Signal Source              Amplifier Stage              Load
   ┌─────────────────┐   ┌──────────────────────────┐   ┌─────────────┐
   │       Rs        │   │  Zin            Zout     │   │     RL      │
   ├──────[====]─────┼───┼───┐          ┌──[====]───┼───┼────[====]───┤
   │                 │   │  [ ]         │           │   │             │
  (~) Vs             │   │  [ ] Zin  (^) Av*Vin     │   │ VL (Vout)   │
   │                 │   │   │          │           │   │             │
   └─────────────────┴───┴───┴──────────┴───────────┴───┴─────────────┘
```

#### Three-Step Loading Math Equations:
1. **Input Voltage Attenuation (Voltage Division at Input):**
   $$V_{in} = V_s \times \left( \frac{Z_{in}}{Z_{in} + R_s} \right)$$
2. **Internal Core Amplification:**
   $$V_{internal} = A_{v(open)} \times V_{in}$$
3. **Output Voltage Attenuation (Voltage Division at Output Load):**
   $$V_L = V_{out} = V_{internal} \times \left( \frac{R_L}{R_L + Z_{out}} \right)$$

---

### 4.3 Comprehensive Worked Example: Loaded Voltage Gain

**Given Slide Parameters:**
- **Signal Source:** $V_s = 1.0\text{ V}_{\text{p-p}}$, Source Resistance $R_s = 10\text{ k}\Omega$
- **Amplifier Characteristics:** Open-circuit Voltage Gain $A_{v(open)} = 150$, Input Impedance $Z_{in} = 1\text{ k}\Omega$, Output Impedance $Z_{out} = 1\text{ k}\Omega$
- **External Load:** $R_L = 500\ \Omega$

**Step-by-Step Loaded Gain Calculation:**

1. **Calculate Actual Input Voltage ($V_{in}$) after Source Loading:**
   $$V_{in} = 1.0\text{ V}_{\text{p-p}} \times \left( \frac{1\text{ k}\Omega}{1\text{ k}\Omega + 10\text{ k}\Omega} \right) = \frac{1}{11}\text{ V}_{\text{p-p}} \approx 0.09091\text{ V}_{\text{p-p}} = 90.91\text{ mV}_{\text{p-p}}$$
   *(Input signal is attenuated by a factor of 11 before reaching the amplifier core).*

2. **Calculate Internal Amplified Voltage ($V_{internal}$):**
   $$V_{internal} = 150 \times 90.91\text{ mV}_{\text{p-p}} = 13.636\text{ V}_{\text{p-p}} \approx 13.64\text{ V}_{\text{p-p}}$$

3. **Calculate Final Output Voltage Across Load ($V_L$):**
   $$V_L = 13.64\text{ V}_{\text{p-p}} \times \left( \frac{500\ \Omega}{500\ \Omega + 1000\ \Omega} \right) = 13.64\text{ V}_{\text{p-p}} \times \frac{1}{3} \approx 4.54\text{ V}_{\text{p-p}}$$

4. **Calculate Effective Loaded Voltage Gain ($A_{v(loaded)}$):**
   $$A_{v(loaded)} = \frac{V_L}{V_s} = \frac{4.54\text{ V}_{\text{p-p}}}{1.0\text{ V}_{\text{p-p}}} = 4.54$$

> [!IMPORTANT]
> **Critical Engineering Takeaway:**
> Due to input and output impedance mismatches ($R_s \gg Z_{in}$ and $Z_{out} > R_L$), the nominal open-circuit gain of **150** drops severely to an effective overall loaded gain of only **4.54**!

---

### 4.4 Summary Table: Ideal vs. Real Amplifier Characteristics

| Property | Ideal Amplifier | Real BJT Amplifier Stage | Engineering Goal / Design Impact |
| :--- | :--- | :--- | :--- |
| **Input Impedance ($Z_{in}$)** | $\infty$ (Infinite) | Finite (typically $1\text{ k}\Omega - 10\text{ k}\Omega$) | Maximize $Z_{in}$ to prevent loading the source signal |
| **Output Impedance ($Z_{out}$)**| $0\ \Omega$ (Zero) | Finite (typically $100\ \Omega - 10\text{ k}\Omega$) | Minimize $Z_{out}$ to prevent voltage drop under load |
| **Voltage Gain ($A_v$)** | High / Constant | Dependent on $R_C, R_E, C_E, I_C$ | Achieve targeted amplification with stability |
| **Bandwidth** | $\infty$ (Infinite) | Limited by coupling & parasitic caps | Maintain flat, constant gain across operating range |
| **Gain Flatness** | Constant across all $f$ | Varies with frequency | Ensure uniform gain across target frequency band |
| **Linearity / Fidelity** | Perfect (0% Distortion) | Non-linear at large swings | **Professor's added point:** Maintain exact input shape |

---

## 5. Preview: BJT Small-Signal AC Equivalent Models

To analytically calculate stage gain, $Z_{in}$, and $Z_{out}$ without reliance on computer software, BJTs are replaced by small-signal AC equivalent models:
- **Two-Port Hybrid Model ($h$-parameter model):** 
  - Historical standard utilizing four parameters: $h_{fe}$ ($\beta$), $h_{ie}$ ($R_{in}$), $h_{re}$ (reverse voltage feedback ratio), $h_{oe}$ (output admittance).
  - Involves long algebraic matrix modeling (to be defined conceptually in the next lecture for historical completeness).
- **Computer Simulation (LTSpice):** Modern industry standard that yields fast, comprehensive, and highly accurate non-linear AC results.
