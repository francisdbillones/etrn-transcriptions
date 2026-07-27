# Lecture 4: Rectifier Circuits, Linear DC Power Supplies, and Zener Diode Regulation

---

## Executive Summary & Overview
This lecture provides an exhaustive treatment of diode rectifier topologies, linear DC power supply architectures, diode non-idealities in power circuits, and Zener diode shunt voltage regulators. Key concepts include:
1. **Full-Wave Rectification**: Comparison between 2-diode center-tapped transformer topologies and 4-diode bridge configurations.
2. **Linear DC Power Supply Architecture**: A 4-stage pipeline (Transformer, Bridge Rectifier, Capacitor Filter, Voltage Regulator) converting line AC into regulated DC.
3. **Diode Non-Idealities & Modeling**: Voltage drop differences ($1V_D$ vs. $2V_D$), efficiency trade-offs, engineering estimates, and LTspice simulation practices.
4. **Zener Diode Physics & Shunt Regulation**: Breakdown region operation, commercial availability rules, power limits ($P_{Z,max} = V_Z I_Z$), symbol rules, and step-by-step Thevenin load-line analysis with live in-class numerical corrections.

---

## 1. Class Logistics, Professor Tangents & Technical Environment Trivia

- **Online Session & Display Setup**:
  - The lecture was conducted online temporarily due to scheduling constraints.
  - The professor operated on a single-screen workstation where the presentation covered the Zoom chat and participants list.
  - **Class Communication Rule**: Students were instructed to **unmute** to ask questions, as chat messages could not be monitored continuously without interrupting the presentation screen.
- **DLSU Commute & Traffic Tangent**:
  - The professor chose to stream from the DLSU campus computer rather than from home because of an upcoming **11:00 AM face-to-face laboratory class in Room G407**.
  - Driving from home to DLSU between the morning lecture and the 11:00 AM lab would have encountered **horrendous traffic**, making on-time arrival impossible.
  - **Hardware Note**: The professor noted that the campus setup would ideally benefit from a second monitor, but adding one is not worth the extra desk space for rare online sessions.
- **Presentation Software & Stylus Quirks**:
  - **Slide Format**: The professor remarked, *"Buti na lang hindi widescreen yung slides, no?"* (Good thing the slides are 4:3 standard format rather than widescreen, allowing windows to sit on the margins).
  - **Stylus Sensor Issue**: The drawing tablet stylus tip is spring-loaded. Pressing near the display edge triggered a hardware sensor that PowerPoint misinterpreted as a command to exit the slideshow, losing on-screen annotations twice during the presentation.
- **Laboratory & Schedule Reminders**:
  - Lecture concluded at 9:00 AM.
  - Lab class held face-to-face at **11:00 AM in Room G407**.
  - **Experiment 2 Preview**: Upcoming lab focuses on **Rectification and Clipping**.

---

## 2. Full-Wave Rectification Topologies

Half-wave rectifiers clip half of the AC cycle, leaving long zero-voltage gaps. **Full-wave rectifiers** convert both positive and negative AC half-cycles into continuous unidirectional (positive) DC pulses.

### 2.1 Center-Tapped Transformer Full-Wave Rectifier (2-Diode Configuration)

#### Real-World Mechanism & Synchronized Sources
To achieve full-wave output with two diodes, the circuit requires two equal AC voltage sources in phase with each other. In practical electronics, this is achieved using a **center-tapped transformer secondary winding** (recap from CETRON1).

- The center tap is connected to **ground**.
- The top and bottom secondary terminals produce two synchronized sinusoidal voltage signals relative to ground. When the top terminal goes positive, the bottom terminal goes negative, and vice-versa.

```
           +------------------->|-------------------+
           |                     D1                 |
        +--+--+                                     |
AC In ~ |  |  | Secondary                           +---> (+) Output
        +--+--+                                     |     v_L(t) across R_L
           |-------- (Ground Center Tap) -----------+---> (-) Ground
           |                                        |
           +------------------->|-------------------+
                                 D2
```

#### Cycle-by-Cycle Analysis
1. **Positive Half-Cycle ($v_{in} > 0$)**:
   - Top terminal is positive; bottom terminal is negative relative to the center-tap ground.
   - Diode $D_1$ is **forward biased** (conducts). Current path: Ground $\rightarrow D_1 \rightarrow R_L \rightarrow$ Ground.
   - Diode $D_2$ is **reverse biased** (open circuit).
   - Load voltage: $v_L(t) = v_{top}(t) - V_D$.
