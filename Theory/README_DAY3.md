# CMOS Switching threshold and dynamic simulations

This document provides an overview of the CMOS inverter, covering its static and dynamic behavior. We will explore the theoretical concepts behind the Voltage Transfer Characteristic (VTC), the critical role of the **switching threshold ($V_M$)**, the impact of **velocity saturation** in modern devices, and conclude with practical SPICE simulations using the Sky130 PDK.

-----

## 1. The Voltage Transfer Characteristic (VTC)

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

### 💡 2. The Switching Threshold ($V_M$)

The **Switching Threshold ($V_M$)** is the most critical parameter of the VTC. It represents the exact center of the inverter's switching activity.

  * **Definition**: $V_M$ is the input voltage ($V_{in}$) where the output voltage is precisely equal to the input voltage ($V_{out} = V_{in}$).

  * **Physical State**: At this unique point, both the PMOS and NMOS transistors are simultaneously active in the **saturation region**. This leads to a crucial consequence: the magnitude of the current flowing through the PMOS transistor becomes equal to the current flowing through the NMOS transistor.
    $$|I_{ds,p}| = I_{ds,n} \quad (\text{at } V_{in} = V_{out} = V_M)$$
    This current balance is the underlying condition that defines the switching threshold.

#### The Role of Transistor Sizing

The value of $V_M$ is determined by the relative electrical strengths of the PMOS and NMOS transistors, which is controlled by their **Width-to-Length ($W/L$) ratios**. To create a symmetric inverter with $V_M \approx V_{DD}/2$, the PMOS transistor must be made physically wider than the NMOS to compensate for the lower mobility of holes compared to electrons ($\mu_n > \mu_p$).

  * **Increasing PMOS Width ($W_p$)**: Shifts $V_M$ higher (towards $V_{DD}$).
  * **Increasing NMOS Width ($W_n$)**: Shifts $V_M$ lower (towards Ground).

-----

### ⚡️ 3. Advanced Topic: Velocity Saturation

In modern, **short-channel transistors**, the classic square-law I-V model is inaccurate. At high electric fields (common in small devices), the velocity of charge carriers (electrons and holes) no longer increases with the field strength and instead saturates at a maximum velocity ($v_{sat}$).

  * **Impact**: This **velocity saturation** changes the transistor's current equation, making it more linear with respect to the gate voltage.
  * **Effect on $V_M$**: The formula for calculating $V_M$ is modified. However, the fundamental principle remains the same: $V_M$ is set by the ratio `r` of the PMOS to NMOS drive strengths. To achieve a symmetric inverter ($V_M \approx V_{DD}/2$), we still need to balance the transistors by adjusting their widths, aiming for `r` ≈ 1.

-----

### ⏱️ 4. Dynamic Behavior: Transient Analysis

Transient analysis reveals how an inverter behaves over time, which is essential for determining its speed.

  * **Purpose**: To measure key timing metrics like **propagation delay**, **rise time**, and **fall time**.
  * **SPICE Command**: The `.tran` command is used, with a time-varying input like a `PULSE` voltage source.
  * **Propagation Delay ($t_p$)**: The time it takes for the output to respond to an input change, typically measured between the 50% transition points of the input and output waveforms.

#### Application in Clock Networks

In clock distribution networks, it is crucial to have **balanced rise and fall delays**. If an inverter or buffer is improperly sized (e.g., a weak PMOS), its rise time will be much slower than its fall time. This leads to **duty cycle distortion**, where a 50% duty cycle clock signal becomes skewed, potentially causing timing violations in a digital system. Symmetric inverter design is the first line of defense against this issue.

