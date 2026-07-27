# Lecture 14 Notes: Small-Signal BJT Amplifier Modeling ($r_e$ Model), AC Analysis & Tangents/Trivia

---

## Executive Summary & Overview

This lecture covers small-signal AC analysis and modeling of Bipolar Junction Transistors (BJTs), focusing on transitioning from DC operating point determination ($Q$-point) to evaluating dynamic AC performance. The primary model examined for hand calculations is the **Emitter Resistance ($r_e$) Model** applied to Common Emitter (CE) amplifier configurations. 

In addition to core circuit formulations, this lecture details:
1. **Transistor Equivalent Models**: Comparing the Hybrid-$\pi$ model ($h$-parameters), the $r_e$ model, and computer SPICE models (Gummel-Poon).
2. **Deep Physical Mechanics**: Dynamic emitter junction behavior, thermal voltage ($V_T = 26\text{ mV}$), and high-power transistor limitations (e.g., 2N3055, 2N3771).
3. **Conceptual Foundations**: Frequency continuum (DC vs. AC), capacitor reactance behavior, and why $+V_{CC}$ supplies act as AC ground (Meralco power supply backstory).
4. **CE AC Performance Calculations**: Input impedance ($Z_{in}$), output impedance ($Z_o$), voltage gain ($A_v$), and phase inversion.
5. **Source & Multistage Loading Constraints**: Degradation under source resistance ($R_S = 50\,\Omega$ vs. $600\,\Omega$) and the "harsh reality" of cascading identical CE amplifier stages.
6. **Exam Tips & LTspice Assignment**: Practical warnings regarding biased saturation/cutoff traps in exams and LTspice numerical validation setup.

---

## 1. Class Logistics, Logistics Tangents & Professor Context

- **Post-Break Context**: The lecture resumed after Independent Learning Week (ILW) and several preceding holidays. The professor noted needing to review the previous Canvas lecture recording prior to class just to confirm where the course had stopped.
- **Slide Setup & Tech Commentary**: The professor encountered minor trouble setting up the presentation display on-screen ("*Oh, nope, not that one. What the hell? I already had it on the correct page...*").
- **Weather & Temperature Side Comment**: The professor started an electric fan during the lecture, commenting on unseasonal heat: "*The last two days, the temperature was just like it was during summer, which is crazy.*"
- **Prior Lecture Recap**: The previous session concluded while discussing high-bandwidth amplifier characteristics, frequency response, and input/output impedance parameters.

---

## 2. Transistor Equivalent Models Comparison

To analyze BJT AC performance numerically, physical transistors are replaced by equivalent small-signal models that represent their terminal behavior at a specific DC operating point ($Q$-point).

```
                  ┌───────────────────────────────────────────────┐
                  │          Transistor AC Modeling               │
                  └───────────────────────┬───────────────────────┘
                                          │
        ┌─────────────────────────────────┼─────────────────────────────────┐
        ▼                                 ▼                                 ▼
┌───────────────┐                 ┌───────────────┐                 ┌───────────────┐
│ Hybrid-π      │                 │ r_e Model     │                 │ SPICE /       │
│ Model         │                 │ (Emitter Res) │                 │ Gummel-Poon   │
└───────┬───────┘                 └───────┬───────┘                 └───────┬───────┘
        │                                 │                                 │
        ├─ Industry standard              ├─ Simplified hand calculations   ├─ Numerical simulation
        ├─ 2-port model (H-params)        ├─ Diode + current source         ├─ 10-15+ parameters
        ├─ Captures input/output coupling ├─ Low frequency approximation     ├─ Captures parasitics,
        └─ Complex for hand math          └─ Sensitive to DC operating point   temp, non-linearities
```

