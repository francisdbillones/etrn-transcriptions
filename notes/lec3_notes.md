# Lecture Notes: ETRN2 - Lecture 3 (Diode Circuit Analysis & Rectification)

---

## 1. Lecture Overview & Administrative Announcements

### Class Schedule & Venue Modality Rules
* **Lecture Session (Thursday):** **ONLINE** for all students across both lecture sections. (Reason: Combined lecture enrollment spans three separate laboratory sections).
* **Laboratory Session (Thursday):** **FACE-TO-FACE** for **Lab Section 1 ONLY** at **11:00 AM** in **Room G407**.
* **Upcoming Lab Activity:** Laboratory Experiment 2 will cover Diode Rectification Action, Clipping, and Clamping Circuits.
* **Lecture Timeframe:** 8:00 AM – 9:00 AM (Wrapped up at 9:00 AM after reviewing half-wave rectifiers and introducing full-wave rectifiers).

---

## 2. Diode Circuit Analysis & Worked Example Problems

### Problem 1: Series Diode Circuit with Reverse-Biased Diode

#### Circuit Topology
* **Power Source ($V_{CC}$):** $+12\text{ V}$ DC node.
* **Components:** Diode 1 ($D_1$, Silicon), Diode 2 ($D_2$, Silicon), and Load Resistor ($R = 5.6\text{ k}\Omega$) connected to Ground.
* **Attempted Current Direction:** Clockwise (from $+12\text{ V}$ towards ground).
* **Diode Orientation:** $D_1$ is anode-to-cathode (forward direction); $D_2$ is cathode-to-anode (reverse direction).

```
   +12V ----[ D1 (Si) ]----[ D2 (Si) ]----+----[ R = 5.6k ]---- GND
            (Forward)      (Reverse)      |
                                          +---- Vo
```

#### Analytical Step-by-Step Solution
1. **Diode Conduction States:**
   * $D_1$ is **forward-biased** (matches attempted current flow).
   * $D_2$ is **reverse-biased** (opposes attempted current flow).
2. **Diode & Circuit Current ($I_D$, $I_R$):**
   * Reverse-biased diode $D_2$ acts as an **open circuit**.
   * **Practical Reverse Leakage Current Trivia:** While theoretical ideal current is zero, real silicon diodes in reverse bias conduct a minute leakage current ($I_s$) in the **nanoampere range** ($1\text{ nA} - 10\text{ nA}$). For circuit calculations, it is practically $0\text{ A}$.
   * Since this is a single series loop: $I_R = I_D \approx 0\text{ A}$.
3. **Resistor Voltage ($V_R$) & Output Voltage ($V_O$):**
   * $V_R = I_R \times R = 0\text{ A} \times 5.6\text{ k}\Omega = 0\text{ V}$.
   * Output voltage node $V_O = V_R = 0\text{ V}$.
4. **Reverse Voltage Across Diode 2 ($V_{D2}$):**
   * Using **Model 2 (Simplified / Constant Voltage Drop Model)** for forward-biased silicon diode $D_1$: $V_{D1} = 0.7\text{ V}$.
   * Apply Kirchhoff’s Voltage Law (KVL) around the clockwise loop:
     $$-12\text{ V} + V_{D1} + V_{D2} + V_R = 0$$
     $$-12\text{ V} + 0.7\text{ V} + V_{D2} + 0\text{ V} = 0 \implies V_{D2} = 12\text{ V} - 0.7\text{ V} = 11.3\text{ V}$$

#### Key Concepts & Professor Insights
* In a single series loop, a single reverse-biased diode blocks total current flow and absorbs the entire remaining source voltage (minus the $0.7\text{ V}$ forward drop of conducting diodes).
* *Professor Comment:* "Not really much of a trick there, but what the hell... getting to know your diode characteristics, that's what it's going to turn out to be."

---

### Problem 2: Parallel Silicon Diodes with Series Resistor

#### Circuit Topology
* **DC Source ($E$):** $10\text{ V}$ DC.
* **Series Resistor ($R$):** $330\ \Omega$.
* **Parallel Diode Network:** Two silicon diodes ($D_1$ and $D_2$) connected in parallel to ground.
* **Output Node ($V_O$):** Taken directly across the parallel diode pair.

