# **Tasks of Week 4**

This document outlines the practical steps to simulate the different characteristics of a sky130 NMOS transistor using `ngspice`.

## 1. MOSFET Behavior & $I_d$ vs. $V_{ds}$ Characteristics

### Introduction 

The purpose of this experiment is to characterize the fundamental behavior of an NMOS transistor from the sky130 Process Design Kit (PDK). 
By generating the transistor's I-V curves—plotting the drain current ($I_d$) against the drain-to-source voltage ($V_{ds}$) for various gate-to-source voltages ($V_{gs}$)—we can visualize its three primary regions of operation 
the **cut off mode**, where ideally the current must be zero, the **linear or triode mode**, where the current is linear/quadratic in nature, the **saturation region**, where the transistor acts as a constant current source.

Its main uses are to:

1.  **Identify Operational Regions:** It clearly distinguishes between the **linear region**, where the transistor acts like a voltage-controlled resistor, and the **saturation region**, where it acts like a voltage-controlled current source. Digital switches rely on operating in the saturation region.

2.  **Determine Switching Speed:** The graph quantifies the **saturation current ($I_{dsat}$)**, which is the maximum current the transistor can deliver. A higher saturation current allows the transistor to charge and discharge capacitors faster, directly leading to a **faster logic gate and a lower propagation delay**.

This data is essential for creating the timing models used by Static Timing Analysis (STA) tools to predict the maximum clock speed of a chip.

### SPICE Netlists 

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

**Graph: $I_d$ vs $V_{ds}$ for NMOS (W/L = 5u/2u)**

<img width="1920" height="1080" alt="Screenshot from 2025-10-13 16-22-09" src="https://github.com/user-attachments/assets/a604fc38-d800-41ab-ae3c-4048bb019cf9" />
<p align="center"> $I_d$ v/s $V_{ds}$ all three modes</p>

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/81a34a2c-a9de-4099-8fad-6d23491ddb2e" />
<p align="center"> $I_d$ v/s $V_{ds}$ linear region</p>

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e0707557-917d-4a8b-9134-652def811045" />
<p align ="center"> $I_d$ v/s $V_{ds}$ cut off mode (current is in nA)</p>

1.  **Cutoff Region:** As shown in the third plot, the current is very low at very low values of $V_{gs}$, which is the expected behavior.  
2.  **Linear / Triode Region:** At low values of $V_{ds}$ (2nd plot), the drain current ($I_d$) increases sharply and almost linearly with $V_{ds}$.
3.  **Saturation Region:** As $V_{ds}$ increases beyond a certain point ($V_{ds}$ > $V_{gs}$ - $V_{t}$ ), the drain current begins to flatten and becomes largely independent of $V_{ds}$.
Here, the transistor acts as a voltage-controlled current source, where the current is primarily determined by $V_{gs}$.
This is the primary operating region for digital switching. Here we can also observe a slight positive slope in the current, which is an expected effect of channel length modulation.

### Tabulated Results

For $V_{ds}$ = 1.72V, the $V_{gs}$ sweep from 0V to 1.8V with 0.2 step.
The table below lists the drain currents corresponding to the $V_{gs}$ voltage sweep.

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

- **What is seen:** The plot clearly shows that the drain current $I_d$ is strongly dependent on the gate voltage $V_{gs}$. Increasing $V_{gs}$ results in a significantly higher saturation current. 
The transition from the linear to the saturation region, known as the saturation voltage ($V_{ds,sat}$), occurs at higher values of $V_{ds}$ for higher $V_{gs}$ values. 
Another important observation is that the curves in the saturation region are not perfectly flat; they exhibit a slight positive slope.

- **Why it happens (Device Physics):** The gate voltage $V_{gs}$ controls the formation of the inversion layer (the channel) under the gate.
A higher $V_{gs}$ attracts more free electrons, creating a more conductive channel and thus allowing more current to flow. 
Saturation begins when $V_{ds} \geq V_{gs} - V_{th}$, at which point the channel near the drain is "pinched off." 
The slight upward slope in the saturation region is due to **Channel Length Modulation (CLM)**. 
As $V_{ds}$ increases past the saturation point, the pinch-off region widens, slightly reducing the effective channel length ($L_{eff}$). 
Since drain current is inversely proportional to channel length, this results in a small increase in $I_d$.

