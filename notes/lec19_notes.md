# Lecture 19: JFET Applications, Precision Load Cell Interfacing & Chopper Amplifier Design

**Course:** ETRN2 (Electronics 2)  
**Topic:** Extended JFET Applications, Digital Weighing Scale Signal Conditioning, Load Cell Datasheet Analysis, Chopper-Stabilized Amplifiers, and Synchronous Demodulation  
**Source File:** [lec19.txt](file:///Users/francis/school/y2/t3/etrn2/lecs/transcriptions/without_timestamps/lec19.txt)  

---

## 1. Course Administrative Notes, Setup & Professor Tangents

### 1.1 Technical Setup & Hardware Diagnostics
* **Audio Routing Issue:** Professor experienced initial audio output configuration issues on the lecture workstation, routing sound through the `Tiger Lake audio controller plus speaker and headphones`.
* **Video Camera Disablement:** Professor kept video disabled during the session, remarking: *"As you can see I'm wearing a... I look weird with this thing but I really don’t have any choice so I’m not going to turn on my video."*
* **Zoom UI & Emoticon Bug:** 
  - Zoom bug prevented the participant list window from displaying individual attendees.
  - Only aggregate emoticon counts (thumbs up) were visible on screen.
  - Professor noted: *"I don't know what the bug is with the software. Must be an incompatibility, meaning that I need to update my Zoom."*
  - Professor asked students to raise thumbs-up icons to verify stream stability and comprehension throughout the lecture.

### 1.2 Quiz Evaluation & Grading Status
* **Status Update:** The professor was actively grading the recent class quiz during the lecture period.
* **Student Performance Observations:**
  - Results were reported to be extremely widely spread across the class spectrum.
  - Professor comments: *"Nakita ko na meron, medyo maraming meron, maraming blanco, napakasakit. Meron din naman dyan maraming sagot na hindi mo nalaman kung hindi mo malaman kung saan nanggaling yung sagot. At meron naman na, uy, nakakatuwa naman, at maraming tamang sagot."*
  - Grading process described as *"very painfully slow."*

### 1.3 Elective Course Promotion
* **Course Announcement:** *Microcontrollers and Their Applications* (Elective Course taught by the Professor).
* **Topic Teaser:** Software-based resolution enhancement techniques (oversampling and decimation) to derive 11-bit or higher resolution from a hardware-limited 10-bit ADC.

---

## 2. Review of Fundamental JFET Applications

Before introducing the main extended design challenge, the lecture briefly recapped four foundational Junction Field-Effect Transistor (JFET) applications:

1. **Impedance Matching (Buffer / Source Follower):**
   - Utilizes the near-infinite gate input impedance of JFETs ($R_{in} \to \infty$).
   - Isolates high-impedance signal sources from low-impedance load stages to eliminate signal loading degradation.
2. **Analog Switching:**
   - Controls signal flow using gate-to-source voltage ($V_{GS}$).
   - **ON State:** $V_{GS} = 0\text{ V}$ ($R_{DS} \approx R_{DS(on)}$, low resistance).
   - **OFF State:** $V_{GS} \le V_p$ (Pinch-off voltage, $R_{DS} \to \infty$, open circuit).
3. **Analog Multiplexing (MUX) & Demultiplexing (DEMUX):**
   - **Analog Multiplexer (MUX):** Uses arrayed JFET switches to select and route one of several input signal channels to a single output line.
   - **Analog Demultiplexer (DEMUX):** Inverse operation—routes a single input signal to one of multiple output lines.
4. **Constant Current Source / Current Sink:**
   - Capitalizes on the JFET's flat saturation region behavior.
   - When $V_{GS} = 0\text{ V}$ and $V_{DS} \ge |V_p|$, drain current limits automatically to $I_D \approx I_{DSS}$ regardless of drain voltage fluctuations.

> [!NOTE]  
> The professor closed this section stating: *"Enough of the kids stuff... let's move on to an extended application."*

---

## 3. Real-World Engineering Challenge: 200 kg Digital Weighing Scale

The core focus of the lecture is designing the complete hardware signal conditioning chain for a precision digital scale.

```
+-------------------+      +-----------------------+      +-------------------+      +----------------+
| Physical Weight   | ---> | Sensor (Load Cell)    | ---> | Signal            | ---> | Microcontroller| ---> LCD Display
| (0 - 200 kg)      |      | Voltage Output        |      | Conditioning      |      | (Arduino ADC)  |      (2x20 Text)
+-------------------+      +-----------------------+      +-------------------+      +----------------+
```

### 3.1 Design Specifications
* **Maximum Weight Capacity ($W_{max}$):** $200\text{ kg}$
  - Upper operating limit. The system is designed primarily for weighing humans or objects within $200\text{ kg}$.
  - System behavior above $200\text{ kg}$ is out-of-spec. Professor quote: *"If it gets destroyed, well, that's the fault of whoever is using it."*
* **Step Resolution / Increment ($\Delta W$):** $0.1\text{ kg}$ ($100\text{ grams}$)

### 3.2 Conceptual Distinction: Resolution vs. Accuracy
* Although loosely interchanged in casual discussion, **Resolution** and **Accuracy** are technically distinct:
  - **Resolution:** The smallest detectable change in input physical quantity that produces a distinct output step.
  - **Accuracy:** How close the measured/displayed reading is to the absolute true physical weight value.
* **Why $0.1\text{ kg}$ resolution matters (Christmas Anecdote):**
  - If a scale has only a $1.0\text{ kg}$ resolution, a person weighing $60.7\text{ kg}$ sees $61\text{ kg}$. If they gain weight up to $61.499\text{ kg}$, the scale display remains stuck at $61\text{ kg}$.
  - A $0.1\text{ kg}$ resolution ensures that stepping on the scale after over-indulging during Christmas dinner will register even a $100\text{ gram}$ weight gain ($60.1 \to 60.2 \to 60.3\text{ kg}$).

### 3.3 Required Quantization Steps & ADC Bit Size
1. **Total Step Calculation:**
   $$N_{steps} = \frac{W_{max}}{\Delta W} = \frac{200.0\text{ kg}}{0.1\text{ kg}} = 2,000\text{ discrete steps}$$
   *(Representing readings from $0.0\text{ kg}, 0.1\text{ kg}, 0.2\text{ kg}, \dots$ up to $199.9\text{ kg} / 200.0\text{ kg}$).*

2. **Required ADC Bit Resolution ($n$):**
   $$2^n \ge N_{steps} \implies 2^n \ge 2,000$$
   - For $n = 10\text{ bits}$: $2^{10} = 1,024\text{ steps}$ (Insufficient for 2,000 steps).
   - For $n = 11\text{ bits}$: $2^{11} = 2,048\text{ steps}$ ($2,048 > 2,000$, sufficient capacity).

3. **Hardware Limitation of ATmega328 (Arduino Uno):**
   - The ATmega328 microcontroller contains an internal **10-bit ADC** ($1,024$ levels).
   - Using the standard 10-bit ADC directly limits resolution to $\frac{200\text{ kg}}{1,000\text{ steps}} = 0.2\text{ kg}$ per step.
   - **Human Cognitive Awkwardness:** A $0.2\text{ kg}$ step causes the display to skip odd tenths ($60.0 \to 60.2 \to 60.4 \to 60.6\text{ kg}$). Humans naturally question why odd values like $60.3\text{ kg}$ are missing (*"Bakit walang in between?"*).

4. **Software Resolution Enhancement Mention:**
   - Advanced embedded software techniques (oversampling and decimation) can extract 11-bit effective resolution from a 10-bit hardware ADC. (Detailed in Professor's elective course).

5. **Display Output Interface:**
   - Standard $2 \times 20$ character text LCD display module connected to Arduino.

---

## 4. Comprehensive Sensor Technology Evaluation

The class evaluated several candidate physical sensing methods to convert a $200\text{ kg}$ weight into an analog voltage:

| Sensor Technology Candidate | Physical Operating Principle | Engineering Assessment & Failure Rationale |
| :--- | :--- | :--- |
| **Potentiometer + Mechanical Pivot & Springs** | Platform rests on springs. Weight depresses springs; mechanical pivot translates vertical motion into angular rotation of a laboratory potentiometer voltage divider. | **Unviable:** Severe mechanical **backlash**, friction, hysteresis, mechanical wear, and inability to resolve sub-millimeter spring deflections reliably. |
| **Direct Piezoelectric Transducer** | Piezoelectric crystal generates voltage under applied mechanical stress. | **Unviable for Static Loads:** Piezoelectric material outputs an **AC voltage transient**. Squeezing a piezo continuously (e.g., with pliers) produces a momentary voltage spike that rapidly decays to zero. Under static weight, output is $0\text{ V}$. |
| **Mass-Spring Damped Resonant Frequency System** | Weight ($m$) on spring ($k$) creates a mass-spring system with natural frequency $f = \frac{1}{2\pi}\sqrt{\frac{k}{m}}$. Stepping on scale induces oscillation measured via piezo timer. | **Theoretical Possibility:** Microcontrollers excel at timing periods between zero-crossings. Heavy mass $M \implies$ lower frequency; small mass $m \implies$ higher frequency. **Drawback:** Requires precise zero-crossing period measurement within a narrow window before oscillations damp out. Damping must be carefully tuned so the platform doesn't oscillate excessively and make users dizzy. |
| **Time-of-Flight (ToF) Distance Sensor (Ultrasonic / Laser)** | Ultrasonic transducer emits audio chirp; measures round-trip time ($t/2$) off compressed platform to calculate displacement $\Delta d$. | **Unviable:** A $200\text{ kg}$ load compressing springs by $1\text{ to }2\text{ cm}$ requires resolving displacement changes of $< 10\ \mu\text{m}$ per $0.1\text{ kg}$ step, exceeding standard ToF sensor resolution. |
| **Strain Gauge Load Cell** *(Selected)* | Block of aluminum flexes elastically under weight. Bonded strain gauge resistors change resistance proportionally to surface strain. | **Optimal Solution:** Solid-state, highly linear, reliable, durable, minimal physical deflection, high weight capacity. |

### 4.1 Sensor Real-World Trivia & Scale Examples
* **Home Kitchen Scale Trivia:** Professor owns a home scale rated for $5\text{ kg}$ max capacity with $1\text{ g}$ claimed resolution (sub-gram display). Budget consumer scales do **not** use load cells because precision load cells are too expensive for low-cost mass production.
* **Freight Truck Scale Trivia:** Commercial highway weigh stations use heavy-duty strain gauge load cells to weigh freight trucks for road tariffs. These load cells are shaped like thick metal hockey pucks (resembling Amazon Echo devices). A typical truck scale uses **four puck load cells**, each rated for **$5,000\text{ kg}$ or more**.
* **Availability Note:** Finding high-capacity load cells ($>5,000\text{ kg}$) is common; finding low-capacity load cells ($200\text{ kg}$) is relatively harder and more expensive.

---

## 5. Load Cell Datasheet Specifications & Voltage Analysis

### 5.1 Datasheet Parameters
* **Rated Capacity:** $200\text{ kg}$
* **Sensitivity:** $2.0 \pm 0.05\text{ mV/V}$
* **Input Resistance:** $350 \pm 20\ \Omega$
* **Output Resistance:** $350 \pm 5\ \Omega$
* **Insulation Resistance:** $>5,000\text{ M}\Omega$ (Infinite insulation between internal strain resistors and aluminum body block).
* **Safe Overload Limit:** $150\%\text{ Full Scale } (300\text{ kg})$.
  - *Engineering Warning:* When a person steps onto a scale, dynamic momentum adds to static body weight. The $150\%$ safe overload buffer prevents permanent mechanical deformation (crumpling) of the aluminum block from dynamic stepping impact.
* **Excitation Voltage ($V_{EXC}$):** $10\text{ V to }15\text{ V DC}$ (Recommended: $10\text{ V DC}$).
* **Operating Temperature Range:** $-20^\circ\text{C to }+80^\circ\text{C}$.
* **Temperature Coefficient (TCO):** $0.05\%\text{ FS}$ (Temperature-compensated output).
* **Wiring Color Code:**
  - Power / Excitation Input: **Red** ($V+$) & **Black** ($V-$)
  - Differential Signal Output: **Green** ($S+$) & **White** ($S-$)
  - *Professor Joke:* *"That's why it's our favorite because it has LaSalle colors! (Green and White)."*

---

### 5.2 Microvolt Level Calculations

Given an Excitation Voltage $V_{EXC} = 10\text{ V DC}$ and Sensitivity $= 2.0\text{ mV/V}$:

1. **Full-Scale Output Voltage ($V_{out, max}$ at $200\text{ kg}$):**
   $$V_{out, max} = \text{Sensitivity} \times V_{EXC} = 2.0\frac{\text{mV}}{\text{V}} \times 10\text{ V} = 20\text{ mV DC}$$

2. **Voltage Proportionality Table:**

   | Weight Load | Output Voltage Calculation | Sensor Output Voltage |
   | :--- | :--- | :--- |
   | **$200.0\text{ kg}$ (Full Scale)** | $2.0\text{ mV/V} \times 10\text{ V}$ | $20.00\text{ mV}$ |
   | **$100.0\text{ kg}$** | $20\text{ mV} \times \frac{100}{200}$ | $10.00\text{ mV}$ |
   | **$50.0\text{ kg}$** | $20\text{ mV} \times \frac{50}{200}$ | $5.00\text{ mV}$ |
   | **$50.1\text{ kg}$** | $5.00\text{ mV} + 0.01\text{ mV}$ | $5.01\text{ mV}$ |
   | **$50.2\text{ kg}$** | $5.00\text{ mV} + 0.02\text{ mV}$ | $5.02\text{ mV}$ |

3. **Step Resolution Voltage ($\Delta V_{step}$ per $0.1\text{ kg}$ step):**
   $$\Delta V_{step} = \frac{V_{out, max}}{N_{steps}} = \frac{20\text{ mV}}{2,000\text{ steps}} = 0.01\text{ mV} = 10\ \mu\text{V}$$

> [!IMPORTANT]  
> Each $0.1\text{ kg}$ weight step generates a tiny signal change of **only $10\ \mu\text{V}$**.

---

## 6. The Amplification Challenge & Failure of Discrete DC BJTs

### 6.1 Required Voltage Amplification Gain ($A_v$)
The Arduino ADC input range is $0\text{ to }3.3\text{ V DC}$. To map full-scale load cell output ($20\text{ mV}$) to maximum ADC input ($3.3\text{ V}$):

$$A_v = \frac{V_{ADC, max}}{V_{sensor, max}} = \frac{3.3\text{ V}}{0.020\text{ V}} = 165$$

### 6.2 The DC Thermal Drift Dilemma
* **Step Budget:** A $0.1\text{ kg}$ step corresponds to $10\ \mu\text{V}$.
* **Max Allowable Amplifier Drift:** Must be strictly $< 10\ \mu\text{V}$, ideally **$\le 5\ \mu\text{V}$** (half of one step resolution) to prevent false display increments.
* **Failure of Discrete BJT DC Amplifiers:**
  - Standard discrete BJT configurations (Emitter-Stabilized, Voltage Divider, Collector Feedback) using transistors like the **2N2222** can achieve $A_v = 165$ (e.g., increasing $R_C$ from $4.7\text{ k}\Omega$ to $5.6\text{ k}\Omega$).
  - However, in laboratory testing (heating stabilizing resistors with a $5\text{ W}$ power resistor), BJT DC Operating Points ($Q$-point) drifted by **hundreds of millivolts** ($> 0.5\text{ V}$).
  - A thermal drift of $500\text{ mV}$ is **$50,000\times$ larger** than the $10\ \mu\text{V}$ signal budget! Direct DC BJT amplification is completely unusable for static microvolt measurement.

---

## 7. Chopper-Stabilized Signal Conditioning Architecture

### 7.1 Architecture Principle: DC $\to$ AC $\to$ DC
To achieve high gain without DC thermal drift:
1. **Modulation:** Chop the static small **DC signal** ($0-20\text{ mV}$) into an **AC square wave**.
2. **AC Amplification:** Amplify the AC square wave using an **AC-coupled Amplifier** ($A_v = 165$). Coupling capacitors block DC $Q$-point shifts. Thermal drift only shifts the baseline DC position of the AC signal, leaving the **Peak-to-Peak Amplitude ($V_{p-p}$)** uncorrupted!
3. **Demodulation:** Convert the amplified AC signal back into a clean, drift-free, large **DC signal** ($0-3.3\text{ V}$) using a **Synchronous Sample-and-Hold / Peak Detector**.

> [!NOTE]  
> **Signal Conditioning:** The technical term for this input signal processing chain. Professor comment: *"This is why you're in CSE (Computer Systems Engineering). This is the meat of being a CSE."*

---

### 7.2 System Block Diagram

```
                             +-------------------------------------------------------+
                             |              Chopper Control Clock                    |
                             +-------------------------------------------------------+
                                 |                                               |
                                 v                                               v
+------------------+     +---------------+     +------------------+     +---------------+     +---------------+
| Load Cell Sensor | DC  | Stage 1:      | AC  | Stage 2:         | AC  | Stage 3:      | DC  | Arduino       |
| Output           |---->| JFET #1 Shunt |---->| High-Gain AC Amp |---->| JFET #2 Peak  |---->| ATmega328 ADC |
| (0 - 20 mV DC)   |     | Chopper       |     | & Buffer (Av=165)|     | Detector S&H  |     | (0 - 3.3 V DC)|
+------------------+     +---------------+     +------------------+     +---------------+     +---------------+
```

---

### 7.3 Stage-by-Stage Circuit Implementation

#### Stage 1: Input DC-to-AC Chopper Modulator (JFET #1)
* **Configuration:** JFET Switch #1 connected in shunt across the input signal path to ground.
* **Gate Drive Signal:** Square wave control clock applied to Gate ($V_{GS}$).
* **Operation:**
  - When $V_{GS} = 0\text{ V}$: JFET turns **ON** ($R_{DS(on)} \approx 0\ \Omega$), shorting the signal path to ground ($0\text{ V}$).
  - When $V_{GS} = V_p$ (Pinch-off): JFET turns **OFF** (open circuit), allowing the DC load cell voltage to pass through.
* **Output Waveform:** AC square wave centered at $V_{DC}/2$ with $V_{p-p} = V_{DC, loadcell}$.

```
      Input DC Signal                     Gate Drive (VGS)                  Chopped AC Waveform
   +--------------------+               +---+   +---+   +---+              +---+   +---+   +---+
   | 0 - 20 mV DC       |               | 0 |   | 0 |   | 0 |              |   |   |   |   |   | Vp-p = VDC
   +--------------------+               +---+   +---+   +---+              +---+   +---+   +---+
                                            |Vp     |Vp     |Vp            0V-------------------
```

#### Stage 2: AC Amplifier Stage & Output Buffering
* **Amplifier Configuration:** BJT Common Emitter AC amplifier using a **2N2222** transistor.
* **Gain Tuning:** Collector resistor $R_C$ increased (e.g., from $4.7\text{ k}\Omega$ to $5.6\text{ k}\Omega$) to set $A_v = 165$.
* **DC Blocking:** Coupling capacitors block all transistor $Q$-point thermal drift.
* **Buffering Options:** High output impedance of the AC stage is buffered using either:
  1. **BJT Emitter Follower**, OR
  2. **JFET Source Follower** (Preferred for extremely high input impedance to avoid loading the amplifier).
* **Output Waveform:** Amplified AC square wave with peak-to-peak amplitude $V_{p-p} = 165 \times V_{DC, loadcell} = 0\text{ to }3.3\text{ V}_{p-p}$.

#### Stage 3: Synchronous Peak Envelope Detector / Sample & Hold (JFET #2)
* **Configuration:** Series JFET Switch #2 feeding a Hold Capacitor ($C_H$).
* **Synchronization:** Gate of JFET #2 is driven synchronously by the **exact same control clock** used in Stage 1!
* **Operation:**
  - AC signal passes through a series capacitor, shifting its baseline so negative peaks align with $0\text{ V}$.
  - JFET #2 turns ON at the exact moment the AC signal reaches its positive peak.
  - Capacitor $C_H$ charges to the peak voltage $V_{peak} = 3.3\text{ V DC}$.
  - During the chop interval, JFET #2 turns OFF; $C_H$ holds the DC voltage stable for the Arduino ADC to digitize.
* **Output Signal:** Fully restored, amplified, drift-free **DC Voltage ($0\text{ to }3.3\text{ V DC}$)**.

---

## 8. Summary Matrix of Dual JFET Roles

| JFET Component | Operational Function | Gate Switching State | Primary Circuit Objective |
| :--- | :--- | :--- | :--- |
| **JFET Switch #1** | **Chopper Modulator** | Toggles $V_{GS} = 0\text{ V}$ (ON) vs $V_{GS} = V_p$ (OFF) | Converts static microvolt DC sensor voltage into an AC square wave. |
| **JFET Switch #2** | **Synchronous Sample & Hold** | Clocked synchronously with Chopper #1 Clock | Samples AC signal peak and charges hold capacitor $C_H$ to restore drift-free amplified DC voltage. |

---

## 9. Key Formulas & Engineering Equations

1. **Discrete Weight Step Resolution ($N_{steps}$):**
   $$N_{steps} = \frac{W_{max}}{\Delta W} = \frac{200.0\text{ kg}}{0.1\text{ kg}} = 2,000\text{ steps}$$

2. **ADC Bit Resolution Condition:**
   $$2^n \ge N_{steps} \implies 2^{11} = 2,048 \ge 2,000\ \text{(11 bits required)}$$

3. **Load Cell Full-Scale Voltage Output:**
   $$V_{out, max} = \text{Sensitivity (mV/V)} \times V_{EXC} = 2.0\frac{\text{mV}}{\text{V}} \times 10\text{ V} = 20\text{ mV}$$

4. **Step Resolution Voltage ($\Delta V_{step}$):**
   $$\Delta V_{step} = \frac{V_{out, max}}{N_{steps}} = \frac{20\text{ mV}}{2,000} = 10\ \mu\text{V}$$

5. **Required System Voltage Gain ($A_v$):**
   $$A_v = \frac{V_{ADC, max}}{V_{out, max}} = \frac{3.3\text{ V}}{0.020\text{ V}} = 165$$

6. **Mass-Spring Resonant Frequency:**
   $$f = \frac{1}{2\pi}\sqrt{\frac{k}{m}}$$

---

## 10. Exam Tips, Trivia & Professor Comments Index

1. **Resolution vs. Accuracy:** Exam questions often test the distinction between resolution (step increment size) and accuracy (closeness to true value).
2. **Piezo Sensor Static Failure:** Remember for exams that piezoelectric transducers CANNOT measure static weights directly because their output decays to $0\text{ V}$ under constant strain.
3. **Load Cell Overload Calculation:** Safe overload capacity ($150\% = 300\text{ kg}$) accommodates dynamic stepping momentum without permanent mechanical destruction.
4. **DC Drift vs. AC Amplification:** Direct DC amplification fails at high gain ($A_v = 165$) due to thermal $Q$-point drift ($>500\text{ mV}$). Chopper amplifiers eliminate DC drift by converting DC to AC, amplifying in the AC domain, and demodulating back to DC.
5. **Datasheet Color Codes:** LaSalle colors—Red/Black (Input Excitation), Green/White (Differential Signal Output).
6. **BJT Part Numbers & Values:** Discrete 2N2222 BJT AC amplifier boosted from $A_v = 125$ to $A_v = 165$ by increasing $R_C$ from $4.7\text{ k}\Omega$ to $5.6\text{ k}\Omega$.
7. **Signal Conditioning Definition:** The fundamental discipline of CSE bridging physical sensors to microcontroller inputs.