### 2.1 The Hybrid-$\pi$ Model ($h$-parameters)
- **Industry Standard**: Accepted by semiconductor manufacturers for datasheet specifications.
- **Two-Port Topology**: Interconnects input (Base) and output (Collector/Emitter) ports to capture how output load conditions directly affect input characteristics and vice versa.
- **Datasheet Terminology**: Datasheets list parameters such as $h_{fe}$, $h_{oe}$, $h_{ie}$, and $h_{re}$. 
  - The parameter **$h_{fe}$** corresponds directly to the AC current gain $\beta_{ac}$ ($\frac{i_c}{i_b}$).
  - For example, looking up a standard small-signal transistor like the **2N2222** reveals $h_{fe}$ listed instead of $\beta$. The prefix **'h'** explicitly stands for **Hybrid**.
- **Programming Analogy (Assembly Language)**: 
  - Using the Hybrid-$\pi$ model for manual calculation is analogous to programming a CPU in **assembly language**—it provides detailed parameters and explicit timing/resource control, but is unnecessarily complex for quick estimations.

### 2.2 The Emitter Resistance ($r_e$) Model
- **High-Level Language Analogy**: Equivalent to writing code in a high-level language. It captures the essential operating behavior without overwhelming mathematical complexity.
- **Simplified Topology**: Replaces the BJT with a base-emitter diode and a collector current source.

### 2.3 Computer Simulation Models (SPICE / Gummel-Poon)
- When high accuracy is required across wide frequency ranges or operating conditions, computer simulation tools (e.g., **LTspice**) are used.
- SPICE transistor models (such as the **Gummel-Poon model**) utilize **10 to 15+ parameters** (e.g., $I_S, \beta_F, V_A, C_{JE}, C_{JC}, \tau_F$), whereas the Hybrid-$\pi$ model uses 4–5 parameters and the $r_e$ model uses 2–3 parameters.

| Parameter / Feature | **Hybrid-$\pi$ Model ($h$-parameters)** | **$r_e$ Model (Emitter Resistance)** | **SPICE / Gummel-Poon Model** |
| :--- | :--- | :--- | :--- |
| **Primary Use** | Datasheets, high-frequency modeling | Quick hand calculations & estimations | Computer CAD / LTspice simulations |
| **Complexity** | Medium–High (2-port network parameters) | Low (Diode + controlled current source) | High (10–15+ parameters) |
| **Key Parameters** | $h_{fe}$ ($\beta$), $h_{ie}$, $h_{oe}$, $h_{re}$ | $r_e$ ($26\text{ mV}/I_E$), $\beta_{ac}$ | $I_S, \beta_F, V_A, C_{JE}, C_{JC}, \tau_F$, etc. |
| **Limitations** | Complex interconnects between input/output | Low-frequency only; ignores parasitic $C$ | Requires simulation software tool |

---

## 3. The Emitter Resistance ($r_e$) Model & Physical Device Mechanics

### 3.1 Model Topology
The $r_e$ model approximates the BJT as:
- **Input side (Base–Emitter junction)**: Modeled as a forward-biased diode with dynamic AC junction resistance $r_e$.
- **Output side (Collector–Base/Emitter junction)**: Modeled as a controlled current source delivering $I_c = \beta I_b$.

```
Base (B) o─────────────┐
                       │
                      ┌┴┐
                      │ │ r_e (Dynamic emitter resistance)
                      └┬┘
                       │
                       ├───────────────o Emitter (E)
                       │
                      ─┴─
                      ▲  I_c = β * I_b (Controlled Current Source)
                     / \
                    └───┘
                       │
Collector (C) o────────┴─
```

### 3.2 Dynamic Emitter Resistance ($r_e$) Derivation
The small-signal dynamic resistance of the forward-biased base-emitter diode junction is derived from the slope of the $I-V$ diode characteristic curve at the DC bias point:

$$r_e = \frac{V_T}{I_E} = \frac{26\text{ mV}}{I_E}$$

*Where $V_T \approx 26\text{ mV}$ is the thermal voltage at room temperature ($300\text{ K}$).*

> [!NOTE]
> **Professor Joke / Anecdote**: Regarding the derivation of the $26\text{ mV}$ thermal voltage factor from semiconductor device physics, the professor noted: "*We're just going to stick with this and say amen for now.*"

