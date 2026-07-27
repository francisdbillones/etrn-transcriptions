# ETRN2 Lecture 7: LED Circuits, Specialized Diodes, Diode Testing & History of Transistors

> [!NOTE]
> **Source Material**: Lecture 7 Audio Transcription (`lec7.txt`)  
> **Scope**: Exhaustive academic notes capturing all technical concepts, mathematical derivations, component part numbers, laboratory warnings, device physics, exam tips, professor complaints, and historical tangents.

---

## 1. Administrative Notes, System Overhead & Professor Tangents

* **Zoom CPU Load Remark**: At the beginning of the lecture screen share, the professor inspects task manager / CPU load:
  * **Observed Load**: Hovering around $30\% - 40\%$, peaking at **$60\%$ CPU utilization**.
  * **Professor Comment**: Remarks *"Gosh, Zoom is heavy,"* but notes it is *"within bounds, so not a big deal."*
* **Course Pace & Schedule**:
  * The class is noted to be well ahead of the other section.
  * Lecture 7 completes the diode unit. The subsequent lecture will be **face-to-face**, starting the next major topic: **Transistors, Terminal Currents, and BJT Biasing**.

---

## 2. Light Emitting Diodes (LEDs) — Physics, Color Variations & Parallel Constraints

### 2.1 Forward Voltage ($V_F$) Variations by Color & Material Chemistry
LEDs emit incoherent light across visible wavelengths as well as invisible spectrums (Infrared / UV) via recombination of charge carriers across a semiconductor bandgap. Because different colors require different semiconductor substrate materials and doping compounds (e.g., GaAs, GaP, GaN, InGaN), their forward threshold voltages ($V_F$) vary substantially:

| LED Color | Typical Forward Voltage Range ($V_F$) | Material / Physical Characteristics |
| :--- | :--- | :--- |
| **Red** | $1.6\text{ V} - 1.7\text{ V}$ | Lowest $V_F$ requirement |
| **Yellow / Amber** | $1.8\text{ V} - 2.1\text{ V}$ | Moderate $V_F$ |
| **Green** | $1.9\text{ V} - 4.0\text{ V}$ | Wide range depending on traditional GaP vs. modern InGaN processes |
| **Blue** | $2.5\text{ V} - 3.7\text{ V}$ | High $V_F$; wide variance across manufacturers |
| **White** | $\sim 3.0\text{ V} - 3.4\text{ V}$ | High $V_F$; typically a Blue GaN LED coated with yellow phosphor |

> [!NOTE]
> Forward voltage ranges overlap between manufacturers due to differing internal semiconductor fabrication technologies.

---

### 2.2 Critical Textbook Graph Inaccuracy & Exam Warning

> [!WARNING]
> **Professor Warning on Textbook $I\text{-}V$ Curves**:
> The professor explicitly warns students **not to take textbook $I\text{-}V$ conduction graphs too literally!**
> * **Discrepancy Pointed Out**: In standard textbook slides, the conduction curves for Green ($1.9\text{ V}-4.0\text{ V}$) and Blue ($2.5\text{ V}-3.7\text{ V}$) are often drawn right next to each other as if their lower threshold bounds are nearly identical.
> * **Reality**: numerically, their lower bounds differ by over $0.6\text{ V}$. The textbook graph curves are rendered purely for illustrative entertainment to show that curves shift rightward with higher photon energy. **Do not rely on visually estimated plot intersections from slide graphs for numerical exam calculations!**

---

### 2.3 Paralleling LEDs: Incandescent Comparison vs. Multi-Color LED Failure

#### Incandescent Bulbs vs. LEDs
* **Incandescent Bulbs / Household AC**: Incandescent bulbs act as pure resistive loads. In residential wiring and basic CETRON1 circuits, multiple bulbs plug directly in parallel across a $220\text{V} / 110\text{V}$ voltage supply and operate independently.
* **LEDs in Parallel**: LEDs have an exponential diode conduction curve ($I_D \propto e^{V_D/V_T}$). They **must** be current-limited. Without a series resistor or current limiter, thermal runaway occurs and the LED will burn out immediately.

#### Why Naïve Parallel Connection of Different Color LEDs Fails
If a student attempts to wire a **Red LED** ($V_F \approx 1.7\text{ V}$), a **Green LED** ($V_F \approx 2.1\text{ V}$), and a **Blue LED** ($V_F \approx 3.0\text{ V}$) in parallel using a **single shared series resistor**:

