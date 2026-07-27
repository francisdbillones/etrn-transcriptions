# Lecture 18 Notes: JFET Voltage Divider Biasing, Transistor Testing & Packaging, and JFET Applications

---

## 1. Administrative Context & Course Progress
* **Synchronization Note:** The professor notes that two course sections are not yet fully synchronized. The previous lecture ended around the JFET self-bias circuit and physical JFET identification.
* **Lecture Coverage:** This lecture completes JFET biasing (Voltage Divider Bias), details multimeter-based transistor testing (BJTs & JFETs), analyzes transistor packaging styles, and introduces four key JFET application circuits.

---

## 2. JFET Biasing: Voltage Divider Bias (3rd Biasing Method)

### 2.1 Overview & Circuit Topology
So far, three biasing methods for JFETs have been covered:
1. **Fixed Bias:** Requires two separate DC power supplies (one positive for drain $V_{DD}$, one negative for gate $V_{GG}$).
2. **Self-Bias:** Uses a source resistor $R_S$ so that source current $I_S$ lifts the source voltage $V_S$ above ground, creating a negative gate-to-source bias ($V_{GS} = -I_D R_S$) without a negative power supply.
3. **Voltage Divider Bias:** Uses a resistive divider ($R_1, R_2$) across $V_{DD}$ to force a fixed DC gate potential $V_G$. It is no longer self-bias because $V_G$ is explicitly set by the voltage divider.

```
       +V_DD (e.g. 30V)
         |
     +---+---+
     |       |
    [R1]    [RD] (1.1k)
     |       |
     +---G --+--- V_out
     |     | |
    [R2] JFET|
     |     | |
     |      -+
     |       |
     |      [RS] (10k)
     |       |
    ===     ===
    GND     GND
```
*Schematic Note:* On standard textbook schematics, watch for missing dots at the voltage divider node junction.

### 2.2 Governing Equations
1. **Gate Voltage ($V_G$):**  
   Because the JFET gate current is zero ($I_G = 0\text{ A}$), $R_1$ and $R_2$ form an unloaded voltage divider across $V_{DD}$:
   $$V_G = V_{DD} \left( \frac{R_2}{R_1 + R_2} \right)$$
2. **Source Voltage ($V_S$) & Drain Current ($I_D$):**  
   $$V_S = I_S R_S = I_D R_S$$
   $$V_{GS} = V_G - V_S \implies V_S = V_G - V_{GS}$$
   $$I_D = \frac{V_S}{R_S} = \frac{V_G - V_{GS}}{R_S}$$

Since neither $V_{GS}$ nor $V_S$ is known initially, this single linear equation cannot be solved directly without knowing the device's non-linear transconductance characteristic.

---

### 2.3 Graphical DC Load Line Analysis