> [!IMPORTANT]
> Because $r_e$ is inversely proportional to $I_E$, an accurate **DC bias analysis** must be performed **prior** to any AC small-signal calculation.

### 3.3 Current Approximations & High-Power Transistor Limitations

For standard small-signal BJTs (such as the **2N2222**) operating in regions favoring high current gain ($\beta = 70, 100, 200+$):

$$I_E = I_C + I_B = (\beta + 1)I_B \approx \beta I_B \implies I_E \approx I_C$$

#### High-Power Transistor Tangent & Specific Device Part Numbers
The approximation $I_E \approx I_C$ **fails** for high-power transistors. High-power transistors designed for heavy current operation and high power dissipation—housed in large **TO-3 metal packages**—exhibit significantly lower $\beta$ values.

Specific power transistor part numbers cited by the professor:
1. **2N3055**: Classic TO-3 power BJT.
2. **2N3771**: Heavy-duty TO-3 power BJT with extremely low gain ($\beta \approx 25 \text{ to } 30$).

> [!WARNING]
> **High-Power Transistor Caveat**: For power devices like the 2N3771 with $\beta \approx 25$, $I_B$ accounts for nearly $4\%$ of $I_E$. Dropping the $+1$ term or assuming $I_E \approx I_C$ introduces significant calculation errors. The approximation is valid **only** for small-signal transistors.

---

## 4. Deep Conceptual Mechanics: DC vs. AC, Frequency Continuum, and Power Supply AC Ground

### 4.1 The DC vs. AC Frequency Continuum

The distinction between DC and AC analysis is often presented as two rigid, isolated domains. In physical reality, DC and AC are points along a continuous frequency spectrum.

```
Frequency Spectrum:
0 Hz (Pure DC) ────────► 0.001 Hz ────► 0.1 Hz ────► 1 Hz ────► 1 kHz (High Frequency AC)
[X_C = ∞ (Open)]                                              [X_C ≈ 0 (Short)]
```

Professor Quotes:
- "*An AC signal is a DC signal that is wiggling (amplitude-wise).*"
- "*A DC signal is an AC signal whose frequency is so low that we don't perceive it as a signal that is changing.*"

#### Frequency Progression & Capacitor Reactance
Consider a sinusoidal signal decreasing in frequency:
$$10\text{ Hz} \longrightarrow 2\text{ Hz} \longrightarrow 1\text{ Hz} \longrightarrow 0.5\text{ Hz} \longrightarrow 0.1\text{ Hz} \longrightarrow 0.01\text{ Hz} \longrightarrow 0.001\text{ Hz}$$

A $0.001\text{ Hz}$ waveform changes so slowly over observation intervals that it is functionally indistinguishable from DC.

Capacitive reactance is defined as:
$$X_C = \frac{1}{j\omega C} = \frac{1}{j 2\pi f C}$$

*Reactance $X_C$ can be conceptualized as resistance shifted by 90° in the complex polar plane.*

- At **DC ($f = 0\text{ Hz}$)**: $X_C = \frac{1}{0} \to \infty\,\Omega \implies \text{Open Circuit}$.
- At **AC ($f \gg 0\text{ Hz}$)**: $X_C \to 0\,\Omega \implies \text{Short Circuit}$ (assuming $f$ is sufficiently high relative to $C$).

### 4.2 Power Supply AC Ground Tangent (Meralco Grid Backstory)

In AC equivalent schematics, the DC supply rail ($+V_{CC}$) is drawn connected directly to **AC Ground**.

```
AC Power Supply Path:
220V AC (Meralco Grid) ──► Step-Down Transformer ──► Full-Wave Bridge Rectifier
                                                               │
                                                       Pulsing DC Output
                                                               │
                                                    [Smoothing Filter Cap] (Connected to Ground)
                                                               │
                                                       Steady 12V DC Rail
```

