# Lecture Notes: Electronics 2 (Lecture 6)

**Course:** Electronics 2 (CE-Tron 2)  
**Topic:** Clamping Circuits, Voltage Multipliers (Doublers/Triplers/Quadruplers), LTSpice Circuit Simulation, Curriculum Roadmap (CE-Tron 3), Light-Emitting Diodes (LED Physics, Packaging, Doping, $V_F$ Spectrum), and Lighting Technology History  
**Source File:** `file:///Users/francis/school/y2/t3/etrn2/lecs/transcriptions/without_timestamps/lec6.txt`  
**Output Path:** `file:///Users/francis/school/y2/t3/etrn2/lecs/transcriptions/notes/lec6_notes.md`

---

## 1. Clamping Circuits (Clampers)

### 1.1 Core Definitions & Distinctions
* **Clamper (Voltage Shifter)**: A diode-capacitor circuit designed to shift (move) an alternating current (AC) signal to a different DC baseline without altering the shape or peak-to-peak voltage ($V_{p-p}$) of the input waveform.
* **Clipper vs. Clamper Distinction**:
  * **Clipper**: Alters or cuts off portions of the AC waveform (modifies waveform shape/limits).
  * **Clamper**: Preserves the exact shape and $V_{p-p}$ of the waveform, adding a DC offset that moves the entire signal upwards or downwards.
* **Core Action**: Facilitated through the charging and discharging mechanics of a capacitor ($C$) controlled by diode ($D$) conduction states.

---

### 1.2 Step-by-Step Mathematical & Circuit Analysis

Consider a symmetrical AC input signal $V_{in}$ swinging between $+15\text{ V}$ and $-15\text{ V}$ (or $+5\text{ V}$ peak with a $-5\text{ V}$ DC bias source / $+10\text{ V}$ peak-to-peak).

```
   C
---||---+---|>|---+--- 
        |  (Diode)|
       [V_bias]  [R_L] V_out
        |         |
--------+---------+---
```

#### Phase 1: Initial Condition & Positive Charging Cycle
1. **Initial Uncharged State**: The capacitor starts fully discharged ($V_C(0) = 0\text{ V}$).
2. **First Positive Half-Cycle**:
   * As $V_{in}$ rises towards its positive peak, current flows clockwise through the capacitor and diode.
   * Diode $D$ is **forward-biased** (ON). Assuming an ideal diode ($V_D = 0\text{ V}$):
     $$\text{KVL}: -V_{in} + V_C + V_D + V_{bias} = 0$$
     $$V_C = V_{in} - V_{bias}$$
   * If $V_{in, peak} = +5\text{ V}$ and $V_{bias} = -5\text{ V}$ (or battery polarity aiding):
     $$V_C = 5\text{ V} - (-5\text{ V}) = 10\text{ V}$$
3. **Instantaneous Charging Current**:
   * Because $V_C$ starts at $0\text{ V}$, a very large (theoretically infinite, practically limited by source resistance) instantaneous charging current flows through the diode.
   * This rapidly charges the capacitor to its peak charging voltage $V_C = 10\text{ V}$ (polarity: positive on input side, negative on diode side).

#### Phase 2: Downward Swing & Reverse-Biased Cycle
1. **Downward / Negative Cycle**:
   * As $V_{in}$ passes its positive peak and drops ($+4\text{V}, +3\text{V}, +2\text{V}, 0\text{V}, -5\text{V}, -15\text{V}$), current attempts to flow counter-clockwise to discharge the capacitor.
2. **Diode Blocking Action**:
   * Current attempting to flow in reverse forces diode $D$ into **reverse bias** (OFF / open circuit).
   * The diode open-circuits and absorbs the voltage differential.
   * Capacitor $C$ cannot discharge back through the open diode; it retains its peak stored charge ($V_C = 10\text{ V}$).