```
             +V_CC
               │
              [R_S] (Shared Resistor)
               │
       ┌───────┼───────┐
       │       │       │
      ▲ Red   ▲ Green ▲ Blue
     ───     ───     ───
       │       │       │
      GND     GND     GND
```

1. As supply voltage increases, the **Red LED** reaches its forward bias threshold first at **$1.7\text{ V}$**.
2. Once conducting (e.g., drawing $20\text{ mA}$), the Red LED **clamps the entire parallel node voltage to $1.7\text{ V}$**.
3. Because the Green ($2.1\text{ V}$) and Blue ($3.0\text{ V}$) LEDs are tied to the exact same node, they receive only $1.7\text{ V}$ across their terminals.
4. **Result**: A $1.7\text{ V}$ potential line **never intersects** the conduction curves of the Green or Blue LEDs. The Red LED glows brightly, while the Green and Blue LEDs draw zero (or microampere-level) current, remaining **completely unlit or extremely dim**.

> [!IMPORTANT]
> **When CAN LEDs be paralleled with a shared resistor?**  
> Paralleling LEDs with a single shared resistor works **ONLY** if the LEDs have tightly matched forward voltages — meaning they are of the **same color, same model, and from the same manufacturer batch**. Even then, separate resistors are preferred to prevent current hogging due to thermal drift.

---

### 2.4 Proper Multi-Color LED Circuit Topology
To properly light LEDs of different colors simultaneously, assign a **dedicated individual series resistor** to each LED branch:

```
                  +V_CC
                    │
       ┌────────────┼────────────┐
       │            │            │
     [R_R]        [R_G]        [R_B]  (Separate Resistors)
       │            │            │
      ▲ Red        ▲ Green      ▲ Blue
     ─── (1.7V)   ─── (2.1V)   ─── (3.0V)
       │            │            │
      GND          GND          GND
```

* **Advantage**: Each LED independently establishes its distinct forward voltage drop ($V_{F,R}, V_{F,G}, V_{F,B}$), allowing the designer to set exact branch currents ($I_{D,R}, I_{D,G}, I_{D,B}$) by selecting specific resistor values.

---

### 2.5 Luminous Efficiency & Perceived Human Brightness

* **Blue LED Efficiency**: Blue LEDs exhibit extremely high electrical-to-optical conversion efficiency. A tiny forward current produces an intense visual output.
* **Human Eye Perception**: The human eye has low spectral sensitivity to blue light compared to green-yellow light. However, because modern Blue LEDs are physically so efficient, they appear intensely bright even at minimal current levels.
* **Brightness Matching**: To achieve uniform perceived brightness across Red, Green, and Blue indicators on a front panel, you must calculate **different branch currents** (and thus different resistor values) for each color rather than forcing equal current.

---

### 2.6 Professor Rant: Overly Bright Indicator LEDs in Commercial Products

> [!NOTE]
> **Professor Side-Comment / Rant**:  
> The professor criticizes modern consumer product design for using excessively blinding indicator LEDs:
> * **Computer PC Fans & Accessories**: Blinding LEDs added unnecessarily just to grab consumer attention.
> * **Home Air Conditioners**: AC units featuring status LEDs that are far too bright at night, illuminating entire bedrooms and disrupting sleep.
> * **Consumer Workaround**: The professor notes that thankfully, better manufacturers include a remote control setting to dim or completely turn off display LEDs after switching the unit on.

---

## 3. LED Indicator Circuit Design & Thermal Degradation

### 3.1 Design Guidelines & Current Selection

* **Textbook vs. Real-World Current**: Textbooks frequently recommend $I_D = 20\text{ mA}$. The professor warns that **$20\text{ mA}$ is way too high** for indoor status indicators, producing blinding glare and wasting power.
* **Professor's Design Recommendation**:
  * **Default Starting Target**: **$I_D = 5\text{ mA}$** (ample for clear indication indoors).
  * **Low-Power/Indoors**: **$1\text{ mA} - 2\text{ mA}$** is already clearly visible.
  * **High Current ($>15-20\text{ mA}$)**: Reserved strictly for outdoor devices exposed to direct bright sunlight.

---

### 3.2 Thermal Degradation Mechanism & LED Lifespan