#### Why $+V_{CC}$ is an AC Ground:
1. Converting $220\text{V AC}$ (Meralco grid) to a $12\text{V DC}$ supply requires a step-down transformer followed by a full-wave bridge rectifier.
2. Rectification produces pulsing DC. To smooth out voltage ripple, a large **filter capacitor** is placed in parallel between the $12\text{V}$ output rail and Ground.
3. Since capacitors act as short circuits to AC signals, the large smoothing capacitor in the power supply provides a direct, low-impedance short circuit to ground for all AC signals on the $+V_{CC}$ rail.
4. Consequently, every node connected to $+V_{CC}$ is at **AC Ground** potential.

---

## 5. Common Emitter (CE) AC Analysis & Governing Formulations

### 5.1 Phase Inversion (180° Phase Shift)
The Common Emitter amplifier exhibits a **180° phase inversion** between input and output signals.

```
Input Signal (V_in):   ───/\───\/───  (0° Phase)
Output Signal (V_o):   ───\/───/\───  (180° Phase Shifted & Amplified)
```

#### Physical Mechanism:
1. As $V_{in}$ rises on the positive half-cycle, Base voltage $V_B$ increases.
2. Increasing $V_B$ increases Base current $I_B$, which increases Collector current $I_C = \beta I_B$.
3. Higher $I_C$ increases the voltage drop across the collector resistor: $V_{RC} = I_C R_C \uparrow$.
4. The output collector voltage is $V_o = V_C = V_{CC} - V_{RC}$. As $V_{RC}$ increases, $V_C$ decreases $\implies V_o$ drops negative.

### 5.2 Governing $r_e$ Model Formulations for CE Amplifiers

1. **Base AC Resistance ($R_{in(base)}$)**:
   $$R_{in(base)} = \beta_{ac} \cdot r_e$$

2. **Total Stage Input Impedance ($Z_{in}$)**:
   $$Z_{in} = R_1 \parallel R_2 \parallel R_{in(base)} = R_1 \parallel R_2 \parallel (\beta_{ac} \cdot r_e)$$

3. **Stage Output Impedance ($Z_o$)**:
   $$Z_o = R_C \quad (\text{assuming internal output resistance } r_o = \infty)$$
   $$\text{With external load } R_L: \quad Z_o' = R_C \parallel R_L$$

4. **Unloaded Theoretical Voltage Gain ($A_v$)**:
   $$A_v = \frac{V_o}{V_i} = -\frac{R_C}{r_e} \implies |A_v| = \frac{R_C}{r_e}$$

5. **Current Gain ($A_i$)**:
   $$A_i \approx \beta_{ac}$$

---

## 6. Step-by-Step Worked Circuit Problem

### 6.1 Circuit Schematic & Given Values

```
          +V_CC = 12V
            │
      ┌─────┴─────┐
      │           │
     [R1]        [RC]
    22kΩ        1kΩ
      │           │           C3
      ├─────┬─────┤───────────||─────────o V_out
      │     │     │
     [R2]   │   |/ C
    6.8kΩ   └───|  BJT (β_dc=150, β_ac=160)
      │         |\ E
      │           │
  C1  │           ├───────────┐
──||──┴┐          │           │
       │         [RE]        [C2]  (Bypass Cap)
       o V_in    560Ω         │
                  │           │
                 ─┴─         ─┴─ Ground
```

- **Supply Voltage**: $V_{CC} = 12\text{ V}$
- **Resistors**: $R_1 = 22\text{ k}\Omega$, $R_2 = 6.8\text{ k}\Omega$, $R_E = 560\ \Omega$, $R_C = 1\text{ k}\Omega$
- **Transistor Parameters**: $\beta_{dc} = 150$, $\beta_{ac} = 160$
- **Capacitors**: $C_1, C_3$ (Input/Output coupling), $C_2$ (Emitter bypass to ground)

---

### 6.2 Step 1: DC Operating Point Analysis ($Q$-Point & $I_E$)