2. **Negative Half-Cycle ($v_{in} < 0$)**:
   - Top terminal is negative; bottom terminal is positive relative to center-tap ground.
   - Diode $D_1$ is **reverse biased** (open circuit).
   - Diode $D_2$ is **forward biased** (conducts). Current flows from the bottom winding through $D_2$ into $R_L$ to ground.
   - Despite input phase reversal, current flows through $R_L$ in the **exact same direction**, producing a positive output voltage pulse: $v_L(t) = |v_{bottom}(t)| - V_D$.

#### Output Characteristics
- **Peak Voltage**: $V_{O,peak} = V_{S,peak} - V_D$ (only 1 diode drop per half-cycle).
- **Ripple Frequency**: $f_{out} = 2 f_{in}$ ($120\text{ Hz}$ output for a $60\text{ Hz}$ AC line).

---

### 2.2 Full-Wave Bridge Rectifier (4-Diode Configuration)

#### Motivation & Circuit Topology
The bridge rectifier achieves full-wave output using a **single 2-terminal AC transformer secondary** (no center tap required) by employing four diodes arranged in a bridge (diamond) network.

```
                  +-------|>|-------+ (Node A)
                  |        D1       |
       AC Source  |                 +----+---- (+) Output
        (~) ------+                      |
       Term 1     |                 [ R_L Load ]
                  |        D4       |    |
                  +-------|<|-------+    +---- (-) Output / Ground
                  |                      |
       AC Source  +-------|>|-------+    |
        (~) ------+        D2       |    |
       Term 2     |                 +----+
                  |        D3       |
                  +-------|<|-------+
```

#### Cycle-by-Cycle Pair Conduction Analysis
1. **Positive Half-Cycle ($v_{in} > 0$)**:
   - Terminal 1 is positive relative to Terminal 2.
   - Current enters top node: diode $C$ ($D_3$) is reverse-biased. Current flows through diode $A$ ($D_1$).
   - At top of load $R_L$: diode $D$ ($D_4$) is reverse-biased.
   - **Forward-Biased Pair**: Diodes $A$ & $B$ ($D_1$ & $D_2$).
   - **Reverse-Biased Pair**: Diodes $C$ & $D$ ($D_3$ & $D_4$) [Open Circuits].
   - Current path: Term 1 $\rightarrow D_1 \rightarrow R_L \rightarrow$ Ground $\rightarrow D_2 \rightarrow$ Term 2.
2. **Negative Half-Cycle ($v_{in} < 0$)**:
   - Terminal 2 is positive relative to Terminal 1.
   - Diode $B$ ($D_2$) is reverse-biased. Current flows through diode $D$ ($D_4$).
   - Diode $A$ ($D_1$) is reverse-biased. Current enters load $R_L$ in the **same positive direction**, flows to ground, returns through diode $C$ ($D_3$).
   - **Forward-Biased Pair**: Diodes $C$ & $D$ ($D_3$ & $D_4$).
   - **Reverse-Biased Pair**: Diodes $A$ & $B$ ($D_1$ & $D_2$) [Open Circuits].
   - Current path: Term 2 $\rightarrow D_4 \rightarrow R_L \rightarrow$ Ground $\rightarrow D_3 \rightarrow$ Term 1.

#### Output Characteristics
- **Peak Output Voltage**: $V_{O,peak} = V_{S,peak} - 2V_D$ (reflects two series diode drops per conduction path).
- **Ripple Frequency**: $f_{out} = 2 f_{in}$.

---

### 2.3 Terminology & Redundancy Trivia

> [!NOTE]
> **Professor's Terminology Comment**:
> Calling a circuit a **"Full-Wave Bridge Rectifier"** is technically **redundant**, because by definition any bridge rectifier topology inherently provides full-wave rectification. However, the phrase is ubiquitous in engineering literature and trade terminology, so students should accept and recognize it.

---

### 2.4 Topology Comparison Matrix

| Feature / Metric | Center-Tapped Full-Wave Rectifier | Full-Wave Bridge Rectifier |
| :--- | :--- | :--- |
| **Number of Diodes** | 2 | 4 |
| **Transformer Requirements** | Center-tapped secondary (3 terminals) | Standard single winding (2 terminals) |
| **Conduction Path / Half-Cycle** | 1 diode in series | 2 diodes in series |
| **Peak Output Voltage** | $V_{S,peak} - V_D$ | $V_{S,peak} - 2V_D$ |
| **Conduction Efficiency** | Higher (lower silicon voltage drop) | Slightly lower ($2 \times V_D$ drop) |
| **Transformer Cost / Complexity** | Higher (specialized winding) | Lower (off-the-shelf standard winding) |

