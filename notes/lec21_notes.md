# Lecture 21: Discrete Darlington Design, Sziklai Pair (CFP), Phase Splitter, and Differential Amplifiers

**Course:** ETRN2 (Electronics 2)  
**Topic:** Discrete Darlington Configuration & Transistor Selection, Sziklai Pair / Complementary Feedback Pair (CFP) Analysis, Phase Splitter Circuit, BJT Differential Pair (Difference Amplifier), Common-Mode Noise Rejection (XLR, USB 2.0, SATA, HDMI)  
**Source File:** [lec21.txt](file:///Users/francis/school/y2/t3/etrn2/lecs/transcriptions/without_timestamps/lec21.txt)  
**Video Recording:** [lec21.mp4](file:///Users/francis/school/y2/t3/etrn2/lecs/lec21.mp4)  

---

## 1. Course Administrative Notes & Classroom Dynamics

### 1.1 Class Logistics & Stream Interaction
* **Online Session Dynamics:** Professor highlighted the challenge of online lectures regarding real-time student engagement and audio/video latency.
* **Student Interaction:** Encouraged active participation, asking students to unmute for questions and use Zoom reaction icons (thumbs-up) to verify comprehension and stream stability.

---

## 2. Discrete Darlington Configuration & Transistor Pairing

Building upon previous discussions of Darlington transistors (e.g., pre-packaged modules like `TIP120` or `2N6055`), discrete Darlington pairs can be custom-designed using individual BJT components to meet specific power, current, or gain requirements.

```
                   Discrete Darlington Switch Model
                   
                       +V_CC
                         |
                       [LOAD]
                         |
                 +-------+--------- Collector (C)
                 |                 |
               |/ Q1 (Driver)    |/ Q2 (Slave Power BJT)
       I_B --->|                 |
               |\>---------------+
                 |               |
                [R_bleed]        |
                 |               |\> Emitter (E)
                 +---------------+---- GND
```

### 2.1 Driver (Q1) vs. Slave (Q2) Transistor Selection Criteria
* **Master/Driver Transistor (Q1):** 
  * Provides base current to Q2 and drives the internal/external bleed resistor.
  * Handles small current levels; usually a small-signal BJT with high current gain ($\beta_1$).
* **Slave/Power Transistor (Q2):** 
  * Carries the bulk of the load current ($I_C$).
  * High-power handling capability, though typically exhibits lower intrinsic current gain ($\beta_2$).
* **Voltage Rating Rule ($V_{CEO}$):**
  * Both $Q1$ and $Q2$ must sustain the full supply voltage $V_{CC}$ when OFF.
  * $V_{CEO}$ rating should be conservatively selected with a **25%–30% safety factor** above $V_{CC}$.
* **Power Dissipation:**
  * Most thermal dissipation occurs in $Q2$ due to saturation voltage drops ($V_{CE(\text{sat})} \approx 1\text{ V} - 2\text{ V}$).

### 2.2 Discrete BJT Pair Examples & Characteristics
1. **Low-to-Medium Power Pair:**
   * **$Q1$:** `2N2222` ($V_{CEO} = 50\text{ V}$, $\beta \approx 200-300$, $I_{C(\max)} = 800\text{ mA}$, TO-18 package).
   * **$Q2$:** `TIP31B` ($V_{CEO} = 60\text{ V}$, $\beta \approx 25-50$, $I_{C(\max)} = 3\text{ A}$, TO-220 package).
   * **Combined Performance:** Achieves a total effective gain $\beta_{\text{total}} > 1,000$ even with a bleed resistor installed.
2. **High-Power Pair:**
   * **$Q1$:** `MJE15030` or `TIP31B`.
   * **$Q2$:** `2N3771` ($V_{CEO} = 40\text{ V}$, $\beta \approx 50-60$, $I_{C(\max)} = 30\text{ A}$, $P_D = 200\text{ W}$).
   * **Combined Performance:** Yields an overall beta factor of approximately $800 - 1,600$ for heavy power supply applications.
3. **High-Voltage Pair:**
   * **$Q1$:** `2N5551` ($V_{CEO} \approx 150\text{ V}$).
   * **$Q2$:** `MJE15030` ($V_{CEO} \approx 150\text{ V}$).

### 2.3 Darlington Summary
* **Pros:** Extremely high overall current gain ($\beta \approx \beta_1 \cdot \beta_2$), available as integrated single packages.
* **Cons:** High saturation voltage ($V_{CE(\text{sat})} \ge 1\text{ V}$), high turn-on base requirement ($V_{BE} \approx 1.4\text{ V}$), slower switching speeds due to trapped base charge in $Q2$.

---

## 3. Sziklai Pair / Complementary Feedback Pair (CFP)

The **Sziklai pair**, also known as the **Complementary Feedback Pair (CFP)**, is a compound two-transistor configuration invented by Hungarian-American engineer **George Sziklai** (1956 patent for push-pull audio amplifiers).

```
                      NPN-Equivalent Sziklai Pair (CFP)
                      
                         +V_CC
                           |
                     +-----+---------------- Emitter (E2)
                     |     |
                   |/ Q1   |
           I_B --->| NPN   |\ Q2 (PNP Power Transistor)
                   |\>       |
                     |       |
                     +-------+-------------- Base (B2)
                     |       |
                    [R_bleed]|
                     |       |
                     +-------+-------------- Collector (C) (Emulated NPN Collector)
                             |
                            GND
```

### 3.1 Historical Context & Motivation
* In the 1950s and 1960s, high-power NPN transistors were difficult to manufacture, rare, and expensive, whereas PNP power transistors were widely available.
* The Sziklai configuration combines a small NPN driver ($Q1$) with a large PNP power transistor ($Q2$) to **emulate a high-power NPN transistor**, matching the driver transistor's overall polarity.

### 3.2 Key Characteristics & Comparison with Darlington

| Property | Darlington Pair | Sziklai / Complementary Feedback Pair (CFP) |
| :--- | :--- | :--- |
| **Transistor Polarities** | Same (NPN+NPN or PNP+PNP) | Complementary (NPN+PNP or PNP+NPN) |
| **Overall Polarity** | Matches both transistors | Matches the First Driver Transistor ($Q1$) |
| **Turn-On Voltage ($V_{BE}$)** | $V_{BE1} + V_{BE2} \approx 1.4\text{ V}$ | Single junction $V_{BE1} \approx 0.7\text{ V}$ |
| **Saturation Voltage ($V_{sat}$)** | $V_{BE2} + V_{CE(\text{sat})1} \approx 1\text{ V} - 2\text{ V}$ | $V_{BE2} + V_{CE(\text{sat})1} \approx 0.7\text{ V} - 1\text{ V}$ |
| **Current Gain ($\beta_{\text{total}}$)** | $\beta_1 \cdot \beta_2 + \beta_1 + \beta_2$ | $\beta_1 \cdot \beta_2 + \beta_1$ |
| **Linearity & Distortion** | Moderate | Superior linearity due to local negative feedback loop |
| **Thermal Stability** | VBE shifts with $Q2$ temperature | Stable $V_{BE}$ anchored primarily to driver $Q1$ |

### 3.3 Linearity in Audio Push-Pull Power Output Stages
* In complementary push-pull audio power amplifiers driving loudspeakers, the transfer function ($V_{out} / V_{in}$) of a CFP output stage exhibits significantly higher linearity and lower distortion compared to a standard Darlington output stage.
* The inherent local feedback loop in the CFP auto-corrects non-linearities, though CFP designs require careful compensation to prevent high-frequency parasitic oscillation.

---

## 4. BJT Phase Splitter Circuit

A **Phase Splitter** is a single-transistor Common Emitter configuration designed with equal collector and emitter resistors ($R_C = R_E$).

```
                         Phase Splitter Circuit
                         
                             +V_CC
                               |
                              [R_C]
                               |
                               +------ Output 1 (Inverted: -1 × V_in)
                               |
                             |/
            V_in --->---|---|   BJT (Q1)
                        |    |\>
                       [R_B]   |
                        |      +------ Output 2 (Non-Inverted: +1 × V_in)
                       GND     |
                              [R_E] (R_E = R_C)
                               |
                              GND
```

### 4.1 Operating Principles
* **Voltage Gain ($A_v$):** Because $R_C = R_E$, the magnitude of voltage gain at both output terminals is unity ($|A_v| \approx 1.0$). It functions as a buffer/splitter rather than an amplifier.
* **Dual Output Phase Relationship:**
  * **Collector Output (Output 1):** Operates as an inverting Common Emitter amplifier ($V_{out1} = -1 \times V_{in}$).
  * **Emitter Output (Output 2):** Operates as a non-inverting Emitter Follower ($V_{out2} = +1 \times V_{in}$).
  * The two output signals are exact **180° out-of-phase mirror images** of each other.

---

## 5. BJT Differential Pair (Difference Amplifier)

By connecting two phase splitter stages so that they share a common emitter resistor ($R_E$), we form a **Differential Amplifier** (Differential Pair).

```
                      BJT Differential Pair Concept
                      
                   +V_CC                      +V_CC
                     |                          |
                   [R_C1]                     [R_C2]
                     |                          |
                     +--- V_out1                +--- V_out2
                     |                          |
                   |/ Q1                      |/ Q2
     V_in1 --->---|                          |---<--- V_in2
                   |\>                        |\>
                     |                          |
                     +------------+-------------+
                                  |
                                [R_E] (Common Emitter Resistor)
                                  |
                                 GND
```

### 5.1 Analysis & Conceptual Perspectives
1. **Common-Base Viewpoint & Relative Motion Analogy:**
   * If a signal is applied to $V_{in2}$ while $V_{in1}$ is held at AC ground ($0\text{ V}$), $Q1$ sees the signal coming in through its emitter while its base is grounded, operating in **Common-Base mode**.
   * *Analogy:* Standing on solid ground vs. standing inside a moving elevator (e.g., Disneyland Haunted Mansion ride). Moving the floor upward is perceived visually as the surroundings moving downward—emulating an inverted signal input.
2. **Mathematical Difference Operation:**
   * Single-ended output voltage at collector 1: 
     $$V_{out1} \propto (V_{in2} - V_{in1})$$
   * The circuit directly computes the **difference** between the two input signals.

### 5.2 Common-Mode Rejection & Noise Cancellation Applications

```
                      Differential / Balanced Signaling
                      
       [Microphone / Source]                            [Differential Amplifier]
        +---------- (Hot Signal: +V_sig) -------+ Wire 1 ---> Inverting Input (-)
        |                                       |             (Adds equal noise V_noise)
        +---------- (Cold Signal: -V_sig) ------+ Wire 2 ---> Non-Inverting Input (+)
        |                                       |             (Adds equal noise V_noise)
       GND ------------ (Shield Ground) --------+ Shield ---- Ground
       
    Differential Signal:  V_diff = (+V_sig + V_noise) - (-V_sig + V_noise) = 2 × V_sig
    Common-Mode Noise:    V_noise - V_noise = 0  (CANCELLED!)
```

### 5.3 Practical Engineering Applications
* **Professional Audio (XLR Balanced Lines):** Low-level microphone signals traveling over 100+ meter auditorium cables pick up environmental electromagnetic interference (EMI). Sending complementary "hot" and "cold" signals over twisted pair shielded cables allows the differential amplifier at the mixing console to completely subtract out common-mode noise while doubling the audio signal amplitude ($2 \times V_{sig}$).
* **High-Speed Digital Communications:**
  * **USB 2.0:** Uses differential data lines ($D+$ green wire, $D-$ white wire).
  * **SATA & HDMI:** High-speed serialized differential signaling (TMDS/LVDS) prevents data corruption caused by external EMI.
