Of course\! Based on the updated information you provided, here is a revised and consolidated document. It integrates the theory, the concept of velocity saturation, and the practical lab examples into a cohesive guide.

-----

## CMOS Inverter Analysis: Theory, Simulation, and Sizing

This guide provides a comprehensive overview of the CMOS inverter, covering its static and dynamic behavior. We will explore the theoretical concepts behind the Voltage Transfer Characteristic (VTC), the critical role of the **switching threshold ($V_M$)**, the impact of **velocity saturation** in modern devices, and conclude with practical SPICE simulations using the Sky130 PDK.

-----

### 🔬 1. Static Behavior: The Voltage Transfer Characteristic (VTC)

The **Voltage Transfer Characteristic (VTC)** is a fundamental plot that defines the static behavior of an inverter. It shows the steady-state output voltage ($V_{out}$) for every possible input voltage ($V_{in}$), revealing the inverter's switching sharpness, noise margins, and overall robustness.

To generate a VTC in SPICE, we perform a **DC sweep** (`.dc` command), which slowly varies the input voltage from $0$ to $V_{DD}$ and records the output.

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

-----

### 💻 5. Lab Example: Simulation with Sky130 PDK

Here are practical SPICE netlists for simulating a CMOS inverter using the open-source Sky130 Process Design Kit.

#### VTC (DC Sweep) Simulation

This netlist performs a DC sweep on `Vin` to generate the VTC curve.

```spice
* Sky130 Inverter for VTC Analysis
.lib "sky130_fd_pr/models/sky130.lib.spice" tt
.param temp=27

* --- Netlist ---
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0   sky130_fd_pr__nfet_01v8 w=0.36 l=0.15
Cload out 0 50fF
Vdd   vdd 0 1.8V
Vin   in  0 1.8V

* --- Simulation Command ---
.dc Vin 0 1.8 0.01

.control
run
plot v(out) vs v(in)
.endc
.end
```

#### Transient Analysis

This netlist simulates the inverter's response to an input pulse to measure timing.

```spice
* Sky130 Inverter for Transient Analysis
.lib "sky130_fd_pr/models/sky130.lib.spice" tt
.param temp=27

* --- Netlist ---
XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0   sky130_fd_pr__nfet_01v8 w=0.36 l=0.15
Cload out 0 50fF
Vdd   vdd 0 1.8V
Vin   in  0 PULSE(0V 1.8V 0 0.1ns 0.1ns 2ns 4ns)

* --- Simulation Command ---
.tran 1n 10n

.control
run
plot v(out) v(in)
.endc
.end
```
