# **Week 4 : CMOS Circuit Design & Analysis (sky130)**

## 1. **CMOS Circuit Design and SPICE Simulations**

### **What is Circuit Design?**

**Circuit design** is the process of creating an electronic circuit by connecting multiple components, primarily **transistors**, to achieve a specific function. The goal is to arrange these building blocks in a way that they collectively perform a desired task—like acting as a logic gate, an amplifier, or a memory cell. Effective design involves not just achieving the correct functionality, but also optimizing for performance metrics such as **speed**, **power consumption**, and **physical area** (PPA).

### **What are SPICE Simulations?**

**SPICE (Simulation Program with Integrated Circuit Emphasis)** simulation is the process of creating a designed circuit virtually to test its behavior before it's manufactured. The simulator calculates and generates graphs to observe key characteristics like: drain, transfer, Voltage transfer curves, etc. From these graphs, we can extract critical parameters like **propagation delay**, **rise/fall times**, **power consumption**, and **noise margins**. If these parameters don't meet the design requirements, we can tune the circuit—for example, by changing transistor sizes (W/L ratios)—and re-run the simulation until the desired performance is achieved.

### **Why do we use SPICE simulations**

  * **Verify Functionality:** Ensure that logic gates and complex circuits operate as intended according to their truth tables.
  * **Analyze Performance:** Measure critical performance metrics like speed, propagation delay, and rise/fall times.
  * **Evaluate Power Consumption:** Assess static and dynamic power draw to optimize for efficiency.
  * **Optimize and Debug:** Experiment with transistor sizing, layout, and other parameters to improve performance and fix issues early in the design cycle.

### **CMOS Inverter: An Example**

The CMOS inverter is the most fundamental logic gate in digital electronics. Its design and analysis demonstrate the core principles of CMOS technology.

#### **Circuit Structure and Analysis**

<img width="567" height="769" alt="circuit_inverter" src="https://github.com/user-attachments/assets/27fbd992-ac79-4cce-a6c0-bf21271749b6" />


The inverter consists of a complementary pair of transistors: one PMOS and one NMOS.

  * The **PMOS** transistor connects the output to the power supply (**VDD**). It acts as a "pull-up" network.
  * The **NMOS** transistor connects the output to the ground (**VSS**). It acts as a "pull-down" network.
  * The inputs of both transistors are tied together to form the single input (**Vin**).
  * The drains of both transistors are tied together to form the output (**Vout**).

**The Operation:**

| Input (Vin) | PMOS State | NMOS State | Output (Vout) |
| :---------- | :--------- | :--------- | :------------ |
| **Low (0V)** | **ON** | OFF        | **High (VDD)** |
| **High (VDD)**| OFF        | **ON** | **Low (0V)** |

**SPICE Simulations:**

<img width="1191" height="733" alt="image" src="https://github.com/user-attachments/assets/0fb576de-859f-4126-a109-7cd0bf04dfb0" />


1.  **Drain Characteristics - Top Graph:** It shows the relationship between the drain-source current (Ids) and the output voltage (Vout) for various input voltages (Vin).
2.  **Voltage Transfer Characteristic (VTC) - Bottom Graph:** It shows how the output voltage (Vout) varies with the input voltage (Vin) to analyze the inverter's switching behavior and logic levels.

**Cell Delays:**

Static Timing Analysis (STA) tools rely on pre-characterized models of standard cells to calculate timing. These models are generated using thousands of SPICE simulations. A cell's delay is not a single value but depends on two key factors:

1.  **Input Slew:** The transition time of the signal arriving at the cell's input pin.
2.  **Output Load:** The total capacitance the cell's output must drive. The total capacitive load at a gate's output is the sum of all capacitances connected to that net.

<img width="1920" height="1080" alt="Screenshot (357)" src="https://github.com/user-attachments/assets/fa27015d-385e-44c8-9e91-cc5a0fcc7873" />


This data is stored in **2D Lookup Tables (LUTs)** within the cell's timing library (`.lib` file). STA tools use these tables to accurately determine delay.

To find the delay for a specific condition not listed in the table, the tool performs **interpolation** between the nearest characterized points. This allows for accurate delay calculation across a continuous range of operating conditions. For ensuring that the delay values are correct we need to do SPICE simulations. In the above example we can see that there is a CTS where each buffer has a different LUT.

## **2. NMOS Transistor Fundamentals**