- **How this ties back to STA concepts:** Static Timing Analysis (STA) tools rely on models that abstract this physical behavior. 
The **saturation current ($I_{dsat}$)** is the single most important parameter for determining the switching speed of a logic gate. 
The ability of a transistor to provide high $I_{dsat}$ allows it to charge or discharge a capacitive load quickly, resulting in a lower propagation delay. 
The I-V curves from this experiment are used to build the timing libraries (e.g., Non-Linear Delay Models - NLDM) that STA tools use to accurately predict circuit timing.

### Conclusions

This experiment successfully characterized the fundamental I-V behavior of a sky130 NMOS transistor. 
The results visually confirm the device's transition from a resistor-like (linear) to a current-source-like (saturation) behavior based on the applied terminal voltages. 
This transistor-level characteristic directly constrains circuit-level performance; specifically, the saturation current dictates the maximum speed at which a logic gate can operate. 
Understanding this relationship is the first and most critical step in designing high-performance digital circuits and performing accurate timing analysis. 
Any variations in this I-V behavior due to process, voltage, or temperature (PVT) will directly impact timing margins and the reliability of a digital system.

### References

  * SKY130 Process Design Kit (PDK) for the `sky130_fd_pr__nfet_01v8` transistor model.

-----

## 2. Threshold Voltage Extraction & Velocity Saturation 

### Introduction

The purpose of these experiments is to characterize the fundamental electrical behavior of a short-channel NMOS transistor from the sky130 Process Design Kit (PDK). This characterization is crucial for understanding how modern, scaled-down transistors differ from ideal long-channel devices. Two primary analyses were performed:

1.  **$I_d$ vs. $V_{ds}$ (Output Characteristics)**: This experiment aims to visualize the transistor's operational regions and observe the impact of **velocity saturation** on its current-driving capability.
2.  **$I_d$ vs. $V_{gs}$ (Transfer Characteristics)**: This experiment is done to extract the transistor's **threshold voltage ($V_t$)**, a critical parameter that tells its turn-on point, using a method suitable for velocity-saturated devices (short-channel devices).

### SPICE Netlists

#### Netlist 1: $I_d$ vs. $V_{ds}$ Simulation (for Velocity Saturation Analysis)

This netlist performs a nested DC sweep to generate multiple output curves.

```spice
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description
XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15
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

#### Netlist 2: $I_d$ vs. $V_{gs}$ Simulation (for $V_t$ Extraction)

This netlist sweeps the gate voltage while keeping the drain voltage fixed to ensure the transistor is in saturation.

```spice
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description
XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15
R1 n1 in 55
Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands
.op
.dc Vin 0 1.8 0.1 