#### Approximate Analysis Test:
$$\beta_{dc} \cdot R_E = 150 \times 560\ \Omega = 84\text{ k}\Omega$$
$$10 \cdot R_2 = 10 \times 6.8\text{ k}\Omega = 68\text{ k}\Omega$$

Since $84\text{ k}\Omega \ge 68\text{ k}\Omega$, approximate DC analysis is **valid**.

#### Base Voltage ($V_B$):
$$V_B = V_{CC} \cdot \left(\frac{R_2}{R_1 + R_2}\right) = 12\text{ V} \cdot \left(\frac{6.8\text{ k}\Omega}{22\text{ k}\Omega + 6.8\text{ k}\Omega}\right) = 12 \cdot 0.2361 = 2.833\text{ V}$$

#### Emitter Voltage ($V_E$):
$$V_E = V_B - 0.7\text{ V} = 2.833\text{ V} - 0.7\text{ V} = 2.133\text{ V}$$

#### Emitter Current ($I_E$):
$$I_E = \frac{V_E}{R_E} = \frac{2.133\text{ V}}{560\ \Omega} \approx 3.81\text{ mA}$$

#### Collector Current ($I_C$) & Active Region Verification:
$$I_C \approx I_E = 3.81\text{ mA}$$
$$V_{RC} = I_C \cdot R_C = 3.81\text{ mA} \times 1\text{ k}\Omega = 3.81\text{ V}$$
$$V_C = V_{CC} - V_{RC} = 12\text{ V} - 3.81\text{ V} = 8.19\text{ V}$$
$$V_{CE} = V_C - V_E = 8.19\text{ V} - 2.133\text{ V} = 6.057\text{ V} \approx 6.06\text{ V}$$

> [!NOTE]
> Since $V_{CE} \approx 6.06\text{ V} \gg V_{CE(sat)} (\approx 0.2\text{ V})$, the transistor operates safely in the **Active Region** and is properly biased to amplify AC signals.

---

### 6.3 Step 2: Dynamic Emitter Resistance ($r_e$)

$$r_e = \frac{26\text{ mV}}{I_E} = \frac{26\text{ mV}}{3.81\text{ mA}} = 6.824\ \Omega \approx 6.83\ \Omega$$

---

### 6.4 Step 3: Small-Signal Impedance Calculations

#### Base AC Input Resistance ($R_{in(base)}$):
$$R_{in(base)} = \beta_{ac} \cdot r_e = 160 \times 6.83\ \Omega = 1092.8\ \Omega \approx 1.092\text{ k}\Omega$$

#### Total Input Impedance ($Z_{in}$):
$$Z_{in} = R_1 \parallel R_2 \parallel R_{in(base)} = 22\text{ k}\Omega \parallel 6.8\text{ k}\Omega \parallel 1.092\text{ k}\Omega$$

$$\frac{1}{Z_{in}} = \frac{1}{22000} + \frac{1}{6800} + \frac{1}{1092.8} \implies Z_{in} \approx 902.3\ \Omega$$

> [!IMPORTANT]
> **Low $Z_{in}$ Observation**: Bypassing $R_E$ with capacitor $C_2$ shorts $R_E$ to ground at AC, yielding high voltage gain but resulting in a low input impedance ($Z_{in} \approx 902.3\ \Omega$). 
> 
> Even if a transistor with a higher beta is used (e.g., lab 2N2222 units with $\beta_{dc} \approx 200 - 250$), $Z_{in}$ would only increase to roughly $2\text{ k}\Omega$, which remains relatively low for standard voltage amplifiers.

#### Output Impedance ($Z_o$):
Without an external load attached ($R_L = \infty$):
$$Z_o = R_C = 1\text{ k}\Omega$$

---

### 6.5 Step 4: Unloaded Theoretical Voltage Gain ($A_v$)

$$A_v = \frac{R_C}{r_e} = \frac{1000\ \Omega}{6.83\ \Omega} \approx 146.41$$

---

## 7. Source Resistance ($R_S$) Degradation & Loading Effects