---

## 3. Linear DC Power Supply Architecture

### 3.1 Modern SMPS vs. Traditional Linear Power Supplies
- **Modern Power Adapters**: Use **Switch-Mode Power Supply (SMPS)** technology for high efficiency and compact size.
- **Traditional Analog / Linear Power Supply**: Heavy, robust transformer-based design. Understanding its block diagram is fundamental to power electronics.

### 3.2 Four-Stage Block Diagram

```
+------------+     +-----------+     +------------+     +---------------+
| Power      | --> | Bridge    | --> | Capacitor  | --> | Voltage       | --> Regulated
| Transformer|     | Rectifier |     | Filter     |     | Regulator     |     DC Output
+------------+     +-----------+     +------------+     +---------------+
  Line AC           Unfiltered        DC + Ripple        Steady DC
  (120V / 220V)     DC Pulses         (Discharge Curve)  (Fixed Target)
```

1. **Power Transformer**: Steps down high-voltage AC ($220\text{V RMS}$ Meralco line in PH, $120\text{V RMS}$ in US textbooks) to low-voltage AC ($V_S$), providing galvanic isolation.
2. **Bridge Rectifier**: Converts AC $V_S$ into pulsating full-wave DC. Drops to $0\text{V}$ 120 times per second ($60\text{Hz}$ AC).
3. **Capacitor Filter (Energy Storage)**: Smooths zero-voltage dips into a continuous DC voltage with residual ripple.
4. **Voltage Regulator**: Eliminates residual ripple to maintain a fixed DC voltage.

---

### 3.3 Capacitor Filter Mechanics & Energy Storage

- **Charging Phase**: As rectified output rises toward $V_{peak}$, the capacitor charges rapidly through the forward-biased bridge diodes.
- **Discharging Phase**: As rectified pulse drops toward zero, bridge diodes turn off. The capacitor acts as a temporary energy reservoir, discharging into the load.
- **Load Rate Dependence**:
  - **Heavy Load** (low load resistance $R_L$, high current demand): Fast discharge rate $\rightarrow$ larger voltage ripple.
  - **Light Load** (high load resistance $R_L$, low current demand): Slow discharge rate $\rightarrow$ smaller voltage ripple.

---

### 3.4 Voltage Regulator Constraints & "Non-Magical" Nature

> [!IMPORTANT]
> **Regulator Operating Limits & Headroom**:
> A linear voltage regulator is **not magical**—it cannot manufacture voltage or step up an under-voltage input.
> - To maintain steady regulation ($V_{OUT}$), the minimum trough of the input ripple voltage ($V_{ripple,min}$) must **always exceed** the target regulated output voltage plus a required headroom margin ($\Delta V$):
>   $$V_{ripple,min} \ge V_{OUT} + \Delta V_{headroom}$$
> - If the line plug is pulled or input ripple dips below this threshold, the regulator drops out of regulation, and $V_{OUT}$ drops proportionally below the target voltage.

---

## 4. Diode Modeling in Power Rectifiers

### 4.1 Diode Model Spectrum
1. **Model 1 (Ideal Diode)**: $V_D = 0\text{ V}$. Quick functional check; ignores voltage loss.
2. **Model 2 (Constant Voltage Drop)**: $V_D \approx 0.7\text{ V}$ (silicon). Standard engineering hand calculations.
3. **Model 3 (Piecewise Linear with Resistance)**: Ideal diode + threshold voltage ($V_{D0}$) + series resistance ($R_D$). Accounts for load-dependent forward drops.

### 4.2 Engineering Practice vs. SPICE Simulation

- **LTspice Practice**: Professor recommends LTspice (*"hint, hint"*) for exact exponential diode modeling when precision is required.
- **Manual Back-of-the-Envelope Estimates**: Hand calculations using Model 2 or 3 allow engineers to estimate peak output voltage ($V_{peak,out} \approx V_{S,peak} - 2V_D$) instantly without waiting to set up simulation schematics.

---

## 5. Zener Diodes & Shunt Regulation Physics