.control
run
display
setplot dc1
.endc
.end
```

### Plots & Figures

#### Graph 1: NMOS Output Characteristics ($I_d$ vs. $V_{ds}$) (W/L=0.39/0.15)

The plot shows drain current ($I_d$) versus drain-to-source voltage ($V_{ds}$). Each curve corresponds to a different gate voltage ($V_{gs}$). 

<img width="1920" height="1080" alt="Screenshot from 2025-10-13 21-54-28" src="https://github.com/user-attachments/assets/546a8d9b-8b63-4983-9183-0b7fb33768df" />


#### Graph 2: NMOS Transfer Characteristics ($I_d$ vs. $V_{gs}$)

This plot shows drain current ($I_d$) versus gate-to-source voltage ($V_{gs}$) for $V_{ds} = 1.8V$. The tangent line is extrapolated from the linear portion of the curve. Its intersection with the x-axis provides the **threshold voltage ($V_t$)**.

<img width="1548" height="871" alt="image" src="https://github.com/user-attachments/assets/291fe5d9-71e2-4eda-bf74-fc4269e74e67" />


### Tabulated Results

The primary parameter extracted from these experiments is the transistor's threshold voltage for a short channel NMOS. This method is only possible for the short-channel device as the long channel device will have a quadratic relation ($I_d$ vs. $V_{gs}$). The **threshold voltage ($V_t$)** for the NMOS transistor was extracted using the **Linear Extrapolation Method**. This technique was applied to the $I_d$ vs. $V_{gs}$ characteristic curve by programmatically finding the slope of the curve in its linear region and extrapolating to the x-axis. The entire process was done using the following sequence of `meas` commands. 

First, the drain current at $V_{gs}=1.2V$ was found using:

```ngspice
meas dc id1 find vdd#branch when v(in)=1.2
```

Next, the current at $V_{gs}=1.5V$ was found with:

```ngspice
meas dc id2 find vdd#branch when v(in)=1.5
```

Finally, these two measured values were used to automatically calculate the slope (transconductance, $g_m$) between the points:

```ngspice
meas dc gm_slope param='(id2-id1)/(1.5-1.2)'
```

The resulting slope value was then used in the point-slope line equation to calculate the x-intercept, yielding the final threshold voltage. 

| Parameter | Extracted Value | Method |
| :--- | :--- | :--- |
| **Threshold Voltage ($V_t$)** | **\~0.804 V** | Linear extrapolation from the $I_d$ vs. $V_{gs}$ curve. |

### Observations / Analysis

  * **What you see**:

    1.  From the **Graph 1**, we see the current saturates as expected. However, for a short-channel device, the current is lower than expected. This is due to velocity saturation. Also we can observe from the graph is that as $V_{gs}$ increases, the drain current $I_d$ increases linearly.
    2.  From the $I_d$ vs. $V_{gs}$ plot, we observe that after the transistor turns on, the drain current increases **linearly** with the gate voltage, rather than quadratically.

  * **Why it happens (Device Physics)**:
    Both of these observations are direct consequences of **velocity saturation**. In short-channel transistors, the high electric field ($E \approx V_{ds} / L$) causes electrons to reach a maximum drift velocity ($v_{sat}$). This physical speed limit prevents the current from increasing as rapidly as it would in a long-channel device. This effect changes the fundamental device equations. The equation for the drain current ($I_d$) when the device is in the velocity saturation region is:

    $$I_d = \mu_n C_{ox} \frac{W}{L} \left[ (V_{gs} - V_t) V_{dsat} - \frac{V_{dsat}^2}{2} \right] (1 + \lambda V_{ds})$$

    * **$I_d$**: The **Drain Current** flowing through the transistor.
    * **$\mu_n$**: The **mobility of electrons** in the channel.
    * **$C_{ox}$**: The **gate oxide capacitance** per unit area.
    * **$W/L$**: The **Width-to-Length ratio** of the transistor, a key design parameter.
    * **$(V_{gs} - V_t)$**: The **gate overdrive voltage**, often abbreviated as $V_{gt}$. It's the effective voltage that controls the channel.
    * **$V_{dsat}$**: The specific drain-to-source voltage at which the charge carriers (electrons) reach their maximum speed (saturation velocity). This is a critical parameter in short-channel models.
    * **$\lambda$**: The **Channel Length Modulation** parameter. This accounts for the slight increase in drain current with $V_{ds}$ even after the transistor has saturated.
    * **$V_{ds}$**: The **drain-to-source voltage**.
      
    This linear relationship is precisely what we observe in the $I_d$ vs. $V_{gs}$ plot and is why the **linear extrapolation method** is the standard technique for extracting $V_t$ in modern devices. We are fitting a line to a behavior that is inherently linear due to this physical effect.

  * **How this ties back to STA**:
    **Velocity saturation** is a critical first-order effect that must be included in the timing models used by Static Timing Analysis (STA) tools. If a tool were to use a simple square-law model for a short-channel device, it would overestimate the drive current and incorrectly predict a much smaller gate delay. An accurate $V_t$ value is essential for calculating the gate overdrive ($V_{gs} - V_t$), which determines the saturation current and, consequently, the propagation delay of the logic cell.

### Conclusions

This characterization demonstrates how the physical behavior of modern, short-channel transistors directly impacts circuit performance. The phenomenon of **velocity saturation** fundamentally limits the current-driving capability and changes the device's response to gate voltage from quadratic to linear. This understanding is critical for accurately extracting parameters like the **threshold voltage ($V_t$)** and deriving a delay model for a particular cell.

### References / Citations

  * SKY130 Process Design Kit (PDK) model files for the `sky130_fd_pr__nfet_01v8` transistor model.

-----

## 3. CMOS Inverter: Voltage Transfer Characteristic (VTC) 

### Introduction

The purpose of this experiment is to analyze the **Voltage Transfer Characteristic (VTC)** of a standard CMOS inverter. The VTC plot, which shows the output voltage ($V_{out}$) as a function of the input voltage ($V_{in}$), is the most important characteristic of a digital logic gate. This analysis allows us to determine the inverter's switching behavior, its robustness to noise, and its overall DC performance. The key parameter extracted from this plot is the **switching threshold ($V_m$)**, which is the input voltage at which the output voltage is exactly equal to the input voltage.

### SPICE Netlists

The following SPICE netlist was used to simulate the CMOS inverter and generate its VTC curve. It consists of one PMOS and one NMOS transistor from the sky130 library, connected in the standard inverter configuration. A DC sweep of the input voltage (`Vin`) from 0V to 1.8V is performed to trace the output voltage.

```spice
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15
Cload out 0 50fF
Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands
.op
.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc
.end
```

### Plots & Figures

#### Graph: CMOS Inverter VTC ($V_{out}$ vs. $V_{in}$)

<img width="1489" height="837" alt="image" src="https://github.com/user-attachments/assets/b44a4be1-3c3a-4b5d-9aa2-ab3ec82fe536" />

   The plot above shows the inverter's output voltage as a function of its input voltage. The curve exhibits a very sharp, high-gain transition between the high and low output states. The **switching threshold ($V_M$)** is marked at the point where the VTC curve intersects the line $V_{out} = V_{in}$. This point represents the theoretical switching point of the inverter. The PMOS used is `W=0.84u L=0.15u`, the NMOS used is `W=0.36u L=0.15u` with a capacitive load `50fF`.

### Tabulated Results

The primary DC parameter extracted from the VTC is the switching threshold voltage, $V_m$. The value is found at the intersection of the VTC curve and the line $V_{out} = V_{in}$. In `ngspice`, this can be found precisely using the `meas` command:

```ngspice
meas dc vm find in when v(out)=v(in)
``` 

| Parameter | Extracted Value |
| :--- | :----|
| **Switching Threshold ($V_m$)** | **0.8769191 V** | 

### Observations / Analysis

  * **What you see**: The VTC plot shows a classic inverter characteristic: for low input voltages ($V_{in} \ll V_{DD}/2$), the output is high ($V_{out} \approx V_{DD}$). For high input voltages ($V_{in} \gg V_{DD}/2$), the output is low ($V_{out} \approx 0V$). The transition between these two states is very steep, indicating a high voltage gain in the transition region. The switching threshold ($V_M$) is typically found to be close to $V_{DD}/2$ for a symmetric inverter. As the $$\frac{W_p}{L_p} = \frac{0.84}{0.15} $$ and $$\frac{W_n}{L_n} = \frac{0.36}{0.15}$$ which makes the resistance of the NMOS equal to that of the PMOS. As a result, we can observe that the VTC graph is almost symmetric. 

  * **Why it happens (Device Physics)**: The behavior is governed by which transistor is "on" and which is "off."

      * **When $V_{in}$ is low (0V)**: The NMOS gate-source voltage is 0V, so it is in **cutoff**. The PMOS gate-source voltage is $-V_{DD}$, so it is strongly **on**, pulling the output node `vout` up to $V_{DD}$.
      * **When $V_{in}$ is high ($V_{DD}$)**: The NMOS gate-source voltage is $V_{DD}$, so it is strongly **on**, pulling the output node `vout` down to ground. The PMOS gate-source voltage is 0V, so it is in **cutoff**.
      * **During the transition (around $V_{in} \approx V_m$)**: Both the NMOS and PMOS transistors are simultaneously in their **saturation regions**, creating a direct path between $V_{DD}$ and ground. This causes a short-circuit current to flow and results in the high voltage gain (steep slope) of the characteristic.

  * **How this ties back to STA**: The VTC is the foundation of digital logic levels. The switching threshold ($V_M$) and the steepness of the transition region define the **noise margins** of the gate. A well-defined $V_m$ and high noise margins ensure that the gate is robust against voltage fluctuations on its input. In Static Timing Analysis (STA), the input logic thresholds ($V_{IL}$, $V_{IH}$) used in timing libraries are directly derived from this VTC curve. These thresholds define the valid "0" and "1" logic levels and are critical for determining if a signal transition is valid.

### Conclusions

The VTC experiment successfully characterized the DC switching behavior of the CMOS inverter, confirming its near-ideal properties as a logic gate. The sharp transition and well-defined output levels demonstrate its effectiveness in restoring signal integrity. The extracted switching threshold ($V_M$) is a fundamental parameter that not only defines the inverter's switching point but also serves as a critical input for creating the timing and noise models used in the design and verification of digital circuits. Any variation in this VTC due to process or environmental changes will directly impact the circuit's noise immunity and functional reliability.

### References / Citations

  * SKY130 Process Design Kit (PDK) model files for the `sky130_fd_pr__nfet_01v8` and `sky130_fd_pr__pfet_01v8` transistor models.

-----

## **4. Transient Behavior: Rise / Fall Delays**

### Introduction 

The purpose of this experiment is to analyze the **transient (time-domain) behavior** of the CMOS inverter. While the VTC describes the inverter's DC characteristics, a transient analysis is essential for understanding its dynamic performance—specifically, its speed. By applying a time-varying pulse to the input, we can measure the **propagation delay**, which is the time it takes for the output to respond to a change at the input. This delay is the most fundamental timing metric for a digital logic gate and ultimately determines the maximum operating frequency of a digital circuit.

### SPICE Netlists

The following SPICE netlist was used to simulate the transient response of the inverter. A pulse voltage source is applied to the input, and a small load capacitor is added to the output to model the input capacitance of the next logic gate in a chain, which is crucial for a realistic delay measurement.

```spice
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15