```
   +10V ----[ R = 330 ohm ]----+----+---- Vo
                                |    |
                             [D1]  [D2]  (Both Silicon, Model 2)
                                |    |
                               GND  GND
```

#### Professor's Pet Peeve & Model Consistency Rule
* **Textbook Notation Preference:** The professor explicitly noted a preference for explicitly drawn DC voltage sources over floating terminal labels (e.g., "$+10\text{ V}$ floating node"), stating: *"What I hate about this is it's just a floating thing. For my brain, I have more difficulties thinking about it. But here we have an explicit voltage source."*
* > [!IMPORTANT]
  > **Strict Model Consistency Rule:** Never mix and match diode models within the same problem (e.g., using Model 1 for $D_1$ and Model 2 for $D_2$). Doing so yields physically invalid/weird results. If Model 2 is selected, it must be applied uniformly to all identical diodes in the circuit.

#### Deep Dive: Load Line & Resistance Models (Static vs Dynamic)
* **Static (DC) Resistance ($R_D$):** Applies to steady-state non-changing DC sources (like this $10\text{ V}$ source).
* **Dynamic (AC) Resistance ($r_d$):** Applies to changing AC signals.
* Under **Model 2**, the forward equivalent resistance $R_D = 0\ \Omega$ above the $0.7\text{ V}$ knee.
* Under **Model 3**, each diode is modeled as a $0.7\text{ V}$ battery in series with an internal dynamic resistance $R_d$. If both diodes are identical, their internal resistances $R_d$ are identical.
* Because both branches have identical characteristics, total input current $I_1$ **splits equally** between $D_1$ and $D_2$.

#### Step-by-Step Solution
1. **Output Voltage ($V_O$) & Diode Voltages ($V_{D1}, V_{D2}$):**
   * Since $D_1$, $D_2$, and $V_O$ are all in parallel:
     $$V_{D1} = V_{D2} = V_O = 0.7\text{ V}$$
2. **Total Loop Current ($I_1$):**
   * Apply KVL across the source and series resistor:
     $$I_1 = \frac{E - V_O}{R} = \frac{10\text{ V} - 0.7\text{ V}}{330\ \Omega} = \frac{9.3\text{ V}}{330\ \Omega} \approx 0.02818\text{ A} = 28.2\text{ mA}$$
     *(Student Diane approximated this as $\approx 30\text{ mA}$; exact value is $28.18\text{ mA} \approx 28.2\text{ mA}$).*
3. **Branch Currents ($I_{D1}, I_{D2}$):**
   * Current splits equally:
     $$I_{D1} = I_{D2} = \frac{I_1}{2} = \frac{28.2\text{ mA}}{2} = 14.1\text{ mA}$$

---

### Problem 3: Anti-Parallel Diode Pair Between Two DC Sources

#### Circuit Topology
* **Left Source ($E_1$):** $20\text{ V}$ DC.
* **Right Source ($E_2$):** $4\text{ V}$ DC.
* **Series Resistor ($R$):** $2.2\text{ k}\Omega$.
* **Parallel Diode Pair:** Two diodes connected back-to-back in parallel ($D_1$ pointing left-to-right, $D_2$ pointing right-to-left).

```
   +20V ----[ R = 2.2k ]----+---|>|---(D1)---+---- +4V
                            |                |
                            +---|<|---(D2)---+
```

#### Step-by-Step Solution & Analysis
1. **Current Flow Direction:**
   * Potential difference: $E_1 (20\text{ V}) > E_2 (4\text{ V})$.
   * Net potential pushes current from **left to right** (following standard engineering current convention).
2. **Conducting vs. Non-Conducting Diode:**
   * $D_1$ (pointing left-to-right) is **forward-biased** ($V_{D1} = 0.7\text{ V}$).
   * $D_2$ (pointing right-to-left) is **reverse-biased** (acts as an open circuit).
3. **Loop Current ($I$) Calculation:**
   * KVL around the loop:
     $$-E_1 + V_R + V_{D1} + E_2 = 0$$
     $$V_R = E_1 - E_2 - V_{D1} = 20\text{ V} - 4\text{ V} - 0.7\text{ V} = 15.3\text{ V}$$
     $$I = \frac{V_R}{R} = \frac{15.3\text{ V}}{2.2\text{ k}\Omega} = \frac{15.3\text{ V}}{2200\ \Omega} \approx 6.9545\text{ mA} \approx 6.95\text{ mA}$$

