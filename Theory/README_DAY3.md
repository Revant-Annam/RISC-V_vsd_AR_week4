# CMOS Switching threshold and dynamic simulations

This document provides an overview of the CMOS inverter, covering its static and dynamic behavior. We will explore the theoretical concepts behind the Voltage Transfer Characteristic (VTC), the critical role of the **switching threshold ($V_M$)**, the impact of **velocity saturation** in modern devices, and conclude with practical SPICE simulations using the Sky130 PDK.

-----

## The Voltage Transfer Characteristic (VTC)

The **Voltage Transfer Characteristic (VTC)** is a fundamental plot that defines the static behavior of an inverter. It shows the steady-state output voltage ($V_{out}$) for every possible input voltage ($V_{in}$), revealing the inverter's switching sharpness, noise margins, and overall robustness.

### CMOS Inverter SPICE Deck

<img width="1297" height="612" alt="image" src="https://github.com/user-attachments/assets/abb1c12b-ae03-46c1-b423-63d232c35cdf" />

A SPICE deck is a text file that describes a circuit for simulation. A SPICE Deck contains four essential parts:

- Component Connectivity: Define how PMOS (M1), NMOS (M2), power supply (Vdd), ground (Vss), input (Vin), and output (Vout) are connected.
- Component Values: Specify transistor dimensions (W/L), supply voltages (e.g., 2.5V), and load capacitance (Cload = 10fF).
- Identify Nodes: Recognize each node in the circuit — such as in, out, vdd, vss, and transistor terminals.
- Name Nodes: Assign clear names to all nodes for ease of reference during simulation and result interpretation.

**CMOS Inverter SPICE Deck Explained:**

* `M1 out in vdd vdd pmos W=0.375u L=0.25u`: This line defines the **PMOS transistor**, named `M1`. It's connected between the `out` node and the `vdd` node, with its gate connected to `in`. It has a specified width (W) and length (L).
* `M2 out in 0 0 nmos W=0.375u L=0.25u`: This defines the **NMOS transistor**, `M2`. It's connected between the `out` node and ground (`0`), with its gate also connected to `in`.
* `cload out 0 10f`: This creates a **capacitor**, `cload`, with a value of 10 femtofarads (10fF). It's connected between the `out` node and ground, representing the load the inverter has to drive.
* `Vdd vdd 0 2.5`: This defines the **power supply**, named `Vdd`. It creates a 2.5 Volt DC source between the `vdd` node and ground (`0`).
* `Vin in 0 2.5`: This defines the **input voltage source**, `Vin`, connected between the `in` node and ground. The initial value is 2.5V, but this source will be varied by the `.dc` command.
* `.op`: This command tells SPICE to perform an **Operating Point** analysis, calculating the circuit's DC voltages and currents in its initial state.
* `.dc Vin 0 2.5 0.05`: This command initiates a **DC Sweep**. It instructs the simulator to vary the `Vin` source from **0V** to **2.5V** in steps of **0.05V**, calculating the circuit's behavior at each point. This is used to generate the VTC curve.
* `.include tsmc_025um_model.mod` / `.LIB "tsmc_025um_model.mod" CMOS_MODELS`: Both commands link the simulation to a **model file**. This file contains the detailed electrical parameters (like threshold voltage) for the `pmos` and `nmos` transistors based on a specific manufacturing process (TSMC 0.25µm). Without this, the simulator wouldn't know how the transistors behave.
* `.end`: This command marks the **end of the SPICE deck**. The simulator ignores anything written after this line.

-----

## Static behavior evaluation — CMOS inverter robustness — Switching threshold voltage

The characteristics that define the CMOS inverter robustness are:
- Switching Threshold Voltage (Vm)
- Noise Margin
- Power Supply Variation
- Device Variations

### Switching Threshold Voltage of CMOS Inverter (Vm):

The **Switching Threshold Voltage**, denoted as **$V_m$**, is defined as the specific input voltage ($V_{in}$) where it is identical to the output voltage ($V_{out}$). This parameter is critical because it directly influences the inverter's noise margin and overall robustness.