* **Degradation Phenomenon**: Over time, LED light output gradually dims (lumen depreciation). This reduction in intensity is **not caused by external dust/dirt**, but by internal crystal lattice breakdown.
* **Thermal Impact**: Operating LEDs at high currents generates excessive PN junction heat, accelerating thermal degradation and drastically shortening operational lifespan.
* **Design Golden Rule**: **Running LEDs at lower currents yields significantly longer operating life.**

---

### 3.3 Series Resistor Formula & Step-by-Step Numerical Example

#### General Formula
$$R_S = \frac{V_{source} - V_F}{I_D}$$

Where:
* $V_{source}$ = DC supply voltage ($\text{V}$)
* $V_F$ = Forward voltage drop of the LED at design current ($\text{V}$)
* $I_D$ = Target forward operating current ($\text{A}$)

#### Lecture Example Problem
* **Given Parameters**:
  * Supply Voltage ($V_{source}$) = $5.0\text{ V}$
  * Yellow LED Forward Voltage ($V_F$) = $1.8\text{ V}$
  * Target Operating Current ($I_D$) = $10\text{ mA} = 0.010\text{ A}$
* **Step 1: Calculate Exact Resistance**:
  $$R_S = \frac{5.0\text{ V} - 1.8\text{ V}}{0.010\text{ A}} = \frac{3.2\text{ V}}{0.010\text{ A}} = 320\ \Omega$$
* **Step 2: Component Selection using EIA E12 Resistor Series**:
  * $320\ \Omega$ is not a standard commercial value in the **EIA E12** resistor series.
  * Standard E12 values in that decade: $270\ \Omega, 330\ \Omega, 390\ \Omega$.
  * **Selection Rule**: **Always select the next higher standard resistor value** $\rightarrow$ **$330\ \Omega$**.
* **Rationale for Choosing Higher Resistance**:
  * Selecting $330\ \Omega$ lowers the actual current slightly below $10\text{ mA}$ ($I_{actual} = \frac{3.2\text{V}}{330\Omega} \approx 9.7\text{ mA}$).
  * Operating slightly below target current reduces thermal stress, preventing premature diode degradation and extending device lifetime.

---

## 4. Specialized Diode Types

> [!TIP]
> **Exam Alert**: The professor explicitly highlights specialized diodes, noting: *"You might want to take note of things like this because obviously it's going to be released in the quiz, in the objective questions."*

```
Specialized Diodes
├── Varactor Diode (Varicap) ──> Enhanced junction capacitance (C_j ∝ 1/√V_R) ──> Electronic RF Tuning
└── Schottky Diode ──────────> Metal-semiconductor junction ──> Fast switching, low V_F (0.2V-0.3V) ──> SMPS Secondary Rectification
```

---

### 4.1 Varactor Diode (Varicap / Voltage-Variable Capacitance Diode)

* **Definition**: A PN junction diode specifically fabricated to enhance and exploit its internal depletion junction capacitance ($C_j$).
* **Schematic Symbol**: Standard diode symbol merged with a parallel capacitor plate and a diagonal variable arrow.

```
       Anode   │◀───┐  Cathode
       ────────┤    ├────────
               │◀───┘
                 /  (Diagonal variable arrow across capacitor)
```

* **Operating Physics**:
  * Operated under **Reverse Bias** ($V_R$).
  * Increasing reverse voltage widens the depletion region, spreading spatial charges further apart and decreasing capacitance:
    $$C_j \propto \frac{1}{\sqrt{V_R}}$$
* **Applications — Electronic Tuning**:
  * Used in **Tuned Radio Frequency (TRF)** circuits, Voltage-Controlled Oscillators (VCOs), smartphones, televisions, and FM/AM radios.
  * **Historical vs. Modern Tuning**:
    * *Legacy Systems*: Used physical rotary knobs turning mechanical variable capacitors paired with inductors ($LC$ parallel resonant tank circuits) to select frequencies (e.g., $97.1\text{ MHz}$).
    * *Modern Systems*: Replaced mechanical tuning with **Varactor Diodes**. Changing a purely electronic DC control voltage across the varactor varies $C_j$, tuning the resonant reception frequency electrically without moving parts.

---

### 4.2 Schottky Diode

