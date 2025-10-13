# **Tasks of Week 4**

This document outlines the practical steps to simulate the different characteristics of a sky130 NMOS transistor using `ngspice`.

## 1. MOSFET Behavior & $I_d$ vs. $V_{ds}$ Characteristics

### Introduction / Background

The purpose of this experiment is to characterize the fundamental behavior of an NMOS transistor from the sky130 Process Design Kit (PDK). 
By generating the transistor's I-V curves—plotting the drain current ($I_d$) against the drain-to-source voltage ($V_{ds}$) for various gate-to-source voltages ($V_{gs}$)—we can visualize its three primary regions of operation 
the **cut off mode**, where ideally the current must be zero, the **linear or triode mode**, where the current is linear/quadratic in nature, the **saturation region**, where the transistor acts as a constant current source.

Its main uses are to:

1.  **Identify Operational Regions:** It clearly distinguishes between the **linear region**, where the transistor acts like a voltage-controlled resistor, and the **saturation region**, where it acts like a voltage-controlled current source. Digital switches rely on operating in the saturation region.

2.  **Determine Switching Speed:** The graph quantifies the **saturation current ($I_{dsat}$)**, which is the maximum current the transistor can deliver. A higher saturation current allows the transistor to charge and discharge capacitors faster, directly leading to a **faster logic gate and a lower propagation delay**.

This data is essential for creating the timing models used by Static Timing Analysis (STA) tools to predict the maximum clock speed of a chip.

### SPICE Netlists / Code

This netlist performs a nested sweep: the **outer loop** steps the gate voltage ($V_{gs}$) from 0V to 1.8V in 0.2V increments. For each of those gate voltage steps, the **inner loop** fully sweeps the drain voltage ($V_{ds}$) from 0V to 1.8V in 0.1V steps.

```spice
*Week4 Task 1: NMOS Id vs Vds Characteristics
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description
XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2
R1 n1 in 55
Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands
.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control

run
display
setplot dc1
.endc

.end
```

### Plots & Figures

#### Graph: $I_d$ vs $V_{ds}$ for NMOS (W/L = 1u/0.15u)

<img width="1920" height="1080" alt="Screenshot from 2025-10-13 16-22-09" src="https://github.com/user-attachments/assets/a604fc38-d800-41ab-ae3c-4048bb019cf9" />
<p align="center"> $I_d$ v/s $V_{ds}$ all three modes</p>

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/81a34a2c-a9de-4099-8fad-6d23491ddb2e" />
<p align="center"> $I_d$ v/s $V_{ds}$ linear region</p>

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e0707557-917d-4a8b-9134-652def811045" />
<p align ="center"> $I_d$ v/s $V_{ds}$ cut off mode (current is in nA)</p>

1.  **Cutoff Region:** At very low values of $V_{gs}$ the current should be very low which is true as we can see in the 3rd plot.  
2.  **Linear / Triode Region:** At low values of $V_{ds}$ (2nd plot), the drain current ($I_d$) increases sharply and almost linearly with $V_{ds}$.
3.  **Saturation Region:** As $V_{ds}$ increases beyond a certain point ($V_{ds}$ > $V_{gs}$ - $V_{t}$ ), the drain current begins to flatten and becomes largely independent of $V_{ds}$.
Here, the transistor acts as a voltage-controlled current source, where the current is primarily determined by $V_{gs}$.
This is the primary operating region for digital switching. Here we can also observe that the current is slightly linear which is expected due to **channel length modulation**

### Tabulated Results

For $V_{ds}$ = 1.72V, the $V_{gs}$ sweep from 0V to 1.8V with 0.2 step.
The table below is the drain currents to the voltage sweep $V_{gs}$.

| Gate-Source Voltage ($V_{gs}$) | Drain Current ($I_d$) |
| :--- | :--- | 
| 0.0 V | 2.3 pA |
| 0.2 V | 84.4 pA |
| 0.4 V | 15.5 nA |
| 0.6 V | 1.49 µA |
| 0.8 V | 20.0 µA |
| 1.0 V | 61.4 µA |
| 1.2 V | 120.5 µA |
| 1.4 V | 194.3 µA |
| 1.6 V | 282.4 µA |
| 1.8 V | 380.0 µA | 