Cload out 0 50fF
Vdd vdd 0 1.8V
Vin in 0 PULSE(0V 1.8V 0 0.1ns 0.1ns 2ns 4ns)

*simulation commands
.tran 1n 10n

.control
run
.endc
.end
```

### Plots & Figures

#### Graph: Inverter Transient Response

<img width="1476" height="828" alt="image" src="https://github.com/user-attachments/assets/104a9072-6f0a-4b44-b862-88f7ea057edf" />

The plot above shows the input voltage waveform (`in`) and the corresponding output voltage waveform (`out`) over time. As expected, the output is an inverted and delayed version of the input. The propagation delays, **$t_{p,rise}$** and **$t_{p,fall}$**, are measured between the 50% transition points of the input and output signals.

### Tabulated Results

The key performance metrics extracted from the transient simulation are the rise and fall propagation delays.

| Parameter | Extracted Value | Method & Commands Used |
| :--- | :--- | :--- |
| **Fall Propagation Delay ($t_{p,fall}$)** | **288.2233 ps** | Measured from the point where the rising input signal crosses 50% of $V_{DD}$ to the point where the falling output signal crosses 50% of $V_{DD}$.<br><br>`meas tran tp_fall TRIG v(in) VAL='0.9' RISE=1 TARG v(out) VAL='0.9' FALL=1` |
| **Rise Propagation Delay ($t_{p,rise}$)** | **332.5613 ps** | Measured from the point where the falling input signal crosses 50% of $V_{DD}$ to the point where the rising output signal crosses 50% of $V_{DD}$.<br><br>`meas tran tp_rise TRIG v(in) VAL='0.9' FALL=1 TARG v(out) VAL='0.9' RISE=1` |

### Observations / Analysis

  * **What you see**: The output signal is having a rise and fall delay, but the functionality of the inverter is still the same. The output starts to fall only after the input has risen, and it starts to rise only after the input has fallen. The time difference between the input and output crossing their 50% voltage levels is the propagation delay.

  * **Why it happens (Device Physics)**: The delay is caused by the finite time it takes for the transistors to charge or discharge the total capacitance at the output node ($C_L$ plus parasitic capacitances).

      * **Fall Delay ($t_{p,fall}$)**: When the input goes high, the NMOS transistor turns on and provides a finite saturation current ($I_{dsat,N}$) to discharge the output capacitance to ground.
      * **Rise Delay ($t_{p,rise}$)**: When the input goes low, the PMOS transistor turns on and provides a saturation current ($I_{dsat,P}$) to charge the output capacitance up to $V_{DD}$.
        The fundamental relationship is $\Delta t \approx C_L \frac{\Delta V}{I}$. A larger load capacitance or a weaker transistor (lower drive current) will result in a longer delay. When the transistor is ON, it acts as a resistor and thus there is RC delay. 

  * **How this ties back to STA**: **Propagation delay is the single most important metric in Static Timing Analysis (STA)**. The timing models (`.lib` files) used by STA tools are essentially complex lookup tables that contain pre-characterized delay values for each logic gate under various input transition times and output load capacitances. An STA tool calculates the total delay of a logic path by summing the propagation delays of all the gates along that path. The longest path, known as the **critical path**, determines the minimum clock period and thus the maximum operating frequency of the entire chip. This propagation delay is determined by the SPICE simulations which is then fed to the LUTs.

### Conclusions

The transient analysis successfully quantified the dynamic performance of the CMOS inverter by measuring its rise and fall propagation delays. This experiment demonstrates the direct link between transistor-level physics (drive current) and circuit-level parameters (capacitance, voltage) in determining the speed of a digital logic gate. The performance of a complex microprocessor, with billions of transistors, is ultimately constrained by the sum of these delays along its critical paths. Accurately characterizing and modeling these delays is therefore essential for the design and timing verification of digital circuits.

### References / Citations

  * SKY130 Process Design Kit (PDK) model files for the `sky130_fd_pr__nfet_01v8` and `sky130_fd_pr__pfet_01v8` transistor models.

-----

## **5. Noise Margin Analysis**

### Introduction 

The purpose of this experiment is to perform a **Noise Margin Analysis** on the CMOS inverter. This analysis quantifies the inverter's robustness to signal noise. By generating the Voltage Transfer Characteristic (VTC), we can determine the critical input thresholds ($V_{IL}$ and $V_{IH}$) where the gate has a gain of -1. From these points, we define the corresponding output voltage levels ($V_{OH}$ and $V_{OL}$) to calculate the **Noise Margin High ($NM_H$)** and **Noise Margin Low ($NM_L$)**. These margins represent the amount of noise the gate's input can tolerate before the output logic level is compromised. 

### SPICE Netlists

The following SPICE netlist was used to generate the VTC curve for an inverter with a PMOS width (`Wp`), making it almost asymmetric. A DC sweep of the input voltage is performed to trace the output.

```spice
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15

Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands
.op
.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc
.end
```

### Plots & Figures

#### Graph: VTC with Noise Margin Annotations

<img width="1608" height="904" alt="image" src="https://github.com/user-attachments/assets/768f979f-5150-47bc-b33a-c383fa592c9d" />

The plot above shows the inverter's VTC. The key points for noise margin analysis are annotated:

  * **$V_{IL}$ (Input Low Voltage)**: The input voltage where the VTC slope (gain) first becomes -1.
  * **$V_{IH}$ (Input High Voltage)**: The second input voltage where the slope becomes -1.
  * **$V_{OH}$ (Output High Voltage)**: Defined as the output voltage when the input is exactly $V_{IL}$.
  * **$V_{OL}$ (Output Low Voltage)**: Defined as the output voltage when the input is exactly $V_{IH}$.

### Tabulated Results

The key parameters extracted from the VTC are the logic level voltages and the resulting noise margins.

| Parameter | Extracted Value | 
| :--- | :--- |
| **Input Low Voltage ($V_{IL}$)** | **0.757798 V** | 
| **Input High Voltage ($V_{IH}$)** | **0.987156 V** |
| **Output High Voltage ($V_{OH}$)** | **1.73111 V** |
| **Output Low Voltage ($V_{OL}$)** | **0.0955556 V** |
| **Noise Margin Low ($NM_L$)** | **0.6622424 V** | 
| **Noise Margin High ($NM_H$)** | **0.743954 V** | 

### Observations / Analysis

  * **What you see**: We can identify that there are two distinct points on the curve where the slope is exactly -1, defining the input thresholds $V_{IL}$ and $V_{IH}$. The corresponding output voltages at these input points are defined as $V_{OH}$ and $V_{OL}$, respectively. These four points mark the boundaries of the valid operating regions for the inverter.

  * **Why it happens (Device Physics)**: The region between $V_{IL}$ and $V_{IH}$ is the high-gain transition region where both NMOS and PMOS transistors are in saturation. This high gain is what allows the inverter to amplify and restore signals. The points where the gain is exactly -1 represent the "edges" of this region, beyond which the gate's gain drops below unity. This property is what allows the inverter to reject noise; a small fluctuation on the input will be attenuated at the output, as long as the input stays within the valid logic '0' or '1' ranges. By changing the Wp/Wn ratio, we alter the relative strengths of the pull-up and pull-down networks, which shifts the switching threshold ($V_M$) and changes the values of $V_{IL}$ and $V_{IH}$, thereby affecting the noise margins.

  * **How this ties back to STA**: Noise margins are fundamental to the **functional reliability** of a circuit. While STA primarily focuses on timing, its results are only valid if the signals are clean. In modern designs, effects like **crosstalk** (capacitive coupling between adjacent wires) can induce noise that eats into these margins. If a noise glitch is large enough to cross the $V_{IL}$ or $V_{IH}$ thresholds, it can cause a gate to switch incorrectly, leading to a logic failure. The timing libraries used by STA tools contain **noise models** that are derived from these VTC characteristics to help designers analyze and prevent such signal integrity issues.

### Conclusions

This analysis successfully quantified the inverter's noise margins, a critical measure of its operational robustness. The experiment demonstrates that the inverter's DC transfer characteristic defines not just its switching point, but also its ability to tolerate voltage noise on its inputs. The calculated $NM_L$ and $NM_H$ values provide a clear specification for how much the '0' and '1' levels can be corrupted before a logic error occurs. This analysis highlights a key design trade-off: transistor sizing not only affects the speed of a gate (transient behavior) but also its noise immunity (DC behavior), and both must be considered to create a reliable and high-performance digital system.

### References / Citations

  * SKY130 Process Design Kit (PDK) model files for the `sky130_fd_pr__nfet_01v8` and `sky130_fd_pr__pfet_01v8` transistor models.
    
-----

## **6. Power-Supply and Device Variation Studies**

### Introduction / Background

The purpose of this experiment is to study the robustness of a CMOS inverter by analyzing its response to common real-world variations. In any practical integrated circuit, performance is affected by fluctuations in the operating environment and inconsistencies in manufacturing. This analysis focuses on two key aspects:

1.  **Power-Supply Variation**: The supply voltage ($V_{DD}$) across a chip is not perfectly constant due to effects like IR drop and power grid noise. We will analyze the inverter's Voltage Transfer Characteristic (VTC) at a significantly lower supply voltage to see how its functionality and gain are affected.

2.  **Device Variation**: Manufacturing processes are not perfect, leading to variations in transistor dimensions. We will analyze how changing the width of the PMOS transistor (Process Variation) affects the inverter's switching threshold ($V_M$) and its overall symmetry.

These studies are critical for understanding how a simple logic gate maintains its functionality under non-ideal conditions, a property known as robustness.

### SPICE Netlists / Code

#### Netlist 1: Power-Supply Variation Analysis

This netlist uses the `.step` command to simulate the inverter's VTC at two different supply voltages: the nominal 1.8V and a reduced 0.8V.

```spice
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15

Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