### 5.1 Pronunciation & Semiconductor Doping Physics
- **Pronunciation**: Professor notes both *"Zener"* and *"Zenner"* are used, preferring *"Zener"* (*"it just makes it sound more social"*).
- **Semiconductor Doping**: Zener diodes are manufactured with modified, heavy impurity doping in the PN junction, lowering the reverse breakdown voltage to a precise **Zener knee voltage ($V_Z$ or $V_{ZK}$)**.

---

### 5.2 Commercial Voltage Ratings & Physical Limits

- **E12 Parallel Availability**: Like resistors (available only in standard EIA series values like $1.0\text{k}\Omega, 1.2\text{k}\Omega$, with no $1.15\text{k}\Omega$ available), Zener diodes are produced only in specific discrete breakdown voltages.
- **Standard Popular Voltages**: $3.3\text{V}, 4.7\text{V}, 5.1\text{V}, 6.8\text{V}, 8.2\text{V}$.

> [!CAUTION]
> **Commercial Availability & Power Limit Rules (Exam/Quiz Trivia)**:
> 1. **Does a 5.0V Zener Diode exist off-the-shelf?** **NO.** Standard designs must use $4.7\text{V}$ or $5.1\text{V}$ Zeners.
> 2. **Minimum Zener Voltage Limit**: Lowest commercially viable Zener breakdown voltage is $\approx 3.0\text{V} - 3.3\text{V}$. Lower breakdown voltages cannot be manufactured reliably by standard semiconductor processes.
> 3. **High-Voltage Zener Limits ($48\text{V} / 50\text{V}$)**: High-voltage Zeners exist but have severe current limitations because maximum power dissipation is bounded by $P_{Z,max} = V_Z \cdot I_Z$. At $48\text{V}$, even a modest current quickly exceeds thermal power limits, causing device destruction.

---

### 5.3 Schematic Symbol Rules

The Zener symbol features bent flags at the cathode bar pointing in opposing directions.

```
       Anode (A) ----|>|---- Cathode (K)    (Standard Diode)
       Anode (A) ----|/|---- Cathode (K)    (Zener Diode - Correct Opposing Flags)
```

- **Rule**: Flags **must point in opposing directions**. Drawing both flags in the same direction or as a symmetric Z/S is incorrect.

---

### 5.4 Shunt Regulator Circuit Principles & Boundaries

A basic Zener regulator consists of a series resistor $R$ and a Zener diode in parallel (shunt) with load $R_L$.

```
           +-------[ Series R ]-------+-------+
           |                          |       |
      (+)  |                        ----      |
    DC Source V_S                  / \ Zener  [ Load R_L ]  v_L = V_Z
      (-)  |                       --- D1     |
           |                          |       |
           +--------------------------+-------+
```

#### Conduction Boundaries
1. **Upper Bound**: Reverse current must not exceed $I_{Z,max}$ ($P_{Z,max} = V_Z I_{Z,max}$), or the diode will burn out.
2. **Lower Bound**: Reverse current must stay above the knee current ($I_Z > I_{ZK}$). If $I_Z \to 0$, the diode exits reverse breakdown and loses regulation.
3. **Regulation Quality**: Simple Zener shunt regulators provide "fair/so-so" regulation because the breakdown knee is not perfectly vertical (it has a finite dynamic resistance $R_Z$).

---

## 6. Exhaustive Step-by-Step Zener Shunt Regulator Analysis & Numerical Corrections

### 6.1 Circuit Parameters & Problem Setup
- **DC Input Source**: $V_S = 24\text{ V}$ (steady).
- **Series Resistor**: $R = 1.0\text{ k}\Omega$.
- **Variable Load Equivalent Resistance**: $R_L$ varies between $6.0\text{ k}\Omega$ and $1.2\text{ k}\Omega$.
- **Goal**: Find load voltage $V_L$ and evaluate regulation stability across load variations.

---

### 6.2 Step-by-Step Thevenin Reduction

Remove the Zener diode and calculate the Thevenin equivalent across the Zener nodes:

$$\text{Thevenin Voltage: } V_{Th} = V_S \left( \frac{R_L}{R + R_L} \right)$$

$$\text{Thevenin Resistance: } R_{Th} = R \parallel R_L = \frac{R \cdot R_L}{R + R_L}$$

---

### 6.3 Detailed In-Class Numerical Calculation & Slide Corrections