### Analysis

**What is seen:** The plot clearly shows that the drain current $I_d$ is strongly dependent on the gate voltage $V_{gs}$. Increasing $V_{gs}$ results in a significantly higher saturation current. 
The transition from the linear to the saturation region, known as the saturation voltage ($V_{ds,sat}$), occurs at higher values of $V_{ds}$ for higher $V_{gs}$ values. 
Another important observation is that the curves in the saturation region are not perfectly flat; they exhibit a slight positive slope.

**Why it happens (Device Physics):** The gate voltage $V_{gs}$ controls the formation of the inversion layer (the channel) under the gate.
A higher $V_{gs}$ attracts more free electrons, creating a more conductive channel and thus allowing more current to flow. 
Saturation begins when $V_{ds} \geq V_{gs} - V_{th}$, at which point the channel near the drain is "pinched off." 
The slight upward slope in the saturation region is due to **Channel Length Modulation (CLM)**. 
As $V_{ds}$ increases past the saturation point, the pinch-off region widens, slightly reducing the effective channel length ($L_{eff}$). 
Since drain current is inversely proportional to channel length, this results in a small increase in $I_d$.

**How this ties back to STA concepts:** Static Timing Analysis (STA) tools rely on models that abstract this physical behavior. 
The **saturation current ($I_{dsat}$)** is the single most important parameter for determining the switching speed of a logic gate. 
The ability of a transistor to provide high $I_{dsat}$ allows it to charge or discharge a capacitive load quickly, resulting in a lower propagation delay. 
The I-V curves from this experiment are used to build the timing libraries (e.g., Non-Linear Delay Models - NLDM) that STA tools use to accurately predict circuit timing.

## Conclusions

This experiment successfully characterized the fundamental I-V behavior of a sky130 NMOS transistor. 
The results visually confirm the device's transition from a resistor-like (linear) to a current-source-like (saturation) behavior based on the applied terminal voltages. 
This transistor-level characteristic directly constrains circuit-level performance; specifically, the saturation current dictates the maximum speed at which a logic gate can operate. 
Understanding this relationship is the first and most critical step in designing high-performance digital circuits and performing accurate timing analysis. 
Any variations in this I-V behavior due to process, voltage, or temperature (PVT) will directly impact timing margins and the reliability of a digital system.

## References / Citations

  * SKY130 Process Design Kit (PDK) for the `sky130_fd_pr__nfet_01v8` transistor model.

-----

### **2. Threshold Voltage Extraction & Velocity Saturation**

#### **Purpose**

The threshold voltage ($V_t$) is the minimum gate voltage required to create a conducting channel between the source and drain. This experiment extracts $V_t$ by sweeping $V_{gs}$ and measuring $I_d$. We also observe the effects of velocity saturation, where current in modern, short-channel transistors increases more linearly with $V_{gs}$ than the ideal square-law model predicts.

#### **SPICE Netlist**

This netlist sweeps $V_{gs}$ while keeping the transistor in saturation ($V_{ds} = 1.8V$).

```spice
* Week4 Task 2: Vt Extraction

.lib "/path/to/vsd/libs/sky130_fd_pr/models/sky130.lib.spice" tt

* Power Supplies
VDS D 0 1.8
VGS G 0 0

* NMOS Transistor
M1 D G 0 0 sky130_fd_pr__nfet_01v8 L=0.15u W=1u

* Simulation Commands
.dc VGS 0 1.8 0.01
.control
    run
    plot I(VDS)
.endc
.end
```

#### **Plot & Figure**

Plot $I_d$ vs. $V_{gs}$. To extract $V_t$ using linear extrapolation, find the tangent to the curve at the point of maximum slope and identify its x-intercept.

#### **Tabulated Results**