When connected to a physical signal source $V_S$ with internal source resistance $R_S$, a voltage divider is formed between $R_S$ and the stage input impedance $Z_{in}$.

```
Signal Source               Amplifier Input
  o───[ R_S ]─────┬─────────o (Base)
  │               │
 (~) V_S         [Z_in] (902.3 Ω)
  │               │
  o───────────────┴─────────o Ground
```

$$\text{Base Input Voltage: } V_{in} = V_S \cdot \left(\frac{Z_{in}}{Z_{in} + R_S}\right)$$
$$\text{Loaded System Gain: } A_{v, loaded} = A_v \cdot \left(\frac{Z_{in}}{Z_{in} + R_S}\right)$$

### 7.1 Case A: Low Source Resistance ($R_S = 50\,\Omega$)
- **Attenuation Factor**: $\frac{902.3}{902.3 + 50} = 0.9475$
- **Loaded System Gain**: $A_{v, loaded} = 146.41 \times 0.9475 = \mathbf{138.7} \approx 138$
- **Amplification Example**:
  For an input signal $V_S = 20\text{ mV}_{p-p}$:
  $$V_{out(p-p)} = 20\text{ mV}_{p-p} \times 138.7 = \mathbf{2.76\text{ V}_{p-p}} \quad (\text{180° inverted})$$

### 7.2 Case B: Higher Source Resistance ($R_S = 600\,\Omega$)
For standard function generators or audio sources with $R_S = 600\ \Omega$:
- **In-Class Approximation**: $\frac{900}{900 + 600} = 0.60$
- **Exact Attenuation Factor**: $\frac{902.3}{902.3 + 600} = 0.6006$
- **Loaded System Gain**: $A_{v, loaded} = 146.41 \times 0.6006 = \mathbf{87.9}$ (or $87.6$ via class approximation)

| Parameter / Condition | **Low Source Resistance ($R_S = 50\,\Omega$)** | **High Source Resistance ($R_S = 600\,\Omega$)** |
| :--- | :--- | :--- |
| **Input Attenuation Factor** | $\frac{902.3}{902.3 + 50} = 0.9475$ | $\frac{902.3}{902.3 + 600} = 0.6006$ |
| **Loaded Voltage Gain ($A_{v, loaded}$)** | $146.41 \times 0.9475 = \mathbf{138.7}$ | $146.41 \times 0.6006 = \mathbf{87.9}$ |
| **Output for $V_S = 20\text{ mV}_{p-p}$** | $2.76\text{ V}_{p-p}$ | $1.76\text{ V}_{p-p}$ |
| **Gain Reduction** | $-5.3\%$ loss | $-40.0\%$ loss |

---

## 8. Multistage Cascading & The "Harsh Reality" of Loading

Consider cascading two identical Common Emitter stages directly (Stage 1 output connected to Stage 2 input):

```
[ Stage 1 (Av1 = 146.4) ] ──────► [ Stage 2 (Z_in2 = 902.3 Ω) ]
          │
      R_C = 1 kΩ loaded by Z_in2 = 902.3 Ω
```

### 8.1 Naive Expectation vs. Harsh Reality
- **Naive Expectation**: Multiplying individual stage gains yields $A_{v, total} = 140 \times 140 \approx 19,600$.
- **Harsh Reality**: Stage 2's low input impedance ($Z_{in2} = 902.3\ \Omega$) acts as an AC load connected in parallel with Stage 1's collector resistor ($R_{C1} = 1\text{ k}\Omega$).

