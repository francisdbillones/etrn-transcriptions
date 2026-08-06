# Lecture 20: Transistor Switching Applications, Motor Interfacing, and Darlington Configurations

**Course:** ETRN2 (Electronics 2)  
**Topic:** Transistor Switching (Low-Side vs. High-Side, NPN vs. PNP Selection), Power Loss Minimization, Microcontroller/Digital Interface to DC Motors, Darlington Transistor Configuration (Mathematical Derivation, Internal/External Bleeder Resistors, Performance Trade-offs)  
**Source File:** [lec20.txt](file:///Users/francis/school/y2/t3/etrn2/lecs/transcriptions/without_timestamps/lec20.txt)  
**Notes File:** [lec20_notes.md](file:///Users/francis/school/y2/t3/etrn2/lecs/transcriptions/notes/lec20_notes.md)  

---

## 1. Course Administrative Notes & Classroom Dynamics

### 1.1 Class Logistics & Upcoming Exam Announcement
* **Exam Schedule:** 
  * Upcoming exam scheduled for **Saturday at 7:30 AM**.
  * Professor emphasized punctual attendance: *"Don't be late or I might be late, but just remember... knock early."*
  * Detailed format and specific coverage rules were announced directly in class.
  * Recommended materials for solving: White version of yellow pad paper, calculators, standard writing tools.

### 1.2 Feedback on Student Quiz Performance
* **Grading Assessment Observations:**
  * The professor noted extreme variances across submitted quiz papers.
  * Common errors included **gross mathematical/order-of-magnitude mistakes**:
    * Example highlighted: An expected numerical answer of around **21** resulted in student submissions as high as **500**.
    * Unrealistic power calculations resulting in negative power values or current calculations exceeding physical supply constraints (e.g., calculating amperes from a small $10\text{ V}$ supply).
  * **Key takeaway for students:** Always perform sanity checks on final calculated values against physical circuit constraints (e.g., maximum supply voltage $V_{CC}$ and load limits).

---

## 2. Transistor Switching Fundamentals

Beyond linear amplification, BJT transistors function as electronic ON/OFF switches controlled by base current ($I_B$) or gate voltage ($V_{GS}$ in FETs).

```
                      ON / OFF Electronic Switch Concept
                      
           +V_CC                                 +V_CC
             |                                     |
           [LOAD] (e.g., Lamp/Horn)              [LOAD]
             |                                     |
             +--------+                            +--------+
                      |                                     |
                / (Switch Open)                       | (Switch Closed)
                      |                                     |
                     GND                                   GND
              [ CUT-OFF STATE ]                     [ SATURATION STATE ]
```

### 2.1 Key Objectives of Transistor Switching
* **Binary Behavior:** Operate purely in binary states—**Fully ON** (Saturation) or **Fully OFF** (Cut-off). No intermediate linear operation is desired.
* **Maximum Power Transfer to Load:** Deliver maximum supply power to the target load (e.g., automotive headlights, burglar alarm horns, solenoid actuators, motors).
* **Minimizing Switch Power Losses:** Reduce thermal power dissipation across the switching transistor to prevent overheating, avoiding bulky/costly heatsinks and maintaining high power efficiency.

### 2.2 Power Loss & Saturation Analysis

When a transistor switch is turned ON:
$$\text{Power Dissipated in Transistor } (P_{sw}) = V_{CE(\text{sat})} \times I_C$$

```
                       Transistor Switch Loss Model
                       
                           +V_CC = 12 V
                             |
                           [LOAD] R_L = 4 Ω
                             |
                             +---- Collector (C)
                             |
                           |/ 
                   I_B --->|   BJT Switch (Q1)
                           |\>
                             |
                            GND  Emitter (E)
```

#### Example Power Comparison ($V_{CC} = 12\text{ V}$, Load $R_L = 4\,\Omega$):

1. **Ideal Direct Connection (Ideal Mechanical Switch, $V_{sw} = 0\text{ V}$):**
   $$I_L = \frac{12\text{ V}}{4\,\Omega} = 3\text{ A}$$
   $$P_{\text{load, ideal}} = \frac{V_{CC}^2}{R_L} = \frac{12^2}{4} = 36\text{ W}$$

2. **Real Transistor Switch in Saturation ($V_{CE(\text{sat})} = 0.3\text{ V}$):**
   $$V_{\text{load}} = V_{CC} - V_{CE(\text{sat})} = 12\text{ V} - 0.3\text{ V} = 11.7\text{ V}$$
   $$P_{\text{load, real}} = \frac{V_{\text{load}}^2}{R_L} = \frac{(11.7)^2}{4} = 34.22\text{ W}$$
   $$P_{\text{loss, switch}} = V_{CE(\text{sat})} \times I_C \approx 0.3\text{ V} \times 2.925\text{ A} = 0.8775\text{ W}$$

> [!IMPORTANT]  
> To keep $V_{CE(\text{sat})}$ minimal (typically $0.2\text{ V} - 0.3\text{ V}$), the control circuit must drive a robust base current ($I_B \ge \frac{I_C}{\beta_{\text{min}}}$) ensuring the transistor remains deeply saturated under all operating conditions.

---

## 3. High-Side vs. Low-Side Switching: NPN vs. PNP Selection

The choice between NPN and PNP BJTs depends on whether the switch is located on the **Low Side** (between load and GND) or **High Side** (between $+V_{CC}$ and load).

```
        Low-Side Switch (NPN Preferred)           High-Side Switch (PNP Preferred)
        
               +V_CC (12 V)                              +V_CC (12 V)
                 |                                         |
               [LOAD]                                     [ \ ] PNP Switch (Q1)
                 |                                         |
               |/                                          +-------+
       V_in --->|   NPN Switch (Q1)                                |
               |\>                                               [LOAD]
                 |                                                 |
                GND                                               GND
```

### 3.1 Low-Side Switching Configuration
* **Structure:** Load is connected to $+V_{CC}$; switching transistor is connected between Load and Ground.
* **Preferred Transistor Type:** **NPN Transistor**

#### Why NPN is Superior for Low-Side Switching:
1. **Control Voltage Drive:**
   * To turn **ON**: Apply control voltage $V_{IN} \ge V_{BE} + V_{R1} \approx 0.7\text{ V} + V_{R1}$ (Easily driven by low-voltage logic like $3.3\text{ V}$ or $5\text{ V}$ microcontrollers).
   * To turn **OFF**: Bring $V_{IN} < 0.7\text{ V}$ (Pull down to $0\text{ V}$).
2. **Voltage Drop Across Load:**
   * Voltage available across load: $V_{\text{load}} = V_{CC} - V_{CE(\text{sat})} = 12\text{ V} - 0.3\text{ V} = 11.7\text{ V}$.

#### What Happens if PNP is Used on the Low Side?
* Emitter connected to load node; Base pulled down.
* Base-emitter junction voltage limits maximum voltage swing across load, resulting in higher voltage drop across the switch ($V_{\text{load}} \approx 11.3\text{ V}$ vs. $11.7\text{ V}$) and significantly higher power loss in the transistor.

### 3.2 High-Side Switching Configuration
* **Structure:** Switch is connected between $+V_{CC}$ and Load; Load is connected to Ground.
* **Preferred Transistor Type:** **PNP Transistor**

#### Why PNP is Superior for High-Side Switching:
1. **Control Logic:**
   * Emitter is tied directly to $+V_{CC}$ ($12\text{ V}$).
   * To turn **OFF**: Base must be pulled up to $+V_{CC}$ ($12\text{ V}$), making $V_{EB} = 0\text{ V}$.
   * To turn **ON**: Base voltage is pulled down toward Ground ($0\text{ V}$), establishing forward bias $V_{EB} \approx 0.7\text{ V}$.
2. **NPN High-Side Problem:**
   * If an NPN is used high-side (Emitter connected to Load), the Emitter voltage rises as the load turns ON.
   * To keep $V_{BE} \ge 0.7\text{ V}$ into saturation, control voltage at the Base would need to exceed supply voltage ($V_{IN} > V_{CC} + 0.7\text{ V}$), requiring a complex bootstrap or charge pump circuit.

### Summary Matrix: Transistor Switch Selection

| Configuration | Switch Location | Preferred BJT | Control Voltage Turn-ON | Control Voltage Turn-OFF |
| :--- | :--- | :--- | :--- | :--- |
| **Low-Side Switch** | Between Load and GND | **NPN** | High ($> 0.7\text{ V}$) | Low ($< 0.7\text{ V}$, $0\text{ V}$) |
| **High-Side Switch** | Between $+V_{CC}$ and Load | **PNP** | Low (Pulled toward GND) | High (Tied to $+V_{CC}$) |

---

## 4. Digital Interfacing & DC Motor Control

In mechatronics, robotics, and embedded systems, digital controllers (TTL logic, microcontrollers, microprocessors) interface with electromechanical actuators like DC motors via power transistor switches.

```
+--------------------+      +--------------------+      +--------------------+
| Microcontroller /  | ---> | Transistor Driver  | ---> | Electromechanical  |
| Logic Gate (TTL/CMOS)|    | (NPN / Darlington) |      | Actuator (DC Motor)|
+--------------------+      +--------------------+      +--------------------+
```

### 4.1 Fundamentals of DC Motor Control
1. **Direction Control (Clockwise / Counter-Clockwise):**
   * Determined by polarity of the applied DC voltage across motor terminals.
   * Requires an **H-Bridge** transistor circuit for dual-direction control.
2. **Torque Control:**
   * **Torque is directly proportional to Current ($I$)**, NOT voltage.
   * Magnetic field generated by motor windings scales linearly with current: $\tau = K_T \cdot I$.
3. **Speed Control (RPM):**
   * Speed scales with average applied voltage, typically controlled using **Pulse-Width Modulation (PWM)** from a microcontroller.

---

## 5. Darlington Transistor Pair ("Super Transistor")

When a microcontroller output pin (capable of supplying only a few milliamperes, e.g., $10 - 20\text{ mA}$) must switch heavy inductive loads requiring several amperes (e.g., $3 - 5\text{ A}$), a single BJT with a typical $\beta$ of $20 - 50$ is insufficient.

The **Darlington Configuration** combines two transistors on a single chip/package to act as a single "super transistor" with ultra-high current gain.

```
                      NPN Darlington Transistor Pair
                      
                                C (Collector)
                                o
                                |
                        +-------+-------+
                        |               |
                      |/                |
            B o-------|  Q1             |
            (Base)    |\>               |
                        |             |/ 
                        +-------------|  Q2
                                      |\>
                                        |
                                        o E (Emitter)
```

### 5.1 Mathematical Derivation of Total Current Gain ($\beta_D$)

Let $Q_1$ be the input transistor and $Q_2$ be the output power transistor.

1. **Current Equations for $Q_1$:**
   $$I_{C1} = \beta_1 I_{B1}$$
   $$I_{E1} = (\beta_1 + 1) I_{B1}$$

2. **Coupling Condition:**
   The base of $Q_2$ is fed directly by the emitter of $Q_1$:
   $$I_{B2} = I_{E1} = (\beta_1 + 1) I_{B1}$$

3. **Current Equations for $Q_2$:**
   $$I_{C2} = \beta_2 I_{B2} = \beta_2 (\beta_1 + 1) I_{B1}$$
   $$I_{E2} = (\beta_2 + 1) I_{B2} = (\beta_2 + 1)(\beta_1 + 1) I_{B1}$$

4. **Total Darlington Output Currents:**
   * Total Collector Current ($I_{C,\text{total}}$):
     $$I_{C,\text{total}} = I_{C1} + I_{C2} = \beta_1 I_{B1} + \beta_2 (\beta_1 + 1) I_{B1} = [\beta_1 + \beta_2 + \beta_1 \beta_2] I_{B1}$$
   * Total Emitter Current ($I_{E,\text{total}}$):
     $$I_{E,\text{total}} = I_{E2} = (\beta_1 + 1)(\beta_2 + 1) I_{B1} = (1 + \beta_1 + \beta_2 + \beta_1 \beta_2) I_{B1}$$

5. **Effective Darlington Gain ($\beta_D$):**
   $$\beta_D = \frac{I_{C,\text{total}}}{I_{B1}} = \beta_1 \beta_2 + \beta_1 + \beta_2 \approx \beta_1 \beta_2$$

> [!TIP]  
> **Numerical Example:** If $Q_1$ has $\beta_1 = 100$ and $Q_2$ has $\beta_2 = 25$:
> $$\beta_D \approx 100 \times 25 = 2,500$$
> Commercial Darlington packages (e.g., **TIP120 / TIP125 / TIP130 / TIP135**) regularly achieve overall current gains of **$1,000$ to $2,500+$**, allowing a tiny $1\text{ mA}$ logic input to switch over $2.5\text{ A}$ load current.

---

## 6. Trade-offs & Limitations of Darlington Pairs

While Darlington transistors offer extreme current gain, engineering design requires managing three major performance sacrifices:

```
                            Darlington Trade-Offs
+-----------------------------------+-----------------------------------+
| ADVANTAGE                         | DISADVANTAGE                      |
+-----------------------------------+-----------------------------------+
| • Extremely High Gain (β > 1000)  | • Higher Base Turn-ON V_BE (1.4V) |
| • Low Driving Current Required    | • High Saturation V_CE(sat) (1V+) |
| • Compact Single Package          | • Slow Turn-OFF Switching Speed   |
+-----------------------------------+-----------------------------------+
```

### 6.1 Base Turn-ON Voltage ($V_{BE}$)
* Because two base-emitter junctions are in series ($Q_1$ $V_{BE1}$ + $Q_2$ $V_{BE2}$):
  $$V_{BE,D} = V_{BE1} + V_{BE2} \approx 0.7\text{ V} + 0.7\text{ V} = 1.4\text{ V}$$
* Requires at least $1.4\text{ V}$ at the input base to turn ON (compared to $0.7\text{ V}$ for a standard single BJT).

### 6.2 Saturation Voltage ($V_{CE(\text{sat})}$)
* In a Darlington pair, $Q_1$'s collector is tied to $Q_2$'s collector.
* For $Q_1$ to be in saturation, its collector voltage must satisfy $V_{C1} = V_{CE2}$.
* Voltage at base of $Q_2$ is $V_{BE2} \approx 0.7\text{ V}$. Since $V_{CE1(\text{sat})} \approx 0.3\text{ V}$:
  $$V_{CE,D(\text{sat})} = V_{BE2} + V_{CE1(\text{sat})} \approx 0.7\text{ V} + 0.3\text{ V} = 1.0\text{ V} \text{ to } 1.4\text{ V}$$

> [!WARNING]  
> A high saturation voltage of $1.0\text{ V} - 1.4\text{ V}$ generates significant power dissipation ($P = V_{CE(\text{sat})} \times I_C$) when switching heavy currents, causing the device to run much hotter than a single deeply saturated BJT or power MOSFET.

### 6.3 Slow Turn-OFF Speed & Charge Trap Mechanism
1. **Turn-ON Process:** Base region of $Q_2$ is flooded with minority charge carriers (electrons/holes).
2. **Turn-OFF Problem:** When input drive $I_{B1}$ is abruptly removed ($0\text{ V}$):
   * $Q_1$ turns off quickly.
   * However, stored minority carriers trapped in the base region of $Q_2$ **have no direct exit path to ground** because $Q_1$'s emitter conducts current in only one direction.
   * Trapped charge can only dissipate through slow internal recombination or leakage, resulting in severe turn-off delay ($t_{\text{off}}$).

---

## 7. Turn-OFF Speed Enhancement: Integrated Bleeder Resistors

To overcome slow turn-off times in commercial Darlington devices (e.g., TIP120 series), internal **discharge/bleeder resistors** ($R_1, R_2$) and a **damper diode** are integrated directly onto the silicon die.

```
            Internal Integrated Darlington Circuit (e.g., TIP120)
            
                                C (Collector)
                                o
                                |
                        +-------+-------+----------+
                        |               |          |
                      |/                |        ---
            B o-------|  Q1             |        / \  Damper
            (Base)    |\>               |       ---   Diode
                        |               |          |
                        +---+---------|/           |
                        |   |         |\>          |
                       [R1] |           |          |
                        |   +----+------+----------+
                        |        |      |
                        +-------[R2]    |
                                 |      |
                                 +------+
                                        |
                                        o E (Emitter)
```

### 7.1 How Integrated Resistors Work
* **Discharge Path:** Resistors $R_1$ (across $Q_1$ base-emitter) and $R_2$ (across $Q_2$ base-emitter) provide low-impedance passive bypass paths for trapped base charges to escape to Ground during turn-OFF.
* **Effect on Gain:** Slightly reduces maximum overall current gain $\beta_D$ because a small portion of input drive current bleeds through $R_1$ and $R_2$ instead of driving base junctions.
* **Net Benefit:** Dramatically reduces turn-off delay ($t_{\text{off}}$), enabling higher frequency PWM switching.

---

## 8. Summary & Next Steps

* **Low-Side Switching:** Best built using **NPN** BJTs (or N-Channel MOSFETs) because the base drive is easily referenced to ground.
* **High-Side Switching:** Best built using **PNP** BJTs (or P-Channel MOSFETs) because base pull-down turns the switch ON.
* **Darlington Pair:** Ideal for high-current gain requirements ($\beta \ge 1000$), but trade-offs include higher $V_{BE} \approx 1.4\text{ V}$, higher $V_{CE(\text{sat})} \approx 1.0\text{ V}$, and turn-off delays (mitigated by integrated bleeder resistors).
* **Next Lecture Topic:** Alternative compound transistor configurations (e.g., Sziklai / Complementary Darlington pairs).