3. **Series-Aiding Voltage Addition**:
   * When $V_{in}$ reaches its negative peak ($-5\text{ V}$ or $-15\text{ V}$), the negative supply polarity aligns with the charged capacitor polarity.
   * The AC source and charged capacitor act like **two series voltage sources** aiding each other:
     $$V_{out} = V_{in} - V_C = (-5\text{ V}) - 10\text{ V} = -15\text{ V}$$
4. **Final Clamped Output Waveform**:
   * The output waveform is shifted downward by $-10\text{ V}$ DC offset.
   * Peak-to-peak amplitude remains identical to the input ($V_{p-p} = 30\text{ V}$ or $10\text{ V}$).
   * Positive peak reaches $0\text{ V}$ (or $+5\text{ V}$ depending on bias), negative peak reaches $-15\text{ V}$.

---

### 1.3 Time Constant Constraint ($\tau$) & Recharge Cycle

* **Discharge Path**: While diode $D$ is OFF (open circuit), the capacitor slowly discharges energy through the load resistor $R_L$.
* **Critical Design Constraint**:
  $$\tau = R_L \cdot C \gg T_{in} = \frac{2\pi}{\omega} = \frac{1}{f_{in}}$$
  * The $RC$ time constant must be significantly larger than the period $T_{in}$ of the input AC signal.
* **Cycle-by-Cycle Recharging**:
  * During the negative half-cycle, $C$ slowly drains a tiny fraction of its charge through $R_L$, causing $V_C$ to droop slightly.
  * At the peak of every positive half-cycle, $V_{in}$ briefly exceeds $V_C$, forward-biasing diode $D$ for a fraction of a millisecond.
  * This brief pulse instantaneously recharges $C$ back to full stored voltage ($10\text{ V}$).

---

## 2. Voltage Multipliers & Voltage Doubler Circuit

### 2.1 Concept and Topology
A **Voltage Doubler** combines a clamping stage (DC shifter) with a half-wave rectifying stage to extract a DC output voltage approximately **twice the peak voltage** of the input AC source ($V_o \approx 2 V_m$).

```
            C1           D1
   +-------||---+----->|------+-------+
   |            |             |       |
  (~) V1       --- D2        === C2  [R1] Load (V_out ≈ 2 V_m)
   |           / \            |       |
   +------------+-------------+-------+
```

---

### 2.2 Detailed Operation Analysis (Square Wave Input $V_1 = \pm V_m$)

*Professor Note:* Analyzing clamping and multiplier circuits with a **square wave** ($\pm 10\text{ V}$) is much easier to visualize than a sine wave because diode states switch cleanly ON and OFF.

#### Comparison: Standard Half-Wave Rectifiers
* **Positive Half-Wave Rectifier**: Diode anode to source, cathode to load. Passes positive half-cycles ($V_{out} = V_m - 0.7\text{ V}$), blocks negative half-cycles ($V_{out} = 0\text{ V}$).
* **Negative Half-Wave Rectifier**: Diode cathode to source, anode to load. Passes negative half-cycles.

#### Step-by-Step Doubler Execution:

#### Phase A: Negative Half-Cycle ($V_1 = -10\text{ V}$)
1. Input $V_1$ drops to $-10\text{ V}$ (negative top, positive bottom). Current attempts to flow counter-clockwise.
2. Diode $D_2$ is **forward-biased** (ON / acting as a short circuit to ground).
3. Diode $D_1$ is **reverse-biased** (OFF / open circuit).
4. Capacitor $C_1$ charges rapidly directly across $V_1$ to $V_{C1} = 10\text{ V}$ (polarity: positive on right plate near $D_1/D_2$ junction, negative on left plate connected to $V_1$).
5. Output voltage across load $R_1$ is $V_o = 0\text{ V}$.

#### Phase B: Positive Half-Cycle ($V_1 = +10\text{ V}$)
1. Input $V_1$ flips to $+10\text{ V}$ (positive top, negative bottom).
2. Diode $D_2$ becomes **reverse-biased** (OFF).
3. Source $V_1$ ($+10\text{ V}$) and charged capacitor $C_1$ ($+10\text{ V}$) align in series-aiding polarity:
   $$V_{total} = V_1 + V_{C1} = 10\text{ V} + 10\text{ V} = 20\text{ V}$$
