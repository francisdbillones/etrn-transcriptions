# Lecture 22: Transistor-Transistor Logic (TTL) Gates, Totem-Pole Outputs, and 2-Input NAND Gate Analysis

**Course:** ETRN2 (Electronics 2)  
**Topic:** BJT Digital Logic Gate Design, Standard TTL (74xx / 74LS Series), Noise Immunity & Voltage Tolerances, Current Sourcing vs. Sinking (Fan-Out), 4-Transistor Inverter Analysis, Totem-Pole Output Stage, 2-Input TTL NAND Gate Analysis & Truth Table  
**Source File:** [lec22.txt](file:///Users/francis/school/y2/t3/etrn2/lecs/transcriptions/without_timestamps/lec22.txt)  
**Video Recording:** [lec22.mp4](file:///Users/francis/school/y2/t3/etrn2/lecs/lec22.mp4)  

---

## 1. Course Administrative Notes & Overview

### 1.1 Course Schedule & Term Conclusion
* **Final Lecture:** This lecture marks the final topic of the CETRON2 course for the trimester.
* **Upcoming Laboratory Experiment:** Final lab exercise involves simulating TTL digital logic circuits in **LTSpice** to observe voltage transfer characteristics, noise margins, and sourcing/sinking current limits.

---

## 2. Fundamentals of Transistor-Transistor Logic (TTL)

Digital logic gates in standard integrated circuits (such as the `74xx` and `74LSxx` series, e.g., `74LS00`, `74LS04`) are built around **Transistor-Transistor Logic (TTL)** using Bipolar Junction Transistors (BJTs).

```
                      Single-Transistor Inverter (Inadequate)
                      
                               +5V (V_CC)
                                 |
                                [R_C = 1 kΩ]
                                 |
                                 +------ Output (V_out)
                                 |
                               |/
              V_in --->-[R_B]-|   BJT (Q1)
                               |\>
                                 |
                                GND
```

### 2.1 Limitations of a Single-Transistor Inverter
While a basic Common Emitter BJT inverter can invert an analog or digital signal, a single-transistor logic gate fails real-world digital circuit standards due to:
1. **Poor Noise Immunity:** Strict, narrow input voltage thresholds make it vulnerable to false state triggering.
2. **Inadequate Output Driving Capability (Fan-Out):** Limited capability to drive multiple downstream logic gate inputs.
3. **Undefined Intermediate Voltages:** High risk of producing floating/invalid logic levels when inputs drift.
4. **Poor Power-to-Speed Ratio:** Slow turn-off transition times caused by stored base charge without active pull-up.

---

## 3. Real-World Digital Logic Design Requirements

### 3.1 Electromagnetic Interference (EMI) & Noise Immunity
* Fast digital transitions generate rapid current changes ($\frac{di}{dt}$), radiating radio-frequency electromagnetic fields that induce noise into adjacent circuit traces.
* TTL gates are designed with wide **noise margins** (voltage tolerance windows) so that induced noise spikes do not cause erroneous logic switching.

### 3.2 Standard TTL Voltage Thresholds ($V_{CC} = 5.0\text{ V}$)

```
   Input Voltage Levels                      Output Voltage Levels
   
   5.0V +------------------+                 5.0V +------------------+
        |                  |                      |                  |
        |  LOGIC 1 (HIGH)  |                      |  LOGIC 1 (HIGH)  |
   2.0V +------------------+                 2.4V +------------------+
        | UNDEFINED REGION |                      |                  |
   0.7V +------------------+                 0.3V +------------------+
        |  LOGIC 0 (LOW)   |                      |  LOGIC 0 (LOW)   |
   0.0V +------------------+                 0.0V +------------------+
```

* **Input Thresholds:**
  * **$V_{IL(\max)} = 0.7\text{ V}$:** Maximum input voltage guaranteed to be recognized as Logic 0.
  * **$V_{IH(\min)} = 2.0\text{ V}$:** Minimum input voltage guaranteed to be recognized as Logic 1.
  * **Undefined Region ($0.7\text{ V} - 2.0\text{ V}$):** Unstable range where gate response is unpredictable.
* **Output Standards:**
  * **$V_{OL(\max)} \approx 0.3\text{ V} - 0.4\text{ V}$:** Maximum output voltage when driving Logic 0 (leaves a $0.3\text{ V} - 0.4\text{ V}$ noise margin below $V_{IL}$).
  * **$V_{OH(\min)} = 2.4\text{ V}$:** Minimum output voltage when driving Logic 1 (leaves a $0.4\text{ V}$ noise margin above $V_{IH}$).

### 3.3 Fan-Out & Current Sinking vs. Sourcing

```
       Current Sourcing (Logic 1 Output)            Current Sinking (Logic 0 Output)
       
              +V_CC = 5V                                   +V_CC = 5V
                |                                            |
              |/ Q4 (Weak NPN Pull-Up)                     [Load / Next Gate]
       V_out -|<                                             |
                | ---> I_source = 0.4 mA             V_out --+
              [LOAD]                                         |
                |                                          |/ Q3 (Strong NPN Saturation)
               GND                                  I_sink -|< (16 mA)
                                                             |
                                                            GND
```

* **Current Sourcing ($I_{OH} = 0.4\text{ mA}$):**
  * When output is HIGH, $Q4$ operates as an Emitter Follower (Common Collector). Sourcing capacity is relatively weak ($0.4\text{ mA}$) because TTL inputs demand negligible current in the HIGH state.
* **Current Sinking ($I_{OL} = 16\text{ mA}$):**
  * When output is LOW, $Q3$ operates in deep saturation as a Common Emitter switch, sinking up to $16\text{ mA}$ to GND.
* **Engineering Recommendation for LED Driving:**
  * Always connect LEDs in **sinking mode** (Anode to $+5\text{ V}$ via resistor, Cathode to TTL output pin). Sourcing mode will fail to illuminate the LED brightly and strains the top pull-up transistor.

---

## 4. Internal Schematic Analysis of a TTL Inverter

```
                           Standard TTL Inverter Schematic
                           
                                     +5V (V_CC)
                                       |
                   +----------+--------+---------+
                   |          |                  |
                  [R1]       [R2]               [R4 = 130 Ω]
                   |          |                  |
                   |          +-----+          |/ Q4 (Pull-Up)
                   |          |     |          |
                   |        |/ Q2   +---+[D1]--+
        V_in --->--+--| Q1  | (Phase Splitter) |
                   |  |\>   |\>                +------ V_out
                   |    |     |                |
                  ---  ---    +------+       |/ Q3 (Pull-Down)
                              |      |       |
                             [R3]    +-------|<
                              |              |
                             GND            GND
```

### 4.1 Stage-by-Stage Functional Breakdown

1. **Input Current-Steering Transistor ($Q1$):**
   * Multi-emitter or dual-diode equivalent junction.
   * When $V_{in} = \text{LOW } (0\text{ V})$, $Q1$'s base-emitter junction is forward-biased, steering base current out through $V_{in}$ to ground. This keeps $Q1$'s collector voltage low, holding $Q2$ OFF.
   * When $V_{in} = \text{HIGH } (5\text{ V})$, $Q1$'s base-emitter junction is reverse-biased; current flows through $Q1$'s base-collector junction directly into $Q2$'s base.
2. **Phase Splitter Stage ($Q2$):**
   * Common Emitter phase splitter producing complementary collector ($V_{C2}$) and emitter ($V_{E2}$) signals.
   * Drives the output stage out-of-phase: when $Q2$ is ON, $V_{C2}$ is LOW and $V_{E2}$ is HIGH.
3. **Totem-Pole Output Stage ($Q3$, $Q4$, $D1$, $R4$):**
   * **Active Pull-Down ($Q3$):** Saturates to pull $V_{out}$ down to $\sim 0.3\text{ V}$ (Logic 0) when $Q2$ is ON.
   * **Active Pull-Up ($Q4$):** Turns ON when $Q2$ is OFF to pull $V_{out}$ up to $\ge 2.4\text{ V}$ (Logic 1).
   * **Level-Shifting Diode ($D1$):** Prevents both $Q3$ and $Q4$ from turning ON simultaneously (preventing heavy shoot-through current from $V_{CC}$ to GND).
   * **Current-Limiting Resistor ($R4$):** Limits transient surge current during output switching.

---

## 5. Circuit Analysis of a 2-Input TTL NAND Gate

```
                         2-Input TTL NAND Gate Circuit
                         
                                      +5V (V_CC)
                                        |
                    +----------+--------+---------+
                    |          |                  |
                   [R1]       [R2]               [R4]
                    |          |                  |
                    |          +-----+          |/ Q4
     Input X --->---| B  Q1    |     |          |
                    | (Multi-  |/ Q2 +---[D1]---+
     Input Y --->---| Emitter) |\>              |
                    |          |                +------ Output Z
                   ---        [R3]              |
                               |              |/ Q3
                              GND             |
                                              |\>
                                                |
                                               GND
```

### 5.1 Step-by-Step Truth Table Derivation

| Input X | Input Y | $Q1$ State | $Q2$ State | $Q3$ (Bottom) | $Q4$ (Top) | Output Z | Logic State |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **0 (0V)** | **0 (0V)** | Forward Biased to Inputs | **OFF** | **OFF** | **ON** | **HIGH ($\approx 3.4\text{ V}$)** | **1** |
| **1 (5V)** | **0 (0V)** | Forward Biased to Y | **OFF** | **OFF** | **ON** | **HIGH ($\approx 3.4\text{ V}$)** | **1** |
| **0 (0V)** | **1 (5V)** | Forward Biased to X | **OFF** | **OFF** | **ON** | **HIGH ($\approx 3.4\text{ V}$)** | **1** |
| **1 (5V)** | **1 (5V)** | Reverse Biased to Inputs | **ON** | **ON** | **OFF** | **LOW ($\approx 0.3\text{ V}$)** | **0** |

### 5.2 Summary of Logic Operation
* If **either input** ($X$ or $Y$) is LOW ($0\text{ V}$), current from $R1$ bleeds out through that input emitter, keeping $Q2$ and $Q3$ OFF, while $Q4$ pulls Output $Z$ **HIGH**.
* Only when **both inputs** ($X$ and $Y$) are HIGH ($5\text{ V}$) are the input emitters reverse-biased, steering current into $Q2$ and saturating $Q3$, which pulls Output $Z$ **LOW**.
* This matches the Boolean truth table of a **2-Input NAND Gate** ($Z = \overline{X \cdot Y}$).