> [!WARNING]
> **Professor's In-Class Live Corrections**:
> During the lecture, the professor recalculated the slide values live and identified significant rounding errors on the pre-printed presentation slides. Below are the corrected, exact calculations demonstrated in class:

#### Case 1: $R_L = 6.0\text{ k}\Omega$ ($6\text{k}\Omega$)
- **Pre-printed Slide Value**: $20\text{ V}$ (Inaccurate).
- **Professor's Live Calculation**:
  $$V_{Th1} = 24\text{ V} \times \left( \frac{6.0\text{k}}{1.0\text{k} + 6.0\text{k}} \right) = 24 \times \frac{6}{7} \approx 20.57\text{ V}$$
- **Thevenin Resistance**:
  $$R_{Th1} = \frac{1.0\text{k} \times 6.0\text{k}}{1.0\text{k} + 6.0\text{k}} = \frac{6.0\text{k}}{7} \approx 857.14\text{ }\Omega$$

#### Case 2: $R_L = 1.2\text{ k}\Omega$ ($1.2\text{k}\Omega$)
- **Pre-printed Slide Value**: $12\text{ V}$ (Far off).
- **Professor's Live Calculation**:
  $$V_{Th2} = 24\text{ V} \times \left( \frac{1.2\text{k}}{1.0\text{k} + 1.2\text{k}} \right) = 24 \times \frac{1.2}{2.2} \approx 13.09\text{ V} \quad (\text{or } 13.1\text{ V})$$
- **Thevenin Resistance**:
  $$R_{Th2} = \frac{1.0\text{k} \times 1.2\text{k}}{1.0\text{k} + 1.2\text{k}} = \frac{1.2\text{k}}{2.2} \approx 545.45\text{ }\Omega$$

---

### 6.4 DC Load Line Graphical Intersection & Operating Q-Points

The load line equation for the network connected to the Zener diode is:

$$V_L = V_{Th} - I_Z R_{Th}$$

Plotting these two DC load lines ($V_{Th1} = 20.57\text{V}, R_{Th1} = 857\Omega$ and $V_{Th2} = 13.09\text{V}, R_{Th2} = 545\Omega$) onto the Zener reverse characteristic curve yields the operating Q-points:
- At $R_L = 6.0\text{ k}\Omega$: $V_L \approx -10.0\text{ V}$ (magnitude $10.0\text{ V}$).
- At $R_L = 1.2\text{ k}\Omega$: $V_L \approx -9.5\text{ V}$ (magnitude $9.5\text{ V}$).

---

### 6.5 Load Regulation Performance Evaluation

$$\Delta V_L = 10.0\text{ V} - 9.5\text{ V} = 0.5\text{ V}$$

**Conclusion**: Despite a **5-fold change in load resistance** ($6.0\text{ k}\Omega \to 1.2\text{ k}\Omega$) and a massive shift in current demand, the output voltage across the load changes by only **$0.5\text{ V}$**. This demonstrates the effectiveness of Zener shunt regulation in stabilizing voltage against severe load current variations.

---

## Key Exam Tips & Summary Summary
1. **Full-Wave Bridge**: $V_{O,peak} = V_{S,peak} - 2V_D$; uses standard 2-terminal secondary transformer.
2. **Center-Tapped Full-Wave**: $V_{O,peak} = V_{S,peak} - V_D$; uses 3-terminal center-tapped secondary transformer.
3. **Linear Power Supply Stages**: Transformer $\rightarrow$ Bridge Rectifier $\rightarrow$ Capacitor Filter $\rightarrow$ Voltage Regulator.
4. **Capacitor Filter Ripple**: $120\text{Hz}$ ripple frequency for $60\text{Hz}$ AC input; heavier load causes faster discharge and larger ripple.
5. **Voltage Regulator Rule**: Regulator requires minimum input ripple voltage $V_{ripple,min} \ge V_{OUT} + \Delta V_{headroom}$. Cannot step up voltage.
6. **Zener Voltage Availability**: $5.0\text{V}$ Zeners do NOT exist (use $4.7\text{V}$ or $5.1\text{V}$); minimum available Zener breakdown voltage is $\approx 3.0\text{V} - 3.3\text{V}$.
7. **Zener Power Dissipation Limit**: $P_{Z,max} = V_Z I_{Z,max}$; limits high-voltage Zener current capacity.
8. **Zener Shunt Regulator Calculations**: Always use Thevenin reduction ($V_{Th}, R_{Th}$) across Zener terminals before plotting DC load lines.