4. Combined voltage of $20\text{ V}$ forward-biases rectifier diode $D_1$ (ON).
5. Current flows clockwise through $D_1$, charging filter capacitor $C_2$ and supplying load resistor $R_1$.

#### Phase C: Steady State & Recharging
* In subsequent negative half-cycles: $D_1$ turns OFF, $C_2$ powers $R_1$, and $D_2$ turns ON to instantaneously recharge $C_1$ back to $10\text{ V}$.
* In subsequent positive half-cycles: $D_1$ turns ON, boosting $C_2$ back up to $20\text{ V}$.

---

### 2.3 Practical Non-Idealities & Capacitor Ratio Rule

1. **Real-World Voltage Formula**:
   In reality, the output voltage does not reach exact theoretical $2 V_m$ due to diode forward conduction drops ($V_D \approx 0.7\text{ V}$) and capacitor discharge droop:
   $$V_{o, \text{peak}} = 2 V_m - 2 V_D - V_{\text{discharge}} = 2 V_m - 1.4\text{ V} - \Delta V$$
2. **Capacitor Sizing Ratio Rule**:
   * Clamping capacitor $C_1$ must be **significantly larger** than rectifier filter capacitor $C_2$ (rule of thumb: $C_1 \approx 10 \cdot C_2$).
   * *Why?* During Phase B, $C_1$ transfers energy to charge $C_2$. If $C_1 \approx C_2$, $C_1$ loses half its voltage during transfer, severely collapsing the DC output voltage.

---

### 2.4 Cascading Stages (Triplers, Quadruplers) & Efficiency Warning
* By adding repetitive diode-capacitor clamping/rectifying ladders, outputs of $3 V_m$ (tripler), $4 V_m$ (quadrupler), etc., can be generated.
* > [!WARNING]
  > **Inefficiency Warning**: Multi-stage voltage multipliers are **extremely inefficient**. Each cascaded stage incurs additional diode forward drops ($V_D$) and cumulative charge transfer losses. Output voltage strays further and further from theoretical multiples as stages increase. Multipliers are ONLY practical for **high-voltage, very low-current** applications.

---

### 2.5 Real-World Application Tangent: Electronic Mosquito Racket / Swatter

*Professor Tangent:* "Do you guys recognize what this is? Hindi ito tennis racket. It's a mosquito racket / bat."

#### Internal Architecture of a Mosquito Swatter:
1. **Power Source**: Low-voltage DC Battery pack ($3.7\text{ V} - 5\text{ V}$ Lithium-ion or Lead-Acid).
2. **AC Oscillator**: Inverter circuit converting DC battery voltage into high-frequency AC.
3. **Step-Up Transformer**: Boosts AC voltage to intermediate level ($\approx 300\text{ V}$ AC).
4. **Multi-Stage Voltage Multiplier**: Series diode-capacitor ladder boosting $\approx 300\text{ V}$ AC up to $\approx 900\text{ V} - 1000\text{ V}$ DC operating voltage.

#### Why Voltage Multipliers are Ideal for Mosquito Swatters:
* **Zero Quiescent Current Load**: When no insect is touching the metal chicken wire mesh, load current is exactly zero ($I_{load} = 0$).
* **Capacitive Energy Storage**: The multiplier slowly charges a small output capacitor up to $1000\text{ V}$ and sits idle without consuming heavy current.
* **Instantaneous Discharge**: When a mosquito contacts the mesh grid, it shorts the output capacitor, causing an instantaneous discharge spark that zaps/kills/stuns the insect.
* **Safety Sizing**: Output capacitance is kept small so a single discharge kills insects but lacks enough total energy ($E = \frac{1}{2} C V^2$) to be dangerous to humans.
* **Recharge Cycle**: After a zap, the circuit continuously runs to recharge the output capacitor back to $1000\text{ V}$.