### 8.2 Stage 1 Loaded Calculation
1. **Effective AC Collector Resistance ($R_C'$ )**:
   $$R_C' = R_{C1} \parallel Z_{in2} = 1\text{ k}\Omega \parallel 902.3\ \Omega = \frac{1000 \times 902.3}{1000 + 902.3} \approx 474.3\ \Omega$$
2. **Stage 1 Loaded Voltage Gain ($A_{v1, loaded}$)**:
   $$A_{v1, loaded} = \frac{R_C'}{r_e} = \frac{474.3\ \Omega}{6.83\ \Omega} \approx \mathbf{69.4}$$

> [!CAUTION]
> **Impedance Mismatch Core Rule**: High voltage gain in a CE amplifier requires a large collector resistor $R_C$. However, a high $R_C$ exhibits a poor output driving capability when loaded by a low input impedance $Z_{in2}$. 
> 
> Directly cascading CE stages results in massive gain reduction. Practical designs insert buffer stages—such as **Common Collector (Emitter Follower)** amplifiers—between CE stages to bridge high output impedance to low input impedance.

---

## 9. Practical Homework Assignment, Model Validation & Exam Tips

### 9.1 Exam Tip & Bias Trap Warning

> [!WARNING]
> **Sneaky Exam Question Warning**: The professor highlighted a classic exam trap: presenting a circuit diagram and asking students to calculate its voltage gain when the circuit is intentionally biased into **Saturation** or **Cutoff**.
> 
> Students who skip DC bias calculations and blindly apply $A_v = \frac{R_C}{r_e}$ will calculate an incorrect non-zero gain value. If the transistor is in saturation or cutoff, it cannot amplify AC signals, and the true AC voltage gain is **$A_v = 0$**. 
> 
> **Always verify that $V_{CE} > V_{CE(sat)}$ and $I_C > 0$ before performing AC calculations.**

### 9.2 LTspice Simulation Assignment
Students were assigned to replicate and simulate the analyzed CE amplifier in LTspice:

#### Simulation Setup Instructions:
1. Build the CE amplifier circuit in LTspice.
2. Add a custom `.model` directive to set the transistor DC/AC gain:
   `.model NPN BJT(BF=160)` or `.model NPN BJT(BF=200)`
3. Apply a $1\text{ kHz}$ sine wave input signal with a $15\text{ mV}$ peak amplitude ($30\text{ mV}_{p-p}$).
4. Run a transient analysis (`.tran`) and measure peak-to-peak input and output voltages.
5. Compute simulated voltage gain: $A_{v, SPICE} = \frac{V_{out(p-p)}}{V_{in(p-p)}}$.

#### Hand Calculations vs. SPICE Expectation:
- $r_e$ hand calculations provide a **ballpark estimate** that typically differs from LTspice by a factor of roughly **$1.2\times$ ($\sim 20\%$)**.
- This discrepancy occurs because LTspice utilizes the comprehensive **Gummel-Poon model** (accounting for Early voltage $V_A$, junction capacitances $C_{JE}, C_{JC}$, parasitic resistances, and temperature effects), whereas the $r_e$ model uses a simplified 2–3 parameter approximation.

### 9.3 Lecture Wrap-up
- The lecture concluded at 9:00 PM.
- The professor expressed expectation for in-person, face-to-face instruction in the subsequent class session.

---

## 10. Comprehensive Reference Formula Sheet

$$\text{Thermal Voltage: } V_T = \frac{k T}{q} \approx 26\text{ mV} \quad (\text{at } 300\text{ K})$$

$$\text{Dynamic Emitter Resistance: } r_e = \frac{26\text{ mV}}{I_E}$$

$$\text{Base Input AC Resistance: } R_{in(base)} = \beta_{ac} \cdot r_e$$

$$\text{CE Stage Input Impedance: } Z_{in} = R_1 \parallel R_2 \parallel (\beta_{ac} \cdot r_e)$$

$$\text{CE Stage Output Impedance: } Z_o = R_C \parallel R_L$$

$$\text{Unloaded Voltage Gain: } A_v = -\frac{R_C}{r_e} \implies |A_v| = \frac{R_C}{r_e}$$

$$\text{Source-Loaded Voltage Gain: } A_{v, system} = A_v \cdot \left(\frac{Z_{in}}{Z_{in} + R_S}\right) \cdot \left(\frac{R_L}{R_C + R_L}\right)$$

$$\text{Approximate DC Bias Validity Condition: } \beta_{dc} \cdot R_E \ge 10 \cdot R_2$$