* **Definition**: A specialized diode constructed with a **metal-to-semiconductor junction** (Schottky barrier) rather than a standard semiconductor-to-semiconductor PN junction.
* **Key Characteristics**:
  1. **Ultra-Fast Switching Speed**: Negligible minority carrier storage time allows switching ON and OFF at extreme frequencies (hundreds of kilohertz).
  2. **Low Forward Voltage Drop ($V_F$)**: Typically **$0.2\text{ V} - 0.3\text{ V}$**.
  3. **Sharp $I\text{-}V$ Conduction Profile**: Compared to Germanium diodes (which also have low $V_F \approx 0.2\text{ V}$ but present a "lazy", gradual slope curve), Schottky diodes feature a **very steep, sharp vertical conduction curve** identical in shape to Silicon, but shifted to the $0.2\text{ V} - 0.3\text{ V}$ threshold.

```
 Current (I)
    ▲          Schottky      Silicon
    │          (0.2-0.3V)    (0.7V)
    │           │   │         │   │
    │  Germanium│   │         │   │
    │   (Lazy)  │   │         │   │
    │     /     │   │         │   │
    │    /      │   │         │   │
  ──┴───/───────┼───┼─────────┼───┼───────► Voltage (V)
       0.2V    0.3V          0.7V
```

---

### 4.3 Applications in Switch-Mode Power Supplies (SMPS)

* **Line-Frequency (60 Hz) vs. High-Frequency SMPS**:
  * *Traditional Power Supplies*: Operate at AC line frequency ($50\text{ Hz} / 60\text{ Hz}$). Requires massive, heavy iron-core transformers ($50\text{W}-60\text{W}$ transformer is hand-sized).
  * *Modern SMPS*: Converts input AC to DC, then switches it at high frequencies (**$40\text{ kHz}, 80\text{ kHz}, 100+\text{ kHz}$**)—far above human hearing range.
  * *Transformer Miniaturization*: Operating at $80\text{ kHz}$ shrinks a $50\text{W}-60\text{W}$ transformer down to the size of a matchbox!
* **Why Schottky Diodes are Mandatory**:
  * The output of the high-frequency transformer is high-frequency AC. It must be rectified back to DC.
  * Standard silicon rectifier diodes (e.g., `1N4001`) are far too slow to switch ON and OFF at $80\text{ kHz}$ (they suffer severe reverse recovery losses and overheat).
  * **Schottky diodes are required** for high-frequency secondary rectification in SMPS units.

---

### 4.4 Device Part Numbers & Component Identification

* **Standard Silicon Rectifiers**: `1N4001` - `1N4007`, `1N4148` (signal diode).
* **Germanium Diodes**: `1N60`.
* **Schottky Diodes**: Often use manufacturer prefixes like `1S...` (e.g., `1S...` series), though part numbers vary.
* **Identification Methods**:
  1. Check part number marking or datasheet via Google.
  2. Measure forward voltage on a multimeter ($0.2\text{ V} - 0.3\text{ V}$ indicates Schottky or Germanium; sharp curve indicates Schottky).
  3. Contextual clue: If pulled from the output secondary side of a switch-mode power supply, it is almost certainly a Schottky diode.

---

### 4.5 Diodes Mentioned in Passing
* **Current Regulator Diode**: Mentioned on slide, skipped.
* **Tunnel Diode**: Mentioned on slide (exploits quantum electron tunneling through narrow junctions), skipped for detailed analysis.

---

## 5. Diode & LED Testing Procedures using a Digital Multimeter (DMM)

### 5.1 DMM Diode Test Mode Procedure

A Digital Multimeter set to **Diode Test Mode** ($\rightarrow\vdash$) outputs a small constant current and measures terminal voltage drop.

```
Reverse Bias Test:
  (+) [DMM Red Lead]   ───►| (Cathode/Band | Anode) ◄─── (-) [DMM Black Lead]  ===> Display: "OL" (Open Loop)

Forward Bias Test:
  (+) [DMM Red Lead]   ───►| (Anode | Cathode/Band) ◄─── (-) [DMM Black Lead]  ===> Display: VF (0.4V - 0.7V)
```

1. **Reverse Bias Test**:
   * Attach Red (+) lead to Cathode (banded side) and Black (-) lead to Anode.
   * **Expected Output**: **`OL`** (Open Loop / Open Circuit).
   * *Failure Diagnostic*: If DMM displays a voltage or low resistance in reverse bias, the diode is shorted or leaking and must be discarded.