---

## 3. LTSpice Simulation Demonstration & Empirical Findings

The professor conducted a live LTSpice simulation to demonstrate voltage doubler behavior under non-ideal real-world parameters.

### 3.1 Simulation Setup Parameters
* **Input Generator**: Square wave pulse source swinging between $-10\text{ V}$ and $+10\text{ V}$ ($V_{p-p} = 20\text{ V}$).
  * Timing: Pulse width = $1\text{ ms}$, Period $T = 2\text{ ms}$ ($\text{Frequency } f = 500\text{ Hz}$).
* **Diodes**: `1N4148` (standard low-current semi-signal diodes; non-ideal conduction).
* **Capacitor Values**: $C_1 = 10\ \mu\text{F}$ (shifting cap), $C_2 = 1\ \mu\text{F}$ (rectifier filter cap) $\to C_1 = 10 \cdot C_2$.
* **Load Resistor**: $R_L = 47\text{ k}\Omega$.
* **Load Current Calculation**: At $V_o \approx 20\text{ V}$, $I_L = \frac{20\text{ V}}{47\text{ k}\Omega} \approx 0.426\text{ mA}$ ($\approx 0.5\text{ mA}$).
* **Simulation Duration**: $20\text{ ms}$ transient analysis.

---

### 3.2 Key Empirical Simulation Results

1. **Node Voltage at $D_1-C_1$ Junction (Clamped Node)**:
   * First charging pulse: Peak voltage reaches only $\approx 18\text{ V}$. Heavy initial charging current forces `1N4148` diode drop up to $1.5\text{ V} - 2.0\text{ V}$ (far higher than $0.7\text{ V}$).
   * Steady state: Node voltage peaks at $\approx 19\text{ V}$ (`1N4148` dropping $\approx 1.0\text{ V}$).
2. **Rectified Output DC Voltage ($V_o$ across $C_2-R_L$)**:
   * Peak DC Output: Reaches $\mathbf{18.7\text{ V}}$ at $t = 3\text{ ms}$ (Theoretical: $20\text{ V} - 1.4\text{ V} = 18.6\text{ V} \approx 18.7\text{ V}$).
   * Discharge Ripple: With $R_L = 47\text{ k}\Omega$, voltage droops from $18.7\text{ V}$ down to $17.3\text{ V}$ before being recharged in the next cycle.

---

### 3.3 Parametric Experiments & Component Sizing Impact

| Experiment Configuration | Peak Voltage | Discharge / Min Voltage | Observation / Diagnostic Findings |
| :--- | :--- | :--- | :--- |
| **Baseline** ($C_1=10\mu\text{F}, C_2=1\mu\text{F}, R_L=47\text{k}\Omega$) | $18.7\text{ V}$ | $17.3\text{ V}$ | Mild ripple ($\Delta V = 1.4\text{V}$), stable doubling output. |
| **Increased Load** ($R_L = 22\text{ k}\Omega$) | $18.7\text{ V}$ | $16.81\text{ V}$ | Higher discharge current doubles ripple droop. |
| **Heavy Load** ($R_L = 4.7\text{ k}\Omega$) | High ripple | Severe droop | Huge ripple amplitude; circuit cannot sustain DC level. |
| **Equal Capacitors** ($C_1 = C_2 = 10\ \mu\text{F}$) | $17.2\text{ V}$ | Reduced ripple | **Performance Loss**: $C_1$ drains too much charge while filling equal-sized $C_2$, reducing peak output DC level. |
| **Large Capacitors** ($C_1 = C_2 = 100\ \mu\text{F}$) | Degraded | Severe degradation | `1N4148` diodes struggle under massive current surges required to charge $100\mu\text{F}$, increasing $V_D$ drops and lowering output. |

> [!TIP]
> **Key Rule of Thumb**: Keep clamping capacitor $C_1$ approximately **10 times larger** than filter capacitor $C_2$ ($C_1 \approx 10 \cdot C_2$) to maintain high peak DC multiplication.