.control

let powersupply = 1.8
alter Vdd = powersupply
	let voltagesupplyvariation = 0
	dowhile voltagesupplyvariation < 6
	dc Vin 0 1.8 0.01
	let powersupply = powersupply - 0.2
	alter Vdd = powersupply
	let voltagesupplyvariation = voltagesupplyvariation + 1
      end

plot dc1.out vs in dc2.out vs in dc3.out vs in dc4.out vs in dc5.out vs in dc6.out vs in xlabel "input voltage(V)" ylabel "output voltage(V)" title "Inveter dc characteristics as a function of supply voltage"

.endc
.end
```

#### Netlist 2: Device Variation Analysis

This netlist uses the `.param` and `.step` commands to simulate the VTC for several different PMOS widths, demonstrating the effect of process variation on the switching threshold.

```spice
*Model Description
.param temp=27

*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

*Netlist Description
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=7 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.42 l=0.15

Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands
.op
.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc
.end
```

### Plots & Figures

#### Graph 1: VTC under Supply Variation

The plot shows the VTC for $V_{DD}=1.8V$ to $V_{DD}=0.8V$. While the overall shape is preserved, the curve for the lower supply voltage has a noticeably steeper transition region, indicating higher voltage gain. The gain plot shows that there is increase in the gain as the supply voltage decreases.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/59094f8c-2c18-455e-964e-eae66bc9f865" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f9bdb536-5de5-4355-b192-8d8662adc2e1" />


#### Graph 2: VTC under Device Variation

This plot shows a VTC curve for PMOS width (Wp = 7). As the PMOS width increased drastically compared to the previous analysis, the switching threshold ($V_M$) clearly shifts to the right (a higher voltage) but the increase is very small when compared to the increase in width.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e1a5acb4-f265-4996-9f6f-6b9490108b3c" />


### Tabulated Results

The effects of the variations on key inverter parameters are summarized below. To find the peak gain for each simulation run, we can use the `setplot` command to select the specific dataset, followed by the `meas` command with the `min` function to find the minimum value of your pre-calculated gain vector. Since gain is a negative value, the peak gain corresponds to the minimum value on the plot.

The process of extracting the peak gain for each stepped simulation involves a sequence of commands:
1.  **`setplot <plot_name>`**: This command selects the active dataset. Since the `.step` analysis creates multiple runs (`dc1`, `dc2`, etc.), you must use `setplot` to focus on one result set at a time.
2.  **`let gain_vector = deriv(v(out)) / deriv(v(in))`**: This command calculates the gain at every point of the VTC curve and stores it in a new vector. This must be done for each plot context.
3.  **`meas dc <result_name> min <gain_vector>`**: This is the measurement command. It finds the minimum value (the most negative gain) within the specified `gain_vector` for the active DC analysis and prints it to the console.

| VDD | Peak Gain | Input Voltage at Peak Gain |
| :--- | :--- | :------------ |
| 1.8 V | **-11.51** | 0.86 V |
| 1.6 V | **-11.99** | 0.77 V |
| 1.4 V | **-12.38** | 0.67 V |
| 1.2 V | **-13.10** | 0.59 V |
| 1.0 V | **-13.39** | 0.52 V |
| 0.8 V | **-12.75** | 0.45 V |

As the data clearly shows, the magnitude of the inverter's peak gain generally increases as the supply voltage is lowered, confirming the trend observed in the plots. 

| Parameter | Value | 
| :--- | :--- | 
| **Switching threshold $V_m$**| **0.9882076 V** |

### Observations / Analysis

  * **What you see**:

    1.  **Supply Variation**:
       * Lowering $V_{DD}$ from 1.8V to 0.8V scales the VTC down. The output still swings rail-to-rail, and the fundamental inverting functionality is perfectly preserved. Interestingly, the transition region becomes much steeper, indicating a higher voltage gain.
       * As the supply voltage drops below 1 V, we can observe that the peak gain decreases.
       * Another observation from the gain plot is that the **width of the negative gain pulse is wider for higher supply voltages** ($V_{DD}$) and becomes progressively **narrower and sharper for lower supply voltages**. 
    2.  **Device Variation**: Increasing the width of the PMOS transistor makes the pull-up network stronger than the pull-down network. This shifts the switching threshold ($V_M$) from below $V_{DD}/2$ to above $V_{DD}/2$. Despite this shift, the gate continues to function correctly as an inverter.

  * **Why it happens (Device Physics)**:

    1.  **Supply Variation**:
       * The CMOS inverter's rail-to-rail output is a structural property; as long as one transistor is fully on and the other is fully off, the output will be pulled to the supply rails, whatever their voltage. The increased gain at lower voltages occurs because the transistors operate with a smaller gate overdrive ($V_{gs} - V_t$), which pushes them deeper into saturation relative to the supply range, sharpening the transition.
       * The decrease in gain below the supply voltage of `1 V` happens because the supply voltage is unable to drive the transistors thus leading to a decrease in gain.
       * The reason the gain pulse width increases with the supply voltage is that the width is directly proportional to the **width of the inverter's high-gain transition region**, which is the range of input voltages where both the NMOS and PMOS transistors are simultaneously operating in their **saturation regions**.
    2.  **Device Variation**: The switching threshold ($V_M$) is the input voltage where the pull-up (PMOS) and pull-down (NMOS) currents are equal. By making the PMOS wider, it can supply more current. To match this higher current, the NMOS requires a stronger on signal, which means a higher input voltage. This directly causes the switching point ($V_M$) to increase.

  * **How this ties back to STA**: These variations are at the heart of Static Timing Analysis.

    1.  **Voltage Variation**: Supply voltage is one of the three main factors in PVT (Process, Voltage, Temperature) corners. A drop in $V_{DD}$ (due to IR drop) drastically reduces the transistor drive current, which **significantly increases gate propagation delay**. STA must verify that timing constraints are met even at the lowest possible supply voltage.
    2.  **Process Variation**: Random, unpredictable variations in transistor width, length, and threshold voltage during manufacturing are a primary concern. An STA tool analyzes the circuit across different process corners (e.g., fast NMOS/slow PMOS, slow NMOS/fast PMOS) to ensure that variations in switching thresholds and drive strengths do not cause timing failures or race conditions. The robustness of the CMOS structure ensures functionality, but STA is required to guarantee performance.

### Conclusions

These experiments demonstrate the exceptional **robustness** of the CMOS inverter topology. It maintains its logical functionality across significant variations in both power supply and device dimensions.

However, this robustness in *function* does not imply robustness in *performance*. The analysis clearly shows that these variations directly impact key characteristics like voltage gain and switching threshold, which in turn have a first-order effect on circuit timing and noise margins. This is precisely why designers rely on extensive STA across multiple PVT corners—to ensure that a circuit that is functionally correct will also meet its performance targets under all expected real-world conditions.

### References / Citations

  * SKY130 Process Design Kit (PDK) model files for the `sky130_fd_pr__nfet_01v8` and `sky130_fd_pr__pfet_01v8` transistor models.