#### Professor Calculator Incident Trivia
* During this calculation, the professor experienced a calculator mode error: *"Let's use my little calculator here... I pressed something. Oops. Now it's in a different mode, but let's see if it still works... 6.95 milliampere. Unless my calculator's joking. There's a weird symbol that suddenly came out. I don't know what I pressed. Darn it!"*

---

### Problem 4: Complex Series-Parallel Diode Circuit & Case Analysis

#### Circuit Topology
* **DC Source ($E$):** $20\text{ V}$ DC.
* **Series Diode ($D_1$):** Silicon ($V_{D1} = 0.7\text{ V}$).
* **Parallel Combination:** Resistor $R_1$ in parallel with Diode $D_2$ (Silicon).
* **Series Load Resistor ($R_2$):** $5.6\text{ k}\Omega$ connected to ground.

```
   +20V ----[ D1 (Si) ]----+--------+----[ R2 = 5.6k ]---- GND
                           |        |
                         [ R1 ]   [ D2 ]
                           |        |
                           +--------+
```

---

#### Scenario A: Standard High-Resistance Case ($R_1 = 3.3\text{ k}\Omega$)

1. **Operating Assumption:** Both $D_1$ and $D_2$ receive sufficient current to be forward-biased ($V_{D1} = V_{D2} = 0.7\text{ V}$).
2. **Total Series Loop Current ($I_2$ through $R_2$):**
   * Apply outer KVL:
     $$-20\text{ V} + V_{D1} + V_{D2} + V_{R2} = 0$$
     $$V_{R2} = 20\text{ V} - 0.7\text{ V} - 0.7\text{ V} = 18.6\text{ V}$$
     $$I_2 = \frac{18.6\text{ V}}{5.6\text{ k}\Omega} = \frac{18.6\text{ V}}{5600\ \Omega} \approx 3.3214\text{ mA} \approx 3.32\text{ mA}$$
3. **Resistor Branch Current ($I_1$ through $R_1$):**
   * Since $R_1 \parallel D_2$, $V_{R1} = V_{D2} = 0.7\text{ V}$.
     $$I_1 = \frac{0.7\text{ V}}{3.3\text{ k}\Omega} = \frac{0.7\text{ V}}{3300\ \Omega} \approx 212.12\ \mu\text{A} = 0.21212\text{ mA}$$
4. **Diode Currents ($I_{D1}, I_{D2}$):**
   * Total current entering parallel node: $I_{D1} = I_2 = 3.32\text{ mA}$.
   * Apply KCL at parallel node:
     $$I_{D2} = I_2 - I_1 = 3.32\text{ mA} - 0.21212\text{ mA} = 3.10788\text{ mA} \approx 3.11\text{ mA}$$
5. **Validity Verification:** Since $I_1 (0.212\text{ mA}) < I_2 (3.32\text{ mA})$, forward current through $D_2$ is positive ($I_{D2} > 0$), confirming $D_2$ is ON.

---

#### Scenario B: Professor's Critical Counter-Example & Exam Warning ($R_1 = 100\ \Omega$)

> [!WARNING]
> **CRITICAL EXAM TRAP / KCL VIOLATION FALLACY:**
> Never automatically assume a parallel diode turns ON at $0.7\text{ V}$ without checking if the parallel resistor steals all available current!

1. **Breakdown of the $0.7\text{ V}$ Assumption:**
   * If $V_{D2} = 0.7\text{ V}$ with $R_1 = 100\ \Omega$, required branch current $I_1 = \frac{0.7\text{ V}}{100\ \Omega} = 7.0\text{ mA}$.
   * But total loop current supplied from the source is only $I_2 \approx 3.32\text{ mA}$!
   * *Professor's Comment:* *"How can 7 mA come out of $R_1$ when only 3.32 mA entered the node? Mas malaki pa ang lumabas kesa dun sa pumasok!"*
   * This violates Kirchhoff's Current Law (KCL). Thus, $D_2$ **CANNOT** reach $0.7\text{ V}$ and remains **OFF**.

2. **Rigorous Two-Case Analytical Decision Framework:**

