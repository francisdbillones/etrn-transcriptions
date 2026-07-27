# Lecture Notes: ETRN2 - Lecture 11
**Topic:** DC Bias with Voltage Feedback (Collector Feedback), Circuit Analysis, Power Dissipation, Dual-Supply References, and Course Logistics  
**Target Path:** `/Users/francis/school/y2/t3/etrn2/lecs/transcriptions/notes/lec11_notes.md`

---

## 1. Class Logistics, Tech Glitches & Professor Tangents

### 1.1 Course Pacing & Section Synchronization
- **Class Progress**: The lecture is currently well ahead of the other section (Sir Tronto's class). The professor noted that while synchronizing sections is ideal, it is practically impossible at this point, so he tried to slow down slightly during this session.
- **Quiz Scheduling & ELPEP Conflict**:
  - The professor expressed concerns about having desynchronized quiz dates across sections.
  - A survey of student schedules for a potential Friday 7:30 AM quiz revealed conflicts with courses such as `GETHICS`, `LBYARCH`, `Most`, and `LBYTRN`.
  - *Course Note*: `LBYTRN` (Lab class under Sir John Nats) uses a unified approach across sections so it could theoretically be shifted, but other general education / lecture courses create hard conflicts.
  - **Proposed Solution**: 90% probability that the upcoming quiz will be scheduled on a **Saturday morning** to ensure no class conflicts across sections.
  - **Course Continuity**: Lessons will **not** pause while waiting for the quiz. The class will forge ahead with transistor switching applications and BJT AC small-signal amplification.

### 1.2 Tech Glitches & Cybersecurity Tangent (Arch Linux AUR Compromise)
- **Software Issues**: The professor experienced software updates interfering with presentation tools, screen sharing, and slide annotations ("Why is my Pycom gone?"). Red pen annotations were initially invisible to students due to screen capture/display mode settings, and slide fonts rendered differently.
- **Arch Linux Security Story / Trivia**:
  - During the technical troubleshooting, the professor brought up a recent severe hacking incident in the Arch Linux ecosystem.
  - Over **1,500 unmaintained packages** in the Arch User Repository (AUR) were compromised.
  - *Prof Note*: He clarified that he does not use Arch Linux himself, but voiced relief/concern that system updates didn't trigger a boot loop or introduce malware into his machine.

---

## 2. Review: Voltage Divider Bias & Approximation Validity

### 2.1 Exact vs. Approximate Analysis
- In the previous face-to-face lecture (last Thursday), the class finished analyzing **Voltage Divider Bias** using both Exact Analysis (Thevenin Equivalent) and Approximate Analysis.
- **Approximate Analysis Criterion**:
  $$\beta R_E \ge 10 R_2$$
- If the current drawn by the base ($I_B$) is negligible compared to the current flowing through the voltage divider network ($R_1$ and $R_2$), approximate analysis is valid.
- **Accuracy**: Results from approximate analysis typically fall within **5% to 10%** of exact analysis.
- **Real-World Engineering Insight**: The transistor's actual physical $\beta$ parameter varies by more than 5%–10% due to manufacturing tolerances and operating temperature variations anyway, making approximate analysis perfectly adequate for practical design.

---

## 3. DC Bias with Voltage Feedback (Collector Feedback Bias)

### 3.1 Concept and Negative Feedback Mechanism
In fixed-bias circuits, the base resistor $R_B$ is connected directly to $V_{CC}$ (a constant DC supply). As a result, changes in temperature or transistor parameters ($\beta$, $V_{BE}$) cause significant drift in the DC Q-point.

Connecting $R_B$ to the **collector** ($V_C$) instead of $V_{CC}$ introduces **DC negative feedback** that stabilizes the Q-point against temperature fluctuations:

```
[Temperature Rise] ---> [V_BE Shrinks/Decreases] 
                               |
                               v
                     [I_B Tends to Increase] 
                               |
                               v
                     [I_C = β * I_B Increases] 
                               |
                               v
            [V_RC = I_C * R_C Increases (Transistor Turns On Harder)]
                               |
                               v
                     [Collector Voltage V_C Drops] 
                               |
                               v
       [Voltage Across R_B Drops] ---> [I_B Pulled Back Down]
```

1. **Temperature Rise**: Causes base-emitter voltage $V_{BE}$ to shrink/decrease.
2. **Initial $I_B$ Trend**: Reduced $V_{BE}$ increases the voltage across $R_B$, causing base current $I_B$ to increase.
3. **$I_C$ Response**: Collector current $I_C = \beta I_B$ increases, turning the transistor on harder.
4. **$V_{RC}$ Drop**: Increased $I_C$ causes a higher voltage drop across collector resistor $R_C$ ($V_{RC} = I_C R_C$).
5. **Collector Voltage Drop**: As a result, collector voltage $V_C$ (or $V_{CE}$) drops.
6. **Negative Feedback Action**: Because $R_B$ is connected to $V_C$ (not $V_{CC}$), the lower $V_C$ reduces the potential difference across $R_B$, pulling $I_B$ back down.
7. **Equilibrium**: Two opposing tendencies counteract each other, establishing a stable Q-point nearly independent of temperature and less sensitive to $\beta$.

### 3.2 Professor Tangent: Feedback in Everyday Life
- The professor noted that feedback loops exist everywhere:
  - **Academic Feedback**: Taking a quiz and seeing your score provides feedback on your knowledge state, motivating you to study harder.
  - **Parental Feedback**: Parents use positive and negative feedback mechanisms (e.g., "Do well in your exams and I will finance your trip to X, Y, Z").

---

## 4. Mathematical Derivations & Slide Correction

### 4.1 Combined Collector Feedback & Emitter Stabilization
To achieve maximum Q-point stability, collector feedback can be combined with an emitter resistor ($R_E$).

```
        +V_CC
          |
          RC
          |---+--- Collector (V_C)
          |   |
          |  RB
          |   |
          |---+--- Base (V_B)
         / 
     --|  BJT
        \>
          |
          RE
          |
         GND
```

### 4.2 KVL Input Loop & **Slide Error Correction**

#### KVL Equation:
$$V_{CC} - I'_C R_C - I_B R_B - V_{BE} - I_E R_E = 0$$

Where $I'_C = I_C + I_B$ is the total current flowing through $R_C$.

> [!IMPORTANT]
> **Slide Error & Official Correction**:
> The presentation slide contained a typo/confusing symbol showing $I_B \le I_C$.
> **Official Correction**: The symbol MUST be $I_B \ll I_C$ (base current is *much, much less than* collector current).
> The professor pointed out that the borrowed slide deck had contained this error for years and instructed students to refer to the lecture video for the correction!

#### Approximations:
Since $I_B \ll I_C$:
$$I'_C = I_C + I_B \approx I_C$$
$$I_E = I_C + I_B \approx I_C = \beta I_B$$

Substituting $I_C = \beta I_B$ and $I_E \approx \beta I_B$ into KVL:
$$V_{CC} - \beta I_B R_C - I_B R_B - V_{BE} - \beta I_B R_E = 0$$
$$V_{CC} - V_{BE} = I_B \left[ R_B + \beta (R_C + R_E) \right]$$

#### Derived Base Current ($I_B$) Formula:
$$I_B = \frac{V_{CC} - V_{BE}}{R_B + \beta (R_C + R_E)}$$

> [!TIP]
> **Professor Exam Advice**:
> The $I_B$ equation for voltage feedback bias is **not intuitive by simple inspection** due to the algebraic grouping of $\beta(R_C + R_E)$ in the denominator. The professor strongly recommended **committing this exact formula to memory**!

### 4.3 KVL Output Loop & $V_{CE}$ Formula

#### KVL Equation:
$$V_{CC} - I'_C R_C - V_{CE} - I_E R_E = 0$$

Using $I'_C \approx I_C$ and $I_E \approx I_C$:
$$V_{CE} = V_{CC} - I_C (R_C + R_E)$$

#### Collector Voltage ($V_C$):
$$V_C = V_{CC} - I_C R_C \quad \text{or} \quad V_C = V_{CE} + V_E$$

---

## 5. Exhaustive Worked Examples

### 5.1 Example 1: Voltage Feedback Bias with AC Filter Network

#### Given Parameters:
- Supply Voltage: $V_{CC} = 18\text{ V}$
- Base Resistors in Series: $R_{B1} = 91\text{ k}\Omega$, $R_{B2} = 110\text{ k}\Omega$
- Collector Resistor: $R_C = 3.3\text{ k}\Omega$
- Emitter Resistor: $R_E = 510\ \Omega = 0.51\text{ k}\Omega$
- Transistor Gain: $\beta = 75$, $V_{BE} = 0.7\text{ V}$

```
       +18V
        |
       3.3k
        |----+----- Collector (V_C)
        |    |
       91k  110k (Series RB = 201k)
        |----+
        |    |
        |   [Capacitor to GND]
        |
      Base
```

#### Rule for DC Analysis:
> [!NOTE]
> **Capacitors in DC Analysis**: Under DC steady-state, capacitors act as **OPEN CIRCUITS**. Mentally erase all capacitors when solving DC bias points. The capacitor between $R_{B1}$ and $R_{B2}$ is an AC filtering/decoupling network designed to allow AC signal amplification without altering the DC Q-point.

#### Step-by-Step Solution:
1. **Total Base Resistance ($R_B$)**:
   $$R_B = 91\text{ k}\Omega + 110\text{ k}\Omega = 201\text{ k}\Omega$$

2. **Base Current ($I_B$)**:
   $$I_B = \frac{V_{CC} - V_{BE}}{R_B + \beta (R_C + R_E)}$$
   $$I_B = \frac{18\text{ V} - 0.7\text{ V}}{201\text{ k}\Omega + 75 \times (3.3\text{ k}\Omega + 0.51\text{ k}\Omega)}$$
   $$I_B = \frac{17.3\text{ V}}{201\text{ k}\Omega + 75 \times 3.81\text{ k}\Omega} = \frac{17.3\text{ V}}{201\text{ k}\Omega + 285.75\text{ k}\Omega} = \frac{17.3\text{ V}}{486.75\text{ k}\Omega}$$
   $$\mathbf{I_B = 35.54\ \mu A}$$

3. **Collector Current ($I_C$)**:
   $$I_C = \beta I_B = 75 \times 35.54\ \mu\text{A} = 2.6656\text{ mA} \approx \mathbf{2.67\text{ mA}}$$

4. **Collector Voltage ($V_C$)**:
   $$V_C = V_{CC} - I_C R_C = 18\text{ V} - (2.6656\text{ mA} \times 3.3\text{ k}\Omega) = 18\text{ V} - 8.796\text{ V}$$
   $$\mathbf{V_C = 9.20\text{ V}}$$

*Professor Banter*: The professor poked fun at the class's silence ("Mayabang, ayaw magtanong!"), warning them: *"Pakitaan niyo ako pagdating ng quiz, dapat perfect ang sagot!"*

---

### 5.2 Example 2: Standard Voltage Feedback Bias

#### Given Parameters:
- Supply Voltage: $V_{CC} = 20\text{ V}$
- Base Resistor: $R_B = 460\text{ k}\Omega$
- Collector Resistor: $R_C = 4\text{ k}\Omega$
- Emitter Resistor: $R_E = 1\text{ k}\Omega$
- Transistor Gain: $\beta = 100$, $V_{BE} = 0.7\text{ V}$

#### Step-by-Step Solution:
1. **Base Current ($I_B$)**:
   $$I_B = \frac{20\text{ V} - 0.7\text{ V}}{460\text{ k}\Omega + 100 \times (4\text{ k}\Omega + 1\text{ k}\Omega)} = \frac{19.3\text{ V}}{460\text{ k}\Omega + 500\text{ k}\Omega} = \frac{19.3\text{ V}}{960\text{ k}\Omega}$$
   $$\mathbf{I_B = 20.1\ \mu A}$$

2. **Collector Current ($I_C$)**:
   $$I_C = \beta I_B = 100 \times 20.104\ \mu\text{A} = \mathbf{2.01\text{ mA}}$$

3. **Collector-Emitter Voltage ($V_{CE}$)**:
   $$V_{CE} = V_{CC} - I_C (R_C + R_E) = 20\text{ V} - (2.01\text{ mA} \times 5\text{ k}\Omega)$$
   $$V_{CE} = 20\text{ V} - 10.05\text{ V} = \mathbf{9.95\text{ V}} \quad (\text{Exact: } 9.948\text{ V})$$

> [!TIP]
> **Sanity Checking Technique**:
> Always perform quick mental estimations to verify calculated results. Here, $I_C \approx 2\text{ mA}$, $(R_C + R_E) = 5\text{ k}\Omega \implies V_{\text{drop}} \approx 10\text{ V}$, so $V_{CE} \approx 20 - 10 = 10\text{ V}$. If your calculator yields a negative $V_{CE}$ or a voltage exceeding $V_{CC}$, re-check your work!

---

### 5.3 Example 3: Voltage Divider Bias Power Dissipation ($P_C$)

#### Problem Statement:
Determine the power dissipated by the transistor ($P_C$) in **milliwatts (mW)** using approximate analysis if acceptable.

#### Given Parameters:
- $V_{CC} = 12\text{ V}$
- Voltage Divider: $R_1 = 27\text{ k}\Omega$, $R_2 = 7\text{ k}\Omega$
- Collector Resistor: $R_C = 3.8\text{ k}\Omega$
- Emitter Resistor: $R_E = 1.2\text{ k}\Omega$
- Transistor Gain: $\beta = 80$, $V_{BE} = 0.7\text{ V}$

#### Step-by-Step Solution:

1. **Check Approximation Validity**:
   $$\beta R_E = 80 \times 1.2\text{ k}\Omega = 96\text{ k}\Omega$$
   $$10 R_2 = 10 \times 7\text{ k}\Omega = 70\text{ k}\Omega$$
   Since $96\text{ k}\Omega \ge 70\text{ k}\Omega$, approximate analysis is **VALID**.

2. **Base Voltage ($V_B$)**:
   $$V_B = V_{CC} \left( \frac{R_2}{R_1 + R_2} \right) = 12\text{ V} \times \frac{7\text{ k}\Omega}{27\text{ k}\Omega + 7\text{ k}\Omega} = 12 \times \frac{7}{34} = \mathbf{2.47\text{ V}}$$

3. **Emitter Voltage ($V_E$) & Emitter Current ($I_E$)**:
   $$V_E = V_B - V_{BE} = 2.47\text{ V} - 0.7\text{ V} = \mathbf{1.77\text{ V}}$$
   $$I_E = \frac{V_E}{R_E} = \frac{1.77\text{ V}}{1.2\text{ k}\Omega} = \mathbf{1.476\text{ mA}}$$
   $$I_C \approx I_E = \mathbf{1.476\text{ mA}}$$

4. **Collector Voltage Drop ($V_{RC}$) & $V_{CE}$**:
   $$V_{RC} = I_C R_C = 1.476\text{ mA} \times 3.8\text{ k}\Omega = \mathbf{5.61\text{ V}}$$
   $$V_{CE} = V_{CC} - V_{RC} - V_E = 12\text{ V} - 5.61\text{ V} - 1.77\text{ V} = \mathbf{4.62\text{ V}} \quad (\text{Exact: } 4.623\text{ V})$$
   *Transistor Operating State*: $V_{CE} = 4.62\text{ V} > 0.2\text{ V}$, confirming the transistor is ON and operating in its active region.

5. **Transistor Power Dissipation ($P_C$)**:
   $$P_C = I_C \times V_{CE} = 1.476\text{ mA} \times 4.623\text{ V} = \mathbf{6.82\text{ mW}}$$

---

### 5.4 Example 4 & Concept: Dual-Supply Emitter Bias & Andrew Hall Basement Analogy

#### Dual-Supply Configuration:
A BJT circuit operating between a positive rail $+V_{CC} = +12\text{ V}$ and a negative rail $-V_{EE} = -12\text{ V}$ (rather than ground $0\text{ V}$).

```
      +12V (Top Rail / Andrew Hall 12th Floor)
        |
       RC
        |--- Collector
       \
     --|  BJT
        \>
        |--- Emitter
       RE
        |
      -12V (Bottom Rail / Basement 12)
```

#### Professor's Andrew Hall Building Analogy:
- **Ground Level ($0\text{ V}$)**: Where buses, cars, stoplights, and pedestrians are walking on the street.
- **Top Floor ($+12\text{ V}$)**: The 12th floor of Andrew Hall (where the prayer area is; prof comment: *"I never go to the 12th floor"*).
- **Basement Level ($-12\text{ V}$)**: Underground basement parking extending down to Basement 12 ($B12$).
- **Shifting the Reference Frame**:
  - Normally, measurements are referenced to Ground ($0\text{ V}$).
  - If you shift your reference frame to Basement 12 ($-12\text{ V}$ as virtual $0\text{ V}$):
    - Negative rail ($-12\text{ V}$) becomes $0\text{ V}$ reference.
    - Ground ($0\text{ V}$) becomes $+12\text{ V}$ above reference.
    - Positive rail ($+12\text{ V}$) becomes $+24\text{ V}$ above reference!
- **Key Takeaway**: Total potential difference across the rails is $V_{\text{total}} = V_{CC} - (-V_{EE}) = 12 - (-12) = 24\text{ V}$. Standard BJT equations do not change because transistors respond to relative potential differences across terminals, regardless of absolute ground labeling.

---

## 6. Summary Formula & Concept Sheet

| Configuration / Parameter | Formula / Condition | Notes |
|---|---|---|
| **Voltage Feedback Bias ($I_B$)** | $I_B = \frac{V_{CC} - V_{BE}}{R_B + \beta(R_C + R_E)}$ | **Must Memorize!** Denominator includes $\beta(R_C+R_E)$. |
| **Voltage Feedback Bias ($V_{CE}$)** | $V_{CE} = V_{CC} - I_C(R_C + R_E)$ | Intuitive by inspection ($I_C \approx I_E$). |
| **Collector Voltage ($V_C$)** | $V_C = V_{CC} - I_C R_C$ | Direct calculation from top rail. |
| **Approximate Analysis Criterion** | $\beta R_E \ge 10 R_2$ | Valid for Voltage Divider Bias. |
| **Transistor Power Dissipation** | $P_C = I_C V_{CE}$ | Expressed in milliwatts ($\text{mW}$). |
| **Capacitors in DC Analysis** | Open Circuit ($Z_C \rightarrow \infty$) | Mentally erase for DC Q-point solving. |
| **Dual-Supply Potential** | $V_{\text{total}} = V_{CC} - (-V_{EE})$ | Shift reference frame to $-V_{EE}$. |

---

## 7. Next Steps & Upcoming Topics
1. **Transistor Switching Applications**: BJT operating in Cutoff and Saturation modes (to be completed Thursday as filler).
2. **AC Analysis of BJT Amplifiers**: Small-signal AC models ($r_e$ model, hybrid parameters) and AC voltage/current gain calculations following the quiz.