2. **Forward Bias Test**:
   * Attach Red (+) lead to Anode and Black (-) lead to Cathode (banded side).
   * **Expected Output**: Measured forward voltage drop $V_F$:
     * Standard Silicon Diode: **$0.4\text{ V} - 0.7\text{ V}$**
     * Schottky Diode: **$0.2\text{ V} - 0.3\text{ V}$**
     * Broken/Shorted Diode: Suspiciously low reading (e.g., $0.00\text{ V}$) or `OL` in both directions.

---

### 5.2 Professor Rant: Student Lab Practices

> [!CAUTION]
> **Professor Lab Criticism**:  
> The professor observes bad lab habits among students: *"I noticed that in the lab, nobody was actually doing that or testing the diodes. You all assume that what was given to you is not broken. That's very bad practice!"*  
> Always test components on a DMM prior to inserting them into prototype breadboards.

---

### 5.3 Multimeter Compliance Limits & Color LED Testing Restrictions

* **DMM Internal Compliance Voltage**: Most handheld multimeters are powered internally by two AA batteries ($3\text{ V}$) or a regulated $9\text{ V}$ battery, capping the maximum available diode test compliance voltage to **$\sim 2.0\text{ V} - 3.0\text{ V}$**.
* **Testing Compatibility by Color**:
  * **Red, Yellow, Green, Amber LEDs** ($V_F \le 2.0\text{ V}$): Will **faintly light up** when tested on DMM diode test mode.
  * **Blue & White LEDs** ($V_F \ge 3.0\text{ V}$): **Will NOT light up** on most standard multimeters! The meter's internal test voltage is below their forward threshold voltage.

> [!IMPORTANT]
> **Student In-Class Question**: *"Why does the testing only work on certain color diodes?"*  
> **Professor Answer**: Because different color LEDs have different forward voltages based on their bandgap physics. Blue and white LEDs require $\approx 3.0\text{ V}+$ to forward bias, which exceeds the multimeter's internal test compliance limit ($\approx 2\text{ V}$). A blue LED failing to light up on a multimeter does **not** mean it is broken!

---

## 6. History of Electronics & Invention of the Transistor

### 6.1 19th Century Geniuses vs. 20th Century Applied Scientists

```
19th Century (Empirical Geniuses)                20th Century (Applied Scientists & Engineers)
├── Samuel Morse (Telegraph / Morse Code)        ├── John Ambrose Fleming (Vacuum Tube Diode / Thermionic Valve)
├── Alexander Graham Bell (Telephone)            └── Lee DeForest (Vacuum Tube Triode / Signal Amplification)
└── Thomas Edison (Phonograph, Light Bulb, DC)
```

* **19th Century Inventors**: Samuel Morse, Alexander Graham Bell, and Thomas Alva Edison were brilliant empirical geniuses, but **not formally trained scientists**.
* **20th Century Shift**: Scientists began bridging theoretical physics into practical engineering.
  * **John Ambrose Fleming**: Invented the 2-element vacuum tube diode (thermionic valve) for rectification.
  * **Lee DeForest**: Added a 3rd control element (control grid) to create the **Triode**, enabling controlled electron flow and creating the first active electronic device capable of **amplification**.

---

### 6.2 The ENIAC Computer & Vacuum Tube Reliability Rant

* **ENIAC (Electronic Numerical Integrator and Computer, 1945)**: Built using approximately **17,700 vacuum tubes**.

> [!NOTE]
> **Professor Historical Rant on the ENIAC**:
> While history books and introductory courses celebrate ENIAC on a pedestal, they frequently omit how **ridiculously unreliable** it was:
> * **Thermionic Emission Physics**: Vacuum tubes require high-temperature internal filaments to boil off electrons. Anything operating at extreme heat suffers rapid thermal deterioration.
> * **Statistical Failure Rates**: With 17,700 hot tubes in close proximity, failures occurred constantly and randomly. The computer broke down every couple of days and could never achieve a full week of continuous operation.
> * **Troubleshooting Nightmare**: Locating a single blown tube among 17,700 was brutal. For example, if memory erred, the memory system contained 10,000 to 15,000 tubes! Engineers had to analyze bit patterns, isolate sub-modules of 4,000 tubes, and pull/test each tube manually over days of downtime.
> * **Power Consumption**: Required a dedicated local small power plant generator just to supply power to the filaments and plates.