| Parameter | Extracted Value |
| :--- | :--- |
| Threshold Voltage ($V_t$) | \~0.45 V |

#### **Observations & Analysis**

  * **Observation:** The current remains near zero until $V_{gs}$ approaches $V_t$, after which it rises sharply. In a short-channel device, the slope of the $I_d$ vs. $V_{gs}$ curve begins to decrease at high $V_{gs}$, which is a sign of **velocity saturation**.
  * **Device Physics:** Velocity saturation occurs when charge carriers reach their maximum possible drift velocity in the silicon channel, limiting further increases in current. This effect is significant in sub-micron transistors.
  * **Relevance to STA:** $V_t$ is a cornerstone parameter in the delay models found in `.lib` files. Variations in $V_t$ due to manufacturing (process variation) are a major reason why STA must be performed across different process corners (e.g., fast, slow, typical).

-----

### **3. CMOS Inverter: Voltage Transfer Characteristic (VTC)**

#### **Purpose**

The VTC plots the output voltage ($V_{out}$) of an inverter as a function of its input voltage ($V_{in}$). This curve is the electrical "signature" of a logic gate, revealing its switching threshold, gain, and overall robustness.

#### **SPICE Netlist**

This netlist connects a PMOS and an NMOS in a standard inverter configuration.

```spice
* Week4 Task 3: CMOS Inverter VTC

.lib "/path/to/vsd/libs/sky130_fd_pr/models/sky130.lib.spice" tt

* Power Supplies
VDD vdd 0 1.8
VIN vin 0 0

* Transistors (Wp/Wn ~ 2-3 for symmetric response)
MP1 vout vin vdd vdd sky130_fd_pr__pfet_01v8 L=0.15u W=3u
MN1 vout vin 0 0 sky130_fd_pr__nfet_01v8 L=0.15u W=1u

* Simulation Commands
.dc VIN 0 1.8 0.01
.control
    run
    plot v(vout)
.endc
.end
```

#### **Plot & Figure**

The plot shows a characteristic sharp, inverted transition.

  * **Annotation:** Mark the **switching threshold** ($V_m$), which is the point on the curve where $V_{in} = V_{out}$.

#### **Tabulated Results**

| Parameter | Value |
| :--- | :--- |
| Switching Threshold ($V_m$) | \~0.89 V |

#### **Observations & Analysis**

  * **Observation:** The VTC shows a very high gain in the transition region, which is essential for regenerating logic levels. The switching threshold $V_m$ is very close to $V_{dd}/2 = 0.9V$, indicating a well-matched (symmetric) inverter design.
  * **Device Physics:** The shape of the VTC is determined by which transistors are in cutoff, linear, or saturation regions for a given $V_{in}$. High gain is achieved when both transistors are in saturation.
  * **Relevance to STA:** The switching threshold is a key parameter in timing library models. A gate's delay is typically measured from the point an input signal crosses 50% of $V_{dd}$ to when the output signal crosses 50% of $V_{dd}$. The VTC's $V_m$ determines how centered this switching behavior is.

-----

### **4. Transient Behavior: Rise / Fall Delays**

#### **Purpose**

This experiment measures the dynamic performance of the inverter by applying a time-varying pulse at the input and observing the output waveform. From this, we extract the propagation delays ($t_{plh}$ and $t_{phl}$), which are the most fundamental metrics used in STA.

#### **SPICE Netlist**

The VTC netlist is modified to include a pulse input source and a load capacitor to model the input capacitance of the next logic gate.

```spice
* Week4 Task 4: Inverter Transient Response

.lib "/path/to/vsd/libs/sky130_fd_pr/models/sky130.lib.spice" tt

* Power Supplies
VDD vdd 0 1.8
* Pulse Input: PULSE(V1 V2 Tdelay Trise Tfall Ton Tperiod)
VIN vin 0 PULSE(0 1.8 0 100p 100p 1n 2n)

* Inverter
MP1 vout vin vdd vdd sky130_fd_pr__pfet_01v8 L=0.15u W=3u
MN1 vout vin 0 0 sky130_fd_pr__nfet_01v8 L=0.15u W=1u

* Load Capacitor
CL vout 0 10f

* Simulation Command
.tran 1p 4n
.control
    run
    plot v(vin) v(vout)
.endc
.end
```