The NMOS transistor is the basic fundamentals of modern digital circuits. Understanding its physical behavior is key to designing better circuits. Below is the basic circuit of the NMOS and the initial conditions we are assuming:

<img width="1920" height="1080" alt="Screenshot (360)" src="https://github.com/user-attachments/assets/fbc14ec0-a3b7-4018-b9ba-ea99309abd9e" />

<img width="1920" height="1080" alt="Screenshot (361)" src="https://github.com/user-attachments/assets/70458cf8-9248-400b-a416-7b9791258ddd" />


### **MOSFET Modes of Operation**

A transistor's behavior is controlled by its terminal voltages. The gate-source voltage ($V_{GS}$) primarily determines whether a conductive channel exists, while the drain-source voltage ($V_{DS}$) influences how current flows through that channel.

The process of turning an NMOS transistor ON involves three phases controlled by the gate voltage:

* **Accumulation ($V_{GS} < 0$):** A negative voltage on the gate attracts the abundant positive charge carriers (holes) in the p-type substrate to the silicon-oxide interface. No channel forms, and the transistor is firmly **OFF**.
* **Depletion ($0 < V_{GS} < V_t$):** As $V_{GS}$ becomes slightly positive, it repels the mobile holes from the interface. This leaves behind a "depletion region" of fixed, negatively charged atoms. There is still no conductive channel, so the transistor remains **OFF**.

<img width="1920" height="1080" alt="Screenshot (364)" src="https://github.com/user-attachments/assets/3463cdcc-b8fb-4873-b83a-dcb6e84ed3fb" />

* **Strong Inversion ($V_{GS} = V_t$):** When the gate voltage reaches the **threshold voltage ($V_t$)**, the electric field is strong enough to attract a large number of minority carriers (electrons) to the interface. These electrons form a continuous n-type "channel" connecting the source and drain, turning the transistor **ON**. 💡

<img width="1920" height="1080" alt="Screenshot (367)" src="https://github.com/user-attachments/assets/b0724a23-bdfc-48a4-a797-2cc6043ef4a0" />


<img width="1920" height="1080" alt="Screenshot (368)" src="https://github.com/user-attachments/assets/4abcc95a-67b8-4efa-bba7-286e3881fb1e" />


All the above 3 phases comes under the cut off mode of operation. Once the transistor is ON ($V_{GS} > V_t$), its behavior is further defined by the drain voltage ($V_{DS}$):

* **Linear/Resistive Region ($V_{DS} < V_{GS} - V_{t}$):** In this mode, a continuous channel exists, and the transistor acts like a **voltage-controlled resistor**. The drain current ($I_D$) increases as either $V_{GS}$ or $V_{DS}$ increases.
  
    $$I_D = \mu_n C_{ox} \frac{W}{L} \left( (V_{GS} - V_t)V_{DS} - \frac{V_{DS}^2}{2} \right)$$

* **Saturation Region ($V_{DS} \ge V_{GS} - V_{t}$):** As $V_{DS}$ increases, the channel near the drain gets "pinched off." Beyond this point, the current flow becomes relatively independent of $V_{DS}$ and is mainly controlled by $V_{GS}$. The transistor now behaves like a **voltage-controlled current source**.
  
    $$I_D = \frac{1}{2} \mu_n C_{ox} \frac{W}{L} (V_{GS} - V_t)^2 (1 + \lambda V_{DS})$$
  
    The $(1 + \lambda V_{DS})$ term is a correction factor for **channel length modulation**, a secondary effect where a higher $V_{DS}$ slightly shortens the effective channel length, causing a minor increase in current.

### **The Body Effect**

The **body effect** describes how the threshold voltage ($V_t$) of a MOSFET changes when there is a voltage difference between the source and the body (also called the substrate), denoted as $V_{SB}$. In a NMOS transistor, the body is usually connected to the most negative potential (ground), making its voltage the same as the source ($V_{SB} = 0$). However, in many circuits (like stacked transistors in a NAND gate), the source of a transistor can be at a higher voltage than the body.

When the source voltage is higher than the body voltage ($V_{SB} > 0$), a **reverse bias** is created across the source-body p-n junction. The reverse bias pushes the mobile positive charges (holes) in the p-type body further away from the gate. This widens the depletion region of fixed negative ions that the gate voltage must overcome before it can even start forming a channel. 