---

### 6.3 Invention of the Transistor (Bell Laboratories, Dec 1947)

To solve vacuum tube unreliability, researchers at **Bell Laboratories** developed solid-state semiconductor amplifiers.

#### Key Figures (Co-Recipients of the 1956 Nobel Prize in Physics)
1. **William B. Shockley** (1910–1989): Solid-state physicist, head of Bell Labs Semiconductor Research Lab, known as the *"Father of the Transistor"*. Joined Bell Labs in 1936 in the vacuum tube department.
2. **Walter Hauser Brattain**: Experimental physicist; oldest of the trio (characterized by white hair and beard).
3. **John Bardeen**: Theoretical physicist; former researcher at Naval Ordnance Laboratory (munitions, 1941–1945), joined Bell Labs in 1945, later EE Professor at University of Illinois.

> [!IMPORTANT]
> **Unique Historical Nobel Trivia**:  
> John Bardeen is the **only person in history to win TWO Nobel Prizes in Physics**:
> 1. **1956 Nobel Prize in Physics** for the invention of the Transistor.
> 2. **1972 Nobel Prize in Physics** for the microscopic theory of Superconductivity (BCS Theory).

#### Breakthrough Milestones
* **Point-Contact Transistor (December 1947)**:
  * Invented around **Christmas 1947** by Bardeen and Brattain. While colleagues were away on holiday vacation, they stayed in the lab and successfully achieved power amplification.
  * *Structure*: Constructed using two fine cat-whisker contact wires manually pressed against a slab of Germanium crystal.
  * *Drawback*: Extremely fragile; cat-whiskers had to be manually wiggled into microscopic position.
* **Junction Transistor (1948)**:
  * Shockley formulated the underlying junction physics and invented the **Junction Transistor** (solid NPN/PNP semiconductor sandwich), eliminating cat-whiskers and creating a rugged solid-state component.
* **Bell Labs Royalty Waiver**:
  * Bell Labs licensed transistor patents widely for substantial revenue.
  * In honor of founder Alexander Graham Bell (who worked extensively with the deaf), Bell Labs **waived all patent royalties for transistor use in hearing aids**.

---

### 6.4 Shockley Semiconductor, Silicon Valley & The "Traitorous Eight"

```
Shockley Semiconductor Laboratory (Palo Alto, CA - 1954)
       │
       └──► "Traitorous Eight" quit due to Shockley's difficult personality (1957)
               │
               ├──► Fairchild Semiconductor (Mountain View, CA)
               │       │
               │       ├──► Jean Hoerni & Robert Noyce (Planar Process & Silicon IC)
               │       └──► Gordon Moore & Robert Noyce ──► Intel Corporation (1968)
               │                                                │
               │                                                └──► Intel 4004 (1971) & 8088 (1980)
               │
               └──► Texas Instruments (Jack Kilby - Germanium IC, 1958)
```

* **Birth of Silicon Valley**: Shockley left Bell Labs in 1954 to establish **Shockley Semiconductor Laboratory** in **Palo Alto, California**, attracting top talent and giving birth to **Silicon Valley**.
* **The "Traitorous Eight" (1957)**:
  * Shockley was brilliant but had an extremely abrasive management style.
  * In 1957, eight key researchers (known as the *Traitorous Eight*, including **Gordon Moore** and **Robert Noyce**) resigned from Shockley Labs.
* **Fairchild Semiconductor**:
  * The Traitorous Eight founded **Fairchild Semiconductor** in Mountain View, CA. Fairchild became an industry powerhouse, developing silicon planar transistors, logic families, and integrated circuits. Shockley Semiconductor faded commercially while its spin-offs revolutionized global computing.

---

### 6.5 Invention of the Integrated Circuit (IC): TI vs. Fairchild

* **Jack Kilby (Texas Instruments, 1958)**:
  * Conceived placing multiple transistors, resistors, and capacitors on a single Germanium die.
  * Early TI ICs were sold at astronomical prices: **$450 each** in the late 1950s/early 1960s (professor notes: *$450 back then could buy a good second-hand car!*).
* **Robert Noyce (Fairchild Semiconductor, 1959)**:
  * Adapted Jean Hoerni's **Planar Process** to fabricate flat silicon chips with aluminum wiring interconnections embedded directly in silicon dioxide. Filed patent 5 months after Kilby.