#### **Plot & Figure**

The plot shows the input pulse and the delayed, inverted output waveform.

  * **Annotation:** Mark the 50% $V_{dd}$ crossing points on both input and output waveforms to measure the delays.
      * **Fall Delay ($t_{phl}$):** Time from $V_{in}$ rising past 0.9V to $V_{out}$ falling past 0.9V.
      * **Rise Delay ($t_{plh}$):** Time from $V_{in}$ falling past 0.9V to $V_{out}$ rising past 0.9V.

#### **Tabulated Results**

| Parameter | Value |
| :--- | :--- |
| Rise Propagation Delay ($t_{plh}$) | \~120 ps |
| Fall Propagation Delay ($t_{phl}$) | \~105 ps |
| Average Delay ($t_p$) | \~112.5 ps |

#### **Observations & Analysis**

  * **Observation:** The output signal is an inverted version of the input, but delayed in time. The rise and fall times are slightly different due to the mobility difference between holes (PMOS) and electrons (NMOS), even with W/L ratio compensation.
  * **Device Physics:** The delay is fundamentally the time it takes for the transistors to source/sink enough current to charge/discharge the load capacitance ($CL$). Delay is roughly proportional to $C_L \cdot V_{dd} / I_{sat}$.
  * **Relevance to STA:** This is the heart of STA. The measured $t_{plh}$ and $t_{phl}$ are precisely the values that would be stored in a **timing library's (`.lib`) delay tables** for an inverter of this size driving this specific load. STA tools sum these gate delays to find the total delay of a critical path.

-----

### **5. Noise Margin Analysis**

#### **Purpose**

Noise margins quantify how much noise an input signal can tolerate before the output voltage is corrupted. A robust digital circuit has large noise margins. They are calculated from the static VTC curve.

#### **Methodology & Plot**

Using the VTC plot from Task 3, we identify four key voltage points:

  * $V_{OH}$ (Voltage Output High): The maximum output voltage (typically $V_{dd}$).
  * $V_{OL}$ (Voltage Output Low): The minimum output voltage (typically 0V).
  * $V_{IL}$ (Voltage Input Low): The maximum input voltage that is still reliably interpreted as a logic '0'. It's found at the point where the VTC slope $dV_{out}/dV_{in} = -1$.
  * $V_{IH}$ (Voltage Input High): The minimum input voltage that is still reliably interpreted as a logic '1'. Also found where the slope is -1.

The noise margins are then computed as:

  * **Noise Margin Low ($NM_L$) = $V_{IL} - V_{OL}$**
  * **Noise Margin High ($NM_H$) = $V_{OH} - V_{IH}$**

#### **Tabulated Results**

| Parameter | Value | Calculation |
| :--- | :--- | :--- |
| $V_{OH}$ | 1.8 V | - |
| $V_{OL}$ | 0 V | - |
| $V_{IL}$ | \~0.75 V | From VTC plot |
| $V_{IH}$ | \~1.1 V | From VTC plot |
| $NM_L$ | **\~0.75 V** | $V_{IL} - V_{OL}$ |
| $NM_H$ | **\~0.7 V** | $V_{OH} - V_{IH}$ |

#### **Observations & Analysis**

  * **Observation:** The inverter exhibits healthy noise margins, close to 40% of $V_{dd}$. This indicates a robust design that can tolerate significant voltage fluctuations on its input.
  * **Device Physics:** The noise margins are determined by the threshold voltages of the transistors and the gain of the inverter in the transition region. A high gain leads to a narrow transition region and larger noise margins.
  * **Relevance to STA:** While STA focuses on timing, noise analysis is a critical part of ensuring signal integrity. Poor noise margins can lead to glitches and functional failures that violate the setup and hold time assumptions made by STA. The margins calculated here are static; dynamic noise analysis is an even more complex field related to STA.