---

## 4. Academic Advice & Curriculum Roadmap

### 4.1 Integration into CE-Tron 3 (Electronics 3)
* **Professor Note / Exam Tip**: All knowledge regarding diodes, clampers, rectifiers, and multipliers must be retained for upper-level courses.
* In **CE-Tron 3 (Electronics 3)**, students will be tasked with designing complex electronic systems.
* Problem specifications in CE-Tron 3 will assume students independently know how to employ techniques such as voltage doubling to achieve required node voltages when power supply rails are limited.

---

## 5. Light-Emitting Diodes (LEDs)

### 5.1 Fundamentals & Solid-State Physics
* **Definition**: Specialized PN junction diodes designed to emit light photons when **forward-biased**.
* **Physical Process**: **Electroluminescence** — direct conversion of electrical energy into optical radiation (light).
* **Spectrum**: Emits visible light (Red, Green, Blue, etc.) or invisible spectrums (Infrared $\lambda \ge 760\text{ nm}$, Ultraviolet).
* **Efficiency & Market Dominance**: Solid-state LEDs are rapidly replacing incandescent/fluorescent lighting due to vastly superior electrical-to-luminous conversion efficiency and lower manufacturing costs.

---

### 5.2 Package Form Factors & Manufacturing Economy

1. **Through-Hole LEDs**:
   * Feature long metal legs/leads for insertion into through-hole PCB pads.
2. **Surface Mount Device (SMD) LEDs**:
   * Leadless packages soldered directly onto surface pads.
   * Universal standard in modern consumer electronics (routers, Wi-Fi access points, chargers, TVs).
   * **Manufacturing Economy Rationale**: Electronic boards populated by microcontrollers and ICs are 90%–100% SMD. Automated SMT pick-and-place machines assemble SMD components rapidly. Mixing through-hole components requires adding a separate machine or a human operator ("another machine, aka a person") into the production line, slowing down manufacturing process flow.
3. **Chip-on-Board (COB) LEDs**:
   * Multiple bare semiconductor LED dies mounted directly onto an aluminum substrate to maximize thermal conduction for high-lumen residential/industrial lighting.

---

### 5.3 Physical Anatomy of a Through-Hole LED

```
          +-----------------+
          |    (Plastic)    |  Encapsulation (Clear/Translucent)
          |     /-----\     |
          |    /  Die  \    |  Semiconductor Die on Carrier
          |   +---------+   |
          |   | Anvil   |   |  Anvil = Cathode (Larger post / Heat sink)
          |   | (Post)  |   |  Post  = Anode   (Smaller post + Wire bond)
          +---+---------+---+
              |         |
              | Cathode | Anode (+)
              |  (-)    |
```

* **Encapsulation**: Clear or translucent resin casing. Plastic color does NOT dictate emitted light color!
* **Cathode (-) Post**: Connected to the **LARGER metal anvil structure** (copper carrier frame). Serves two purposes:
  1. Mechanical platform holding the semiconductor die.
  2. **Thermal Heat Sink**: Conducts thermal energy away from the semiconductor die through metal leads to the environment.
* **Anode (+) Post**: Connected to the **SMALLER post**, linked via a tiny wire bond to the top of the semiconductor die.
* **Flat Spot**: Mechanical flat notch on the plastic rim prevents LED rotation when panel-mounted.
* **Thermal Dissipation Reality**: While LEDs are highly efficient, they are not 100% efficient. Heat generation indicates electrical energy lost as thermal dissipation rather than converted into light.

---

### 5.4 Diode Schematic Symbols Summary

To date, three distinct diode schematic symbols have been introduced:
1. **Standard Rectifier Diode**: Triangle pointing to a vertical bar.
2. **Zener Diode**: Triangle pointing to a vertical bar with bent wingtips ($Z$-shape).
3. **Light-Emitting Diode (LED)**: Standard diode symbol (with or without enclosing circle) with two outward-pointing wiggly/straight arrows representing emitted photons.