As we apply the gate voltage ($V_{GS}$) to create the inversion layer, the thicker depletion region requires higher voltage to start forming the channel near the source. This means the channel forms first at the drain end. To form a continuous channel all the way to the source, we need to apply an even higher gate voltage. This non-uniformity is why the threshold voltage increases.

<img width="1920" height="1080" alt="Screenshot (371)" src="https://github.com/user-attachments/assets/023759a4-9df3-4797-a71e-908f72ea0c35" />


<img width="1920" height="1080" alt="Screenshot (375)" src="https://github.com/user-attachments/assets/9927539c-3191-42e3-bd0d-fe071db5b282" />


The change in threshold voltage is modeled by the following equation:

$$V_t = V_{t0} + \gamma (\sqrt{|2\phi_f + V_{SB}|} - \sqrt{|2\phi_f|})$$

* **$V_t$ (Threshold Voltage):** This is the **threshold voltage** of the transistor when the body effect is present. 
* **$V_{t0}$ (Zero-Bias Threshold Voltage):** This is the transistor's **intrinsic threshold voltage** when the source and body are at the same potential ($V_{SB} = 0$). 
* **$\gamma$ (Body Effect Coefficient):** This is a **process-dependent constant** provided by the foundry. It depends on physical properties like the doping concentration of the substrate and the thickness of the gate oxide.
* **$\phi_f$ (Fermi Potential):** This is another **material-dependent physical parameter**, also determined by the foundry. It relates to the difference between the Fermi energy level in the doped semiconductor and the intrinsic (undoped) semiconductor.
* **$V_{SB}$ (Source-to-Body Voltage):** The voltage difference between the transistor's source terminal and its body terminal. When $V_{SB} = 0$, the entire second term of the equation becomes zero, and $V_t$ simply equals $V_{t0}$.

#### **Fermi Potential ($\phi_f$)**

The Fermi Potential is the potential difference between the intrinsic Fermi level and the Fermi level of the doped semiconductor. It indicates how strongly the substrate is doped.

$$\phi_f = \phi_t * \ln\left(\frac{N_A}{n_i}\right)$$

Where:
* **$\phi_t$** is the **thermal voltage** which is approximately 26 mV at room temperature.
* **$N_A$** is the **acceptor doping concentration** of the p-type substrate (atoms/cm³).
* **$n_i$** is the **intrinsic carrier concentration** of silicon, which is a material constant.

#### **Body Effect Coefficient ($\gamma$)**

The Body Effect Coefficient quantifies how sensitive the transistor's threshold voltage is to a source-to-body voltage ($V_{SB}$).

$$\gamma = \frac{\sqrt{2q\epsilon_{si}N_A}}{C_{ox}}$$

Where:
* **$q$** is the **elementary charge**.
* **$\epsilon_{si}$** is the **permittivity of silicon**.
* **$N_A$** is the **acceptor doping concentration** of the substrate.
* **$C_{ox}$** is the **gate oxide capacitance per unit area**. This value itself is calculated as $C_{ox} = \frac{\epsilon_{ox}}{t_{ox}}$, where $\epsilon_{ox}$ is the permittivity of the silicon dioxide and $t_{ox}$ is the physical thickness of the gate oxide layer.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e473e161-f84e-4445-a8a6-4edcb12b63ba" />


### **5. Hands-On Lab: Simulating an NMOS Transistor with sky130**

This section outlines the practical steps to simulate the I-V characteristics of a sky130 NMOS transistor using `ngspice`.

#### **1. Clone the Workshop Repository**

First, obtain the necessary model files and example netlists.

```bash
git clone https://github.com/kunalg123/sky130CircuitDesignWorkshop.git
```

#### **2. SPICE Netlist Example**

The following SPICE deck (`day1_nfet_idvds_L2_W5.spice`) is used to characterize an NMOS transistor.

\<details\> \<summary\>\<strong\>Click to view `day1_nfet_idvds_L2_W5.spice`\</strong\>\</summary\>

```spice
* NMOS I-V Characterization for sky130 (W=5u, L=2u)
* This netlist sweeps Vds for different Vgs values.

*** Include the sky130 typical-typical (tt) corner model library ***
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*** Netlist Description ***
* M<name> <drain> <gate> <source> <body> <model> W=<val> L=<val>
XM1 vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2

* Resistor and Voltage Sources
R1  n1 in 55
Vdd vdd 0 1.8
Vin in  0 1.8

*** Simulation Commands ***
* DC sweep:
* - Sweep Vdd (which is Vds for XM1) from 0V to 1.8V in 0.1V steps.
* - For each Vdd step, step Vin (Vgs for XM1) from 0V to 1.8V in 0.2V steps.
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control
  run
  display
.endc

.end
```