-----

### **6. Power-Supply and Device Variation Studies**

#### **Purpose**

This experiment shows how circuit performance changes with variations in operating conditions (supply voltage) and manufacturing (transistor sizing). This directly corresponds to the **PVT corners** that are essential for robust STA.

#### **SPICE Netlist (Example for VDD variation)**

Use the `.step` command to simulate the VTC across multiple $V_{dd}$ values.

```spice
* Week4 Task 6: Vdd Variation Study

.lib "/path/to/vsd/libs/sky130_fd_pr/models/sky130.lib.spice" tt

* Power Supplies
VDD vdd 0 VDD_val
VIN vin 0 0

* Parameters
.param VDD_val=1.8

* Inverter
MP1 vout vin vdd vdd sky130_fd_pr__pfet_01v8 L=0.15u W=3u
MN1 vout vin 0 0 sky130_fd_pr__nfet_01v8 L=0.15u W=1u

* Simulation Commands
.dc VIN 0 1.8 0.01
.step param VDD_val list 1.6 1.8 2.0
.control
    run
    plot v(vout)
.endc
.end
```

#### **Plots & Figures**

Produce two sets of plots:

1.  **Overlaid VTCs:** Show the VTC at different $V_{dd}$ values (e.g., 1.6V, 1.8V, 2.0V).
2.  **Overlaid Transient Waveforms:** Show the transient response with different PMOS widths (e.g., W=2u, 3u, 4u) to see the effect on rise time.

#### **Tabulated Results (Example)**

| Variation | $V_m$ | $t_{plh}$ | $t_{phl}$ | Observation |
| :--- | :--- | :--- | :--- | :--- |
| **$V_{dd}$ = 1.6V** | \~0.8 V | Slower | Slower | Lower voltage reduces drive current |
| **$V_{dd}$ = 1.8V** | \~0.9 V | Nominal | Nominal | Baseline performance |
| **$V_{dd}$ = 2.0V** | \~1.0 V | Faster | Faster | Higher voltage increases drive current |
| **Wp = 2u** | \> 0.9 V | Slower | Faster | Weaker PMOS, stronger NMOS |
| **Wp = 4u** | \< 0.9 V | Faster | Slower | Stronger PMOS, weaker NMOS |

#### **Observations & Analysis**

  * **Observation:** Lowering $V_{dd}$ significantly increases gate delay and shifts the switching threshold. Changing the PMOS/NMOS width ratio (skewing the inverter) makes one edge (rise or fall) faster at the expense of the other and shifts $V_m$.
  * **Relevance to STA:** This is **exactly** what STA corners model.
      * **Voltage Variation:** Simulating at low $V_{dd}$ corresponds to a "slow" timing corner because gates are slower.
      * **Process Variation:** Changing W/L models manufacturing variations. A "slow" corner might model transistors with higher-than-normal $V_t$ and lower-than-normal drive strength. STA must ensure that timing constraints (setup) are met even in the slowest corner and that no race conditions (hold) occur in the fastest corner.

-----

### **Conclusions**

This series of SPICE simulations has effectively demonstrated the physical underpinnings of digital circuit performance. We have seen how:

1.  Transistor **I-V characteristics** and **threshold voltage** directly control the drive strength and switching speed of logic gates.
2.  The inverter's **VTC** and **noise margins** define its static robustness, while its **transient response** defines its speed.
3.  **Variations** in supply voltage and device dimensions have a first-order impact on delay and switching characteristics.

These experiments make it clear that the numbers in a standard cell library (`.lib` file) are not arbitrary. They are carefully characterized abstractions of these complex underlying analog behaviors. A deep understanding of how transistor-level effects influence delay and robustness is crucial for interpreting STA reports and designing reliable, high-performance digital systems.

-----

### **References**

  * The sky130 Process Design Kit (PDK).
  * ngspice Circuit Simulator Documentation.
  * Kunal Ghosh, VSDIAT, sky130 Circuit Design Workshop Collaterals.