| Analytical Case | Physical Condition | Equivalent State of $D_2$ | Governing Governing Equations |
| :--- | :--- | :--- | :--- |
| **Case 1 ($D_2$ ON)** | $I_1 = \frac{0.7\text{ V}}{R_1} < I_{\text{total}}$ | Voltage clamped at $0.7\text{ V}$ | $V_{R2} = E - 1.4\text{ V}$, $I_{\text{total}} = \frac{V_{R2}}{R_2}$ |
| **Case 2 ($D_2$ OFF)** | $I_1 = \frac{0.7\text{ V}}{R_1} \ge I_{\text{total}}$ | **Open Circuit** (Voltage below knee) | $I_{\text{total}} = \frac{E - 0.7\text{ V}}{R_1 + R_2}$, $V_{D2} = I_{\text{total}} \times R_1$ |

3. **Case 2 Rigorous Calculation ($R_1 = 100\ \Omega$):**
   * Assume $D_2$ is an open circuit. KVL around single loop ($E \to D_1 \to R_1 \to R_2 \to \text{GND}$):
     $$-20\text{ V} + 0.7\text{ V} + I(R_1 + R_2) = 0$$
     $$I = \frac{20\text{ V} - 0.7\text{ V}}{100\ \Omega + 5600\ \Omega} = \frac{19.3\text{ V}}{5700\ \Omega} \approx 3.38596\text{ mA} \approx 3.386\text{ mA}$$
   * Calculate node voltage across $R_1$ (and thus $V_{D2}$):
     $$V_{D2} = V_{R1} = I \times R_1 = 3.386\text{ mA} \times 100\ \Omega = 0.3386\text{ V}$$
   * **Verification:** $V_{D2} = 0.3386\text{ V} < 0.7\text{ V}$ (well below the $0.7\text{ V}$ conduction knee). $D_2$ receives zero current ($I_{D2} = 0\text{ A}$) and acts as an open circuit. **Case 2 is verified as the physically correct solution.**

---

## 3. Diode Applications & Fundamental Rectification Theory

### Primary Application: Rectification
* **Etymology & Historical Trivia:** The word *"rectifier"* / *"rectify"* dates back to **1611**.
* **Everyday English vs. Electronics Engineering Definitions:**
  * *Layman's English:* To "rectify" means to correct a mistake or fix a problem ("rectify a situation").
  * *Electronics Engineering:* To "rectify" means to convert an **Alternating Current (AC)** signal into a **Direct Current (DC)** signal of unified polarity.

---

### Redefining Direct Current (DC): CINETRON 1 vs. ETRN 2

> [!IMPORTANT]
> **Fundamental Re-definition of Direct Current (DC):**
> * **CINETRON 1 Misconception:** Students often incorrectly assume DC must be a perfectly flat, non-changing, steady-state continuous voltage/current.
> * **ETRN 2 Engineering Reality:** **DC fundamentally means UNIPOLARITY** (a signal that maintains a single polarity: purely positive or purely negative). It does **NOT** need to be steady or constant!
> * **Battery Decay Example:** A battery's output current declines over time as it drains; it is not flat/steady, yet it is classified as DC because its polarity never reverses.
> * **Rectified Waves:** A series of pulsating sine waves that stay above zero volts is technically a **DC signal**.

```
AC Waveform (Bipolar):          Rectified DC Waveform (Unipolar):
     +V  .--.                        +V  .--.  .--.  .--.
        /    \                          /    \/    \/    \
  -----'------'----- t            -----'------'------'----- t
       \      /                          (Never crosses zero)
     -V '----'
```

### Real-World Application Context & Device Physics Trivia
* **Power Distribution:** Grid electricity (e.g., Meralco) is AC.
* **Consumer Appliances:** Internal electronics (laptops, microprocessors, LED lights) require DC.
* **Internal Power Supplies:** Diodes serve at the input stage of power adapters to convert incoming grid AC to DC.
* **LED Device Physics Trivia:** LEDs (Light Emitting Diodes) are semiconductor diodes. They only emit light when forward-biased under the correct DC polarity. Applying reverse bias blocks current and prevents light emission.

---

## 4. Half-Wave Rectifier (HWR) Circuits & Waveform Analysis

### Circuit Configuration
An AC input source $v_{in}(t) = V_m \sin(\omega t)$ connected in series with a diode $D$ and a load resistor $R_L$.

```
     v_in(t) ~~~~~----+----[ Diode D ]----+---- v_o(t)
                      |                   |
                     GND               [ R_L ]
                                          |
                                         GND
```