When the JFET transconductance characteristic curve ($I_D$ vs. $V_{GS}$) is available (either from manufacturer datasheets or plotted using Shockley's equation $I_D = I_{DSS} \left(1 - \frac{V_{GS}}{V_P}\right)^2$), DC load line analysis determines the exact Q-point ($I_{DQ}, V_{GSQ}$).

#### Procedure & Rationale:
1. **Step 1 (X-axis Intercept):** Plot the first point on the positive $V_{GS}$ axis at $(V_{GS} = +V_G, I_D = 0)$.
2. **Step 2 (Y-axis Intercept / Intermediate Slope Point):**  
   Calculate $I_D = \frac{V_G}{R_S}$.  
   *Professor's Explanation on Equation Paradox:* $I_D = \frac{V_G}{R_S}$ is technically physically incorrect for actual circuit operation (since $I_D = \frac{V_S}{R_S}$, not $\frac{V_G}{R_S}$). However, "have faith!"—in DC load line analysis, this value is used strictly as an intermediate step to establish the geometric slope of the load line.  
   Plot this second point on the Y-axis at $(V_{GS} = 0, I_D = \frac{V_G}{R_S})$.
3. **Step 3 (Line Extension):** Draw a line connecting $(+V_G, 0)$ and $(0, \frac{V_G}{R_S})$, and extend it straight into the second quadrant (negative $V_{GS}$ region).
4. **Step 4 (Q-Point Extraction):** The intersection point between the extended load line and the JFET transconductance curve defines the quiescent operating point ($V_{GSQ}, I_{DQ}$).

#### Practical Example Walkthrough (from Lecture):
* **Given Parameters:**  
  * $V_{DD} = 30\text{ V}$
  * $R_1 = 1.5\text{ M}\Omega$, $R_2 = 1.5\text{ M}\Omega$
  * $R_D = 1.1\text{ k}\Omega$
  * $R_S = 10\text{ k}\Omega$ *(Professor Note: 10k is a rather high/strange value for RS, but it's what the example uses)*
  * Device Specs: $I_{DSS} = 16\text{ mA}$, $V_{GS(\text{off})} = V_P = -8\text{ V}$
* **Calculations:**  
  1. $V_G = 30\text{ V} \times \frac{1.5\text{ M}\Omega}{1.5\text{ M}\Omega + 1.5\text{ M}\Omega} = 15\text{ V}$
  2. Intermediate Y-intercept: $I_{D(\text{intermediate})} = \frac{15\text{ V}}{10\text{ k}\Omega} = 1.5\text{ mA}$
  3. Plot $(+15\text{ V}, 0\text{ mA})$ and $(0\text{ V}, 1.5\text{ mA})$, extend line into negative $V_{GS}$ quadrant.
  4. Intersection with transconductance curve yields Q-point:  
     $$I_{DQ} \approx 2.0\text{ mA}, \quad V_{GSQ} \approx -5.5\text{ V}$$

*Professor Side Comment:* "If you took the time to derive the transconductance curve using Shockley's equation yourself, then you wouldn't need the DC load line analysis! But if you went through all that trouble, maybe it's not a good idea because supposedly the idea behind the DC load line is to do it quickly."

---

## 3. Transistor Part Numbers & Japanese Industrial Standards (JIS)

Japanese transistor part numbers follow the **JIS (Japanese Industrial Standard)** convention, providing instant identification of device type and channel polarity:

* **`2SK...`** $\rightarrow$ N-Channel JFET (e.g., `2SK170` or `2SK170F`)
* **`2SJ...`** $\rightarrow$ P-Channel JFET (e.g., `2SJ74`)

*Key Convention Breakdown:*
* `2` = Field-Effect Transistor / 3-terminal device
* `S` = Semiconductor
* `K` = N-channel FET
* `J` = P-channel FET
* *Professor Comment:* "The JIS standard provides conventions for the Japanese electronics industry. What the IS stands for? I'm sure Google can answer you." *(Note: JIS = Japanese Industrial Standards)*.

---

## 4. Multimeter Transistor Testing & Troubleshooting Philosophy

### 4.1 Professor's Tangent: Lab Troubleshooting & Debugging Mindset
* **Common Student Mistake:** Students build circuits blindly in the lab, spend 1 to 1.5 hours struggling when it doesn't work, give up, and ask "Sir, why isn't it working?" The issue almost always turns out to be simple and fixable in seconds.
* **The "Going Blind" Problem:** When circuits fail, students go blind—they stare at the breadboard and blindly rewire everything without thinking ("Parang tama naman ah... rewire pa rin natin. Which is kind of stupid—you waste time rewiring and wind up with the exact same problem").
* **Circuit Debugging vs. Programming:**  
  * Debugging hardware is identical to debugging code!
  * In software, once compiler syntax errors pass and logic bugs remain, developers add `printf` statements to inspect variable states (`x, y, z`).
  * In hardware, measured node voltages are your `printf` output! Analyze measured voltages against expected operating behavior to locate errors.
* **Component Verification:** Before assuming wiring is wrong, test if the transistor itself is burned or defective using a Digital Multimeter (DMM).

---

### 4.2 BJT Multimeter Testing Procedure (Diode Test Mode)

To test an unknown BJT (e.g., `2N2222` or `TIP31`), identify its polarity (NPN vs. PNP), determine pinout (Base, Collector, Emitter), and verify health:

#### Step 1: Base Identification & Polarity Test (NPN vs. PNP)
1. Set DMM to **Diode Mode** (measures forward junction voltage $V_F$).
2. Place **Red Probe (+)** on Pin 1. Touch **Black Probe (-)** to Pin 2, then Pin 3 individually (*"Hindi sabay. Wag katanga. Isa and pangalawa"*).
3. **If both readings conduct** ($\sim 0.6\text{V} - 0.7\text{V}$, e.g. `0.65V`):  
   * Pin 1 is the **Base**.
   * Transistor is **NPN** (Red probe on P-type Base).
4. **If Black Probe (-)** on Pin 1 conducts to Pin 2 and Pin 3 with Red probe:  
   * Pin 1 is the **Base**.
   * Transistor is **PNP** (Black probe on N-type Base).

#### Step 2: Emitter vs. Collector Identification ($V_{BE}$ vs. $V_{BC}$)
1. Measure forward voltage drop from Base to Pin 2 and Base to Pin 3.
2. **Rule:** The **Base-Emitter ($V_{BE}$) junction has a slightly higher forward voltage drop** than the **Base-Collector ($V_{BC}$) junction** due to higher doping concentration in the emitter.
   $$\text{Higher } V_F \implies \text{Emitter}, \quad \text{Lower } V_F \implies \text{Collector}$$
   *Example:* If Pin 1-to-2 reads `0.65V` and Pin 1-to-3 reads `0.63V`, then Pin 2 = Emitter, Pin 3 = Collector (Pinout: BEC).  
   *(Professor Caveat: "This is something I have to verify. I'm not entirely sure about that one, but that's the general rule.")*

#### Step 3: Defect Verification Checks
1. **Reverse Bias Leakage Check:** Reverse probe polarity on Base-Emitter and Base-Collector junctions. DMM **must read Open Line (`OL`)**. Any measured voltage drop indicates a leaky/damaged semiconductor junction.
2. **Collector-Emitter Short Check:** Switch DMM to **Ohms ($\Omega$) Mode**. Measure resistance between Collector and Emitter in both directions. DMM **must read infinite resistance (`OL`)**.  
   *Failure Mode:* If a transistor experiences thermal breakdown, the Base junctions might still test fine, but Collector-to-Emitter will be internally shorted (reading low or zero resistance).

#### Important Exam & Lab Tip on TIP31:
* In the lecture example, an arbitrary pinout of BEC was derived for demonstration.
* **Real-World TIP31 Pinout:** The standard pinout for a real `TIP31` (in TO-220 package) is **BCE** (Pin 1 = Base, Pin 2/Tab = Collector, Pin 3 = Emitter).  
  *Professor Remark:* "Why do I know that? Well, because it's in my head. I inverted it in the example on purpose so the answer would be different."

---

### 4.3 JFET Multimeter Testing Procedure

JFETs have only **one PN junction** (Gate-to-Channel) and are **depletion-mode** devices (normally-ON when $V_{GS} = 0\text{ V}$).

1. **Drain-Source Channel Test (Ohms Mode):**
   * Leave Gate floating/unconnected ($V_{GS} = 0\text{ V}$).
   * Measure resistance between Drain and Source.
   * **Expected Result:** Must read a low, finite resistance (e.g., `375` $\Omega$). It should **never read `OL`**, because the depletion channel is naturally wide open when $V_{GS} = 0$.
2. **Gate PN Junction Test (Diode Mode):**
   * Test Gate-to-Drain ($G\text{-}D$) and Gate-to-Source ($G\text{-}S$).
   * For N-channel: Red probe on Gate conducts to Drain and Source ($\sim 0.6\text{V} - 0.7\text{V}$); Black probe on Gate reads `OL`.
   * The Gate pin is uniquely identified as the single terminal showing diode conduction to both other terminals.

---

## 5. Transistor Packaging Styles & Physical Characteristics

| Package Style | Typical Power Dissipation | Construction & Features | Pinout & Identification Rules |
| :--- | :--- | :--- | :--- |
| **TO-18** | $\le 1\text{ W}$ | Small metal can package (used for original `2N2222` before plastic epoxy existed). | • 3 legs in triangular layout under base.<br>• **Physical metal tab points directly to Emitter**.<br>• Leg without glass insulation sleeve connects directly to metal case = **Collector**.<br>• Remaining leg = **Base**. |
| **TO-39** | $2\text{ W} - 3\text{ W}$ | Medium metal can package (larger version of TO-18). | • Same triangular pinout and metal tab rule as TO-18.<br>• Metal casing acts as Collector. |
| **TO-92** | $< 1\text{ W}$ | Low-cost molded plastic epoxy package. | • 3 legs arranged in a straight line.<br>• No tab, no metal casing, no triangular clue.<br>• **DMM testing is mandatory** to determine pinout. |
| **TO-220** | $20\text{ W} - 50\text{ W}$ (typical $\sim 35\text{ W}$) | Plastic body with top metal mounting tab for heatsink attachment. | • Metal tab is electrically bonded to **Center Pin (Collector)**.<br>• Standard layout: **BCE** (Base=Left, Collector=Center, Emitter=Right).<br>• Rare variant: **ECB** (Emitter=Left, Collector=Center, Base=Right). Collector is always middle pin. |
| **TO-3** | $80\text{ W} - 250\text{ W}$ | Heavy-duty diamond metal case with two mounting flange holes. | • Designed for high-power applications requiring heavy heatsinks.<br>• Casing is electrically connected to Collector. |

*Note on Power Rating Overlaps:* Transistor packages overlap in power ratings. Manufacturers select packages based on power dissipation limits, physical space, and cost.

---

## 6. Practical Applications of JFETs

### 6.1 Figure of Merit: Transconductance ($\mu$ or $g_m$) vs. Input Impedance
* **Figure of Merit:** JFET performance is defined by transconductance ($\mu$ or $g_m$), analogous to $\beta$ for BJTs.
* **Gain Comparison:** JFETs provide significantly lower voltage gain ($A_v \approx 20 - 50$) compared to BJTs ($A_v > 100 - 120$ in common-emitter configuration). Both produce non-linear distortion.
* **Why Use a JFET?** JFETs possess extremely high input impedance ($R_{in} \approx 1\text{ M}\Omega - 4.7\text{ M}\Omega+$) because the gate junction is reverse-biased ($I_G \approx 0\text{ A}$), whereas BJTs have low input impedance ($R_{in} \approx 1\text{ k}\Omega - 5\text{ k}\Omega$).
* **Primary Function:** Buffering and impedance matching (matching a high-impedance source to lower-impedance BJT amplification stages). Frequently used as the 1st RF/audio input stage.

---

### 6.2 Application 1: Piezoelectric Microphone Preamplifier

#### Piezoelectric Transducer Fundamentals
* Piezoelectric materials are crystalline structures that generate voltage when subjected to mechanical stress (and vice versa—true bidirectional transducer).
* **Real-World Example 1 (LPG Gas Stove Igniter):** Turning the stove knob releases gas (hissing sound), then trips a spring-loaded hammer ("Pak! / Tok!"). The hammer strikes a piezo crystal, creating a high-voltage spark near the gas outlet to ignite the LPG.
* **Real-World Example 2 (Sounders/Buzzers):** Piezo discs are glued inside Nintendo Game & Watch handhelds ("beep-beep-beep!"), digital watch alarms, and DMM continuity beepers.
* **Piezo as a Microphone:** Striking or speaking into a piezo buzzer generates an electrical signal. Though stiff and poor quality, it produces high voltage output with **extremely high output impedance ($\sim 1\text{ M}\Omega$)**.

#### Circuit Design
```
         +9V DC
           |
       +---+---+
       |       |
     [RD]     (10uF Filter)
    (1.5k)     |
       |      === GND
       +-------||-----+---> V_out
       |       C_out  |
     |/ D             [220k Pull-down]
 Piezo---G            |
     |\ S            === GND
       |
      [RS] (500 ohm)
       |
      === GND
```
* **Gate Resistor:** $R_G = 3.3\text{ M}\Omega$ sets high input impedance without loading the piezo.
* **Direct Coupling:** No input capacitor is required because the piezo transducer acts like a capacitor with zero DC offset.
* **Voltage Gain:** Theoretical $A_v \approx \frac{R_D}{R_S} = \frac{1.5\text{ k}\Omega}{500\ \Omega} = 3\times$; realistic actual gain $A_v \approx 2\times$ due to transconductance limitations.
* **Output Network:** $10\ \mu\text{F}$ power supply capacitor filters line noise; $220\text{ k}\Omega$ pull-down resistor references AC-coupled output signal around $0\text{ V}$.

---

### 6.3 Application 2: JFET Shunt Switch / Audio Muting Circuit

Shunting means shorting an AC signal path to ground to mute audio/signals (like the Mute button on a TV remote control).

```
 V_in ----[ R_D ]----+---- V_out (To next stage)
 (Signal)  (10k-47k) |
                   D |
                G ---| JFET
                   S |
                     |
                    === GND
```

* **Circuit Configuration:** Notice that the JFET drain is **not** connected to $V_{DD}$. Instead, the AC input signal $V_{in}$ passes through $R_D$ ($10\text{ k}\Omega - 47\text{ k}\Omega$) directly to the Drain terminal.
* **Mute Mode ($V_{GS} = 0\text{ V}$):**
  * Gate tied to Source ($V_{GS} = 0\text{ V}$), placing JFET in maximum conduction mode ($R_{DS(\text{on})}$ is minimal).
  * $R_D$ and $R_{DS(\text{on})}$ form a voltage divider. Since $R_{DS(\text{on})} \ll R_D$, $V_{out} \approx 0\text{ V}$ (signal is shunted to ground / muted).
* **Unmute Mode ($V_{GS} = -V_P$):**
  * Gate driven to negative pinch-off voltage ($V_{GS} \le -V_P$).
  * Channel pinches off ($R_{DS} \to \infty$, open circuit).
  * Input signal passes cleanly through $R_D$ to $V_{out}$ without attenuation.

---

### 6.4 Application 3: Analog Signal Multiplexer / Demultiplexer

By placing multiple JFET switches in parallel connected to a shared output node ($V_{out}$):

* **Analog Multiplexer (Selector):**
  * Selects between multiple input signals (e.g., Input 1 = Sine Wave, Input 2 = Square Wave, Input 3 = Triangle Wave).
  * Control logic sets $V_{GS} = 0\text{ V}$ on the selected channel (passing signal) and $V_{GS} = -V_P$ on unselected channels (blocking signal).
* **Analog Demultiplexer:**
  * Uses the inverse (mirror-image) topology to route a single input signal selectively into one of three separate output channels.

---

### 6.5 Application 4: Two-Terminal Constant Current Sink / Source

Exploits the property that saturated drain current equals $I_{DSS}$ when $V_{GS} = 0\text{ V}$.

#### Circuit Topologies
1. **Low-Side Constant Current Sink:**  
   * Gate connected directly to Source ($V_{GS} = 0\text{ V}$) and tied to Ground.
   * Load connected between $V_{DD}$ and Drain.
2. **High-Side Constant Current Source:**  
   * Drain connected to $V_{DD}$.
   * Gate connected directly to Source ($V_{GS} = 0\text{ V}$) and tied to the top of the Load; bottom of Load connected to Ground.

```
Low-Side Sink:                   High-Side Source:
     +V_DD                            +V_DD
       |                                |
     [Load]                           |/ D
       |                          G---| JFET
     |/ D                             |\ S
 G---| JFET                             |------+
     |\ S                               |      |
       |------+                       [Load]  [G-to-S short]
       |      |                         |      |
      ===    ===                       ===    ===
      GND    GND                       GND    GND
```

#### Operational Characteristics & Commercial Devices
* Delivers a fixed current $I_D = I_{DSS}$ through the load regardless of variations in supply voltage $V_{DD}$ or load resistance $R_L$, provided $V_{DD}$ is sufficient to keep the JFET in saturation.
* *Constraint Warning:* Will not regulate if supply voltage is too low or load resistance is too high (e.g., $1\text{V}$ supply driving a $100\text{ k}\Omega$ load cannot reach $I_{DSS}$).
* **Commercial 2-Terminal Devices:** Packaged 2-pin constant-current ICs labeled $I_K$ (typically $\sim 1\text{ mA} - 2\text{ mA}$, similar to Zener selection) contain an internal JFET with Gate hardwired to Source. Standard JFETs can be wired identically to create discrete equivalents.

---

## 7. Next Lecture Preview
* The lecture concludes at ~8:41 AM prior to launching into a complex multi-transistor application circuit that requires 20+ minutes of detailed breakdown.
* Topic will resume on Thursday.