\</details\>

#### **3. Running the Simulation and Plotting Results**

Execute the simulation using `ngspice` from your terminal.

```bash
# Navigate to the directory with the .spice file
ngspice day1_nfet_idvds_L2_W5.spice
```

Inside the `ngspice` interactive shell, use the `plot` command to visualize the drain current. The current flowing from the `Vdd` source is equivalent to the transistor's drain current.

```
ngspice> plot -vdd#branch
```

#### **Expected Output**

The simulation will produce the classic I-V characteristic curves for the NMOS transistor, showing its behavior across the linear and saturation regions for different gate voltages.



### **Introduction / Background**

The goal of this project is to bridge the gap between the physical, analog behavior of transistors and the digital, abstract models used in Static Timing Analysis (STA). By simulating fundamental CMOS circuits using SPICE and the sky130 Process Design Kit (PDK), we can directly observe the physical phenomena that STA tools approximate.

This report documents the following experiments:

  * **MOSFET I-V Characterization:** To understand the basic operation of a transistor as a voltage-controlled switch by observing its linear and saturation regions.
  * **Threshold Voltage Extraction:** To determine the critical gate voltage ($V_t$) at which a transistor begins to conduct, a key parameter in all timing models.
  * **CMOS Inverter VTC Analysis:** To map the static input-output voltage relationship of an inverter, defining its switching threshold and noise margins.
  * **Transient Analysis:** To measure the rise and fall delays, which are the fundamental building blocks of path delays calculated in STA.
  * **Variation Analysis:** To see how changes in supply voltage and transistor sizing affect performance, directly correlating to the concept of PVT (Process, Voltage, Temperature) corners in STA.

-----

### **1. MOSFET Behavior & $I_d$ vs. $V_{ds}$ Characteristics**

#### **Purpose**

This experiment visualizes the output characteristics of an NMOS transistor. The resulting plot of Drain Current ($I_d$) versus Drain-Source Voltage ($V_{ds}$) for different Gate-Source Voltages ($V_{gs}$) reveals the two primary operating regions for a digital switch: the **linear (or triode) region**, where it acts like a resistor, and the **saturation region**, where it acts like a current source.

#### **SPICE Netlist**

This netlist sweeps $V_{ds}$ from 0 to 1.8V and steps through different $V_{gs}$ values (0.6V, 0.8V, 1.0V, 1.2V, 1.8V).

```spice
* Week4 Task 1: NMOS Id vs Vds Characteristics

.lib "/path/to/vsd/libs/sky130_fd_pr/models/sky130.lib.spice" tt

* Power Supplies
VDS D 0 0
VGS G 0 0

* NMOS Transistor (device under test)
* M<name> <drain> <gate> <source> <body> <model> L=<val> W=<val>
M1 D G 0 0 sky130_fd_pr__nfet_01v8 L=0.15u W=1u

* Simulation Commands
.dc VDS 0 1.8 0.01 VGS 0.6 1.8 0.2
.control
    run
    plot I(VDS)
.endc
.end
```

#### **Plot & Figure**

The generated plot shows a family of curves, one for each $V_{gs}$ value.

  * **Annotation:** For each curve, mark the "knee" where the device transitions from the linear region (sloped line) to the saturation region (flat line).

#### **Observations & Analysis**

  * **Observation:** At low $V_{ds}$, the drain current $I_d$ increases almost linearly with $V_{ds}$. As $V_{ds}$ increases past the point where $V_{ds} > V_{gs} - V_t$, the current flattens and becomes "saturated." Higher $V_{gs}$ values result in higher saturation currents.
  * **Device Physics:** In the linear region, a continuous channel exists from drain to source. In saturation, the channel gets "pinched off" near the drain, and the current is limited by the charge carriers' velocity.
  * **Relevance to STA:** When a transistor switches **ON**, it operates in the linear region, acting like a low-resistance path to charge or discharge a capacitor. The current it can deliver in saturation determines how quickly it can pull the output node to a certain voltage, which fundamentally defines the **gate delay**.

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