* **Patent Lawsuit & Cross-Licensing**:
  * TI sued Fairchild for patent infringement. TI lost the lawsuit, but both companies agreed to cross-license. Modern IC fabrication relies on concepts from both Kilby (TI) and Noyce (Fairchild).

---

### 6.6 Moore's Law

* **Origin (1965)**: **Gordon Moore** (then at Fairchild) published an article in an electronics trade magazine analyzing historical chip data (2, 4, 8, 16 transistors).
* **Observation**: Plotted data points with a straight ruler, observing that transistor density doubled roughly every **18 to 24 months**.
* **10-Year Extrapolation**: Extrapolated that by 1975, an IC would contain ~10,000 transistors, asking: *"What do you think we can make out of 10,000 transistors?"*
* **Nature of the "Law"**: Moore never declared it a physical law. It became a self-fulfilling industry benchmark. Semiconductor foundries (Intel, TSMC, Samsung) benchmarked their roadmap against Moore's curve. Companies that failed to double density every 18–24 months fell behind competitors and went out of business.

---

### 6.7 Microprocessor Evolution & Professor Rant on Software Bloat

* **Founding of Intel (1968)**: Gordon Moore, Robert Noyce, and Andy Grove left Fairchild to found **Intel Corporation**.
* **Intel 4004 (1971)**: First commercial single-chip 4-bit microprocessor (designed by Ted Hoff, Federico Faggin, Masatoshi Shima).
* **Intel 8088 (1980) & x86 Architecture**:
  * Selected for the IBM Personal Computer, establishing the **x86 CPU Architecture**.
  * **x86 Longevity (1980–2026+)**: Modern x86 PCs in 2026 remain backward-compatible with instruction sets established in 1980 over 45+ years ago.
* **Professor Rant on Modern Software Bloat**:

> [!NOTE]
> **Professor Side-Comment / Rant on Modern Programming**:
> While processor hardware performance has exploded exponentially due to silicon scaling, **"sloppy programming has kept up with it!"**
> * Modern multi-gigahertz multi-core CPU performance is heavily squandered by inefficient compilers, bloated software frameworks, and unoptimized code.
> * Additional CPU overhead is consumed by complex cybersecurity software layers required to defend assets against hackers.

---

## 7. Summary of Important Constants, Part Numbers & Historical Dates

| Category | Item / Value | Context / Details |
| :--- | :--- | :--- |
| **Resistor Series** | **EIA E12 Series** | Standard commercial resistor values ($330\ \Omega$ selected over calculated $320\ \Omega$) |
| **DMM Compliance** | $\sim 2.0\text{ V} - 3.0\text{ V}$ | Maximum compliance voltage for DMM diode test mode |
| **Indicator Current** | **$5\text{ mA}$ target** | Professor recommended LED indicator current ($1-2\text{ mA}$ visible indoor) |
| **SMPS Frequencies** | $40\text{ kHz}, 80\text{ kHz}, 100+\text{ kHz}$ | Operating frequencies for switch-mode power supply transformers |
| **Silicon Rectifier** | `1N4001` - `1N4007`, `1N4148` | Standard silicon diode part numbers |
| **Germanium Diode** | `1N60` | Germanium signal diode ($V_F \approx 0.2\text{ V}$, lazy $I\text{-}V$ slope) |
| **Schottky Diode** | `1S...` series | High-speed, low $V_F$ ($0.2-0.3\text{ V}$) metal-semiconductor diode |
| **ENIAC Count** | **~17,700 vacuum tubes** | First electronic computer; highly unreliable (~15k memory tubes) |
| **Transistor Invention** | **December 1947** | Point-contact transistor created at Bell Labs (Christmas 1947) |
| **Nobel Prize (1956)** | Shockley, Brattain, Bardeen | Nobel Prize in Physics for invention of the Transistor |
| **Nobel Prize (1972)** | John Bardeen | Second Nobel Prize in Physics for Superconductivity (BCS Theory) |
| **Early IC Cost** | **$450.00** | Cost of first Texas Instruments integrated circuits in late 1950s/60s |
| **Intel Microprocessors**| **Intel 4004 (1971)**, **8088 (1980)** | First 4-bit CPU (4004); IBM PC CPU establishing x86 architecture (8088) |
| **Moore's Law Rate** | **18 - 24 months** | Transistor doubling interval on integrated circuit dies |