---

### 5.5 Semiconductor Doping, Spectrum & Forward Voltage ($V_F$)

Unlike standard silicon diodes ($V_F \approx 0.7\text{ V}$), LEDs are constructed from compound semiconductors (GaAs, GaP, GaN, InGaN) with custom energy bandgaps ($\Delta E_g$).

#### Wavelength ($\lambda$) vs. Forward Voltage ($V_F$) Rule:
Photon energy is inversely proportional to wavelength ($E = \frac{hc}{\lambda}$). Shorter wavelengths (blue/UV) require higher bandgap energies, resulting in higher forward conduction voltage drops ($V_F$).

| LED Color / Spectrum | Typical Wavelength ($\lambda$) | Forward Voltage Range ($V_F$) | Relative $V_F$ Ranking |
| :--- | :--- | :--- | :--- |
| **Infrared (IR)** | $\ge 760\text{ nm}$ | $1.2\text{ V} - 1.6\text{ V}$ | Lowest $V_F$ |
| **Red** | $620 - 700\text{ nm}$ | $1.8\text{ V} - 2.1\text{ V}$ | Lowest Visible $V_F$ |
| **Yellow / Orange** | $580 - 600\text{ nm}$ | $2.0\text{ V} - 2.2\text{ V}$ | Medium $V_F$ |
| **Green** | $520 - 570\text{ nm}$ | $2.2\text{ V} - 3.2\text{ V}$ | High $V_F$ |
| **Blue / Violet / UV** | $400 - 470\text{ nm}$ | $3.0\text{ V} - 3.6\text{ V}$ | Highest $V_F$ |

---

### 5.6 Historical Tangent: Monochromatic Light & Street Lighting

*Professor Tangent:* Historical evolution of municipal street lighting and spectral perception.

#### The Monochromatic Spectrum Problem:
Single-semiconductor LEDs emit strictly **monochromatic light** (a single narrow wavelength spike). Human eyes cannot perceive color or contrast effectively under monochromatic illumination; objects appear thin, washed out, and difficult to resolve visually.

#### Street Lighting Evolution:
1. **Mercury Vapor Lamps (Older Technology)**:
   * Emitted bluish-white light.
   * Discharge produced almost pure Ultraviolet (UV) light internally.
   * Glass bulb housing was coated internally with **phosphors**. UV photons struck the phosphors, re-emitting a broad white spectrum.
2. **Sodium Halide / High-Pressure Sodium Lamps (Transition Era)**:
   * Emitted intense orange-yellow light.
   * Significantly more electrically efficient than mercury lamps.
   * **Downside**: Emitted narrow monochromatic orange-yellow spectrum. Citizens complained that despite high brightness, visual acuity was poor and details were hard to see.

---

### 5.7 White LED Engineering Solutions

Because no single semiconductor material intrinsically emits white light, white LED light is synthesized using two main techniques:

1. **Phosphor Conversion Method (Blue/UV LED + Phosphor)**:
   * A short-wavelength Blue ($\sim 450\text{nm}$) or UV semiconductor die is coated with a phosphor layer (e.g., YAG yellow phosphor).
   * Blue photons strike phosphors, exciting them to re-emit longer green and red wavelengths. The combination yields broad-spectrum white light.
2. **Multi-LED Color Temperature Blending**:
   * Mounting dies of different color temperatures on a single COB substrate (e.g., Warm Incandescent Yellow $\sim 3400\text{ K}$ + Cool Bluish White $\sim 6000\text{ K} - 7000\text{ K}$).
   * Operating both simultaneously blends the output to daylight-balanced white light ($\sim 5000\text{ K}$).

---

## 6. Next Lecture Transition

* **Wrap-up**: Diodes unit is complete (except for 5 minutes covering remaining specialized diodes next session).
* **Upcoming Topic**: Transistors (Bipolar Junction Transistors - BJTs & Field-Effect Transistors - FETs).