### Operational Modes
1. **Positive Half-Cycle ($0 \le \omega t \le \pi$, $v_{in} > 0$):**
   * Anode is positive relative to cathode.
   * Diode is **forward-biased** (closed switch).
   * Current flows clockwise through $R_L$.
   * Output voltage: $v_o(t) = v_{in}(t) - V_D$.
2. **Negative Half-Cycle ($\pi \le \omega t \le 2\pi$, $v_{in} < 0$):**
   * Cathode is positive relative to anode.
   * Diode is **reverse-biased** (open circuit).
   * Zero current flows through $R_L$.
   * Output voltage: $v_o(t) = 0\text{ V}$.

---

### Diode Model Impact on HWR Output

* **Model 1 (Ideal Diode):**
  * Conduction begins at $v_{in} = 0\text{ V}$.
  * Peak output voltage $V_{o,\text{peak}} = V_m$.
* **Model 2 / Model 3 / Actual Diode ($V_D = 0.7\text{ V}$):**
  * Conduction is delayed until $v_{in}(t)$ exceeds the $0.7\text{ V}$ knee.
  * Peak output voltage $V_{o,\text{peak}} = V_m - V_D = V_m - 0.7\text{ V}$.

#### Signal Amplitude Sensitivity (Laboratory Experiment 2 Preview)
* **Small Signals ($V_m = 1\text{ V}$ peak):** The $0.7\text{ V}$ diode drop consumes 70% of the signal amplitude, leaving a distorted output peak of only $0.3\text{ V}$.
* **Large Signals ($V_m = 10\text{ V}$ peak):** The $0.7\text{ V}$ drop is a minor fraction (7%), yielding an output peak of $9.3\text{ V}$ that closely tracks the input half-sine.

---

### Mathematical Formulas for Half-Wave Rectification

#### Average DC Output Voltage ($V_{\text{dc}}$)
* **Ideal Diode:**
  $$V_{\text{dc}} = \frac{1}{2\pi} \int_{0}^{\pi} V_m \sin(\theta) d\theta = \frac{V_m}{\pi} \approx 0.318 V_m$$
* **Practical Diode ($V_D = 0.7\text{ V}$):**
  $$V_{\text{dc}} \approx 0.318 (V_m - V_D)$$

* **Derivation Note on 0.318:** The factor $0.318$ is $\frac{1}{\pi}$, representing the average value of a single half-sine pulse over a full $2\pi$ period.

#### Diode Reversal
* Reversing the diode direction (cathode to source) suppresses positive half-cycles and passes negative half-cycles, producing a negative unipolar DC output ($V_{\text{dc}} \approx -0.318 V_m$).

---

## 5. Introduction to Full-Wave Rectifiers (FWR)

* **Concept Preview:** A Full-Wave Rectifier converts **both** positive and negative half-cycles of the AC input into positive output pulses across the load.
* **Center-Tapped Dual-Diode Topology:**
  * Uses a center-tapped transformer winding producing two complementary AC voltages and two diodes.
  * $D_1$ conducts during positive half-cycles; $D_2$ conducts during negative half-cycles.
  * Resulting output across load: Continuous positive half-sine pulses ($f_{out} = 2 f_{in}$).
  * Full mathematical derivation and bridge topologies will be covered in the next lecture session.

---

## 6. Comprehensive Summary of Professor Tangents & Side Notes

* **Classroom Energy Check:** Encouraged sleepy students to wake up and drink coffee ("*Gising na. Hindi pa gising. Ayan. O, kape, kape, kape.*").
* **Textbook Schematics:** Expressed annoyance at floating node voltage labels, preferring explicitly drawn ground-referenced voltage source symbols.
* **Model Selection Rule:** Warned against mixing diode models (Model 1 vs Model 2) in the same circuit to prevent invalid results.
* **Calculator Glitch:** Mode switch incident during Problem 3 current calculation.
* **Exam Strategy:** Highlighted the Case 1 vs. Case 2 parallel diode-resistor trap ($R_1 = 100\ \Omega$) as a prime example of complex exam problems requiring KCL verification.
* **Class Logistics:** Clarified that Thursday lecture is online for everyone, while Thursday 11:00 AM lab in Room G407 is strictly face-to-face for Lab Section 1 only.