At the point where $V_{in} = V_{out} = V_m$, both the NMOS and PMOS transistors are simultaneously active and operating in their **saturation regions**. This state results in the highest voltage gain for the inverter.

The provided image illustrates a comparison between two CMOS inverters that have different sizing ratios for their PMOS and NMOS transistors:

<img width="1476" height="705" alt="image" src="https://github.com/user-attachments/assets/5f301680-4a57-4d81-a812-be9b7ef0fc1a" />


* **Left Graph:** Here, the PMOS and NMOS transistors have identical dimensions (W = 0.375 μm, L = 0.25 μm), leading to equal aspect ratios of $(W/L)_n = (W/L)_p = 1.5$. This configuration yields a switching threshold of approximately **$V_m \approx 0.98$ V**.
* **Right Graph:** In this case, the PMOS is wider than the NMOS (Wp = 0.9375 μm vs. Wn = 0.375 μm), resulting in different aspect ratios: $(W/L)_p = 3.75$ and $(W/L)_n = 1.5$. This sizing results in a higher switching threshold of about **$V_m \approx 1.2$ V**.

### Regions of operation:

The inverter's voltage transfer curve is divided into distinct sections, each corresponding to different operating states for the transistors:

1.  PMOS in Linear / NMOS in Cutoff (OFF)
2.  PMOS in Linear / NMOS in Saturation
3.  **PMOS in Saturation / NMOS in Saturation** (The region where the switching threshold, **$V_m$**, occurs.)
4.  PMOS in Saturation / NMOS in Linear
5.  PMOS in Cutoff (OFF) / NMOS in Linear

<img width="1395" height="715" alt="image" src="https://github.com/user-attachments/assets/13167cbd-7074-48c4-9142-155f8acb84a5" />


### Current balance at Vm:

At the switching threshold ($V_m$), a state of current equilibrium is reached where the condition **$I_{dsp} = -I_{dsn}$** holds true. This means the magnitude of the current flowing through the PMOS transistor is equal to the current flowing through the NMOS transistor.

When evaluating this at $V_m$, the gate-source voltage for the NMOS is $V_{gs} = V_m$, while for the PMOS it is $V_{gs} = V_m - V_{dd}$.

<img width="1461" height="718" alt="image" src="https://github.com/user-attachments/assets/ef2876ec-f284-49de-9dbe-a2f0a303cc32" />


By setting the sum of the currents to zero ($I_{dsp} + I_{dsn} = 0$) and solving the resulting equation, one can derive an expression for $V_m$ that depends on the transistor sizing ratios and mobility parameters. This relationship allows designers to calculate the necessary aspect ratio between the PMOS and NMOS transistors, $(W_p/L_p) / (W_n/L_n)$, to achieve a desired switching threshold ($V_m$) by ensuring the currents are balanced precisely at that point.

<img width="884" height="310" alt="image" src="https://github.com/user-attachments/assets/65311500-ec8c-4af3-a644-0101629b848c" />


The accompanying table demonstrates the impact of adjusting the **$W_p/W_n$ ratio** on three key performance metrics:

<img width="1403" height="719" alt="image" src="https://github.com/user-attachments/assets/32d1fb69-72dc-40de-9e5b-6bdc3f6fac12" />


* ✅ **Rise Delay**
* ✅ **Fall Delay**
* ✅ **Switching Threshold Voltage ($V_m$)**

It is observed that when the PMOS aspect ratio is approximately double the NMOS aspect ratio ($W_p/L_p \approx 2 \times W_n/L_n$), the inverter exhibits **symmetrical rise and fall delays**, each around 80 ps. This balanced condition corresponds to a switching threshold of **$V_m \approx 1.2$ V**.

When the rise and fall delays of a clock buffer are closely matched, there is no need to correct for **duty cycle distortion**. However, if an imbalance exists, typically due to a mismatch in the ON-resistance ($R_{on}$) of the PMOS and NMOS transistors, specialized **duty cycle correction** circuits are implemented within the clock distribution network (clock tree) to restore the signal to a perfect 50% duty cycle.

<img width="1289" height="708" alt="image" src="https://github.com/user-attachments/assets/a7bf1927-1ce2-44a5-8dfc-c25caa9f816e" />
