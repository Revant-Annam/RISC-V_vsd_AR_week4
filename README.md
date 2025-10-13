# **Week 4 Day 1: CMOS Circuit Design & Analysis (sky130)**

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

---

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

#### **The Body Effect**

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

**Fermi Potential ($\phi_f$)**

The Fermi Potential is the potential difference between the intrinsic Fermi level and the Fermi level of the doped semiconductor. It indicates how strongly the substrate is doped.

$$\phi_f = \phi_t * \ln\left(\frac{N_A}{n_i}\right)$$

Where:
* **$\phi_t$** is the **thermal voltage** which is approximately 26 mV at room temperature.
* **$N_A$** is the **acceptor doping concentration** of the p-type substrate (atoms/cm³).
* **$n_i$** is the **intrinsic carrier concentration** of silicon, which is a material constant.

**Body Effect Coefficient ($\gamma$)**

The Body Effect Coefficient quantifies how sensitive the transistor's threshold voltage is to a source-to-body voltage ($V_{SB}$).

$$\gamma = \frac{\sqrt{2q\epsilon_{si}N_A}}{C_{ox}}$$

Where:
* **$q$** is the **elementary charge**.
* **$\epsilon_{si}$** is the **permittivity of silicon**.
* **$N_A$** is the **acceptor doping concentration** of the substrate.
* **$C_{ox}$** is the **gate oxide capacitance per unit area**. This value itself is calculated as $C_{ox} = \frac{\epsilon_{ox}}{t_{ox}}$, where $\epsilon_{ox}$ is the permittivity of the silicon dioxide and $t_{ox}$ is the physical thickness of the gate oxide layer.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e473e161-f84e-4445-a8a6-4edcb12b63ba" />

### **NMOS Linear Mode of operation**

When an NMOS transistor has a gate voltage well above its threshold ($V_{GS} > V_t$) and the drain voltage is still relatively low ($V_{DS} < V_{GS} - V_t$), it operates in the **linear (or triode) region**. In this mode, a continuous, strong inversion channel exists from the source all the way to the drain, acting like a conductive path.

The channel isn't uniform. The voltage along the channel, $V(x)$, gradually increases from $0V$ at the source to $V_{DS}$ at the drain. This voltage gradient creates a horizontal **electric field ($E$)** along the channel.

This electric field is what drives the current. The free electrons in the n-type channel are pushed by this field, drifting from the source (lower potential) to the drain (higher potential). This movement of charge is called **drift current**. The strength of this current depends on how many charge carriers are available (controlled by $V_{GS}$) and how strong the electric field is (controlled by $V_{DS}$).

<img width="1465" height="730" alt="image" src="https://github.com/user-attachments/assets/a595902f-cb15-4e63-b68b-496fe23ab8f0" />


#### **Derivation of the Drain Current ($I_D$) Equation**

<img width="1348" height="721" alt="image" src="https://github.com/user-attachments/assets/e8f4eee4-f8ac-41be-a4c1-5ed339886726" />


The derivation starts from the fundamental drift current equation and integrates it along the length of the channel.

1.  **Start with Drift Current:** The basic formula for drift current is:

    $$I_D = -W \cdot Q_i(x) \cdot v(x)$$

    * **$I_D$** is the drain current.
    * **$W$** is the width of the channel.
    * **$Q_i(x)$** is the charge density (charge per unit area) in the channel at a point $x$.
    * **$v(x)$** is the drift velocity of the electrons at point $x$. The negative sign indicates electrons move opposite to the current flow.

3.  **Define Charge and Velocity:**
    * The **charge density** $Q_i(x)$ depends on the voltage difference between the gate and the channel at that point, $V_{GC}(x) = V_{GS} - V(x)$.

      $$Q_i(x) = C_{ox} \cdot (V_{GS} - V(x) - V_t)$$
    
    * The **electron velocity** $v(x)$ is the product of electron mobility ($\mu_n$) and the electric field $E(x)$. The electric field is the rate of change of voltage with distance, $E(x) = -\frac{dV(x)}{dx}$.

      $$v(x) = -\mu_n E(x) = \mu_n \frac{dV(x)}{dx}$$

4.  Substituting the expressions for charge and velocity back into the current equation.

    $$I_D = W \cdot \left[ C_{ox} (V_{GS} - V(x) - V_t) \right] \cdot \left( \mu_n \frac{dV(x)}{dx} \right)$$

5.  To find the total current, we integrate this expression along the entire length of the channel, from $x=0$ (source) to $x=L$ (drain). We rearrange the equation to separate the variables.

    $$I_D \cdot dx = W \mu_n C_{ox} (V_{GS} - V_t - V(x)) dV$$

    Now, integrate both sides with the appropriate limits:

    $$\int_{0}^{L} I_D \cdot dx = \int_{0}^{V_{DS}} W \mu_n C_{ox} (V_{GS} - V_t - V(x)) dV$$

    $$I_D \cdot L = W \mu_n C_{ox} \left( (V_{GS} - V_t)V_{DS} - \frac{V_{DS}^2}{2} \right)$$

    $$I_D = \mu_n C_{ox} \frac{W}{L} \left( (V_{GS} - V_t)V_{DS} - \frac{V_{DS}^2}{2} \right)$$

It's common to group the terms that depend on the fabrication process and the transistor's dimensions into a single parameter. The term $\mu_n C_{ox} \frac{W}{L}$ is often represented as **$k_n$**, known as the **transconductance parameter**.

So, the equation can be written more compactly as:

$$I_D = k_n \left( (V_{GS} - V_t)V_{DS} - \frac{V_{DS}^2}{2} \right)$$

The fundamental components that make up $k_n$—such as the electron mobility ($\mu_n$) and the gate oxide capacitance ($C_{ox}$)—are **technology parameters**. These values are defined by the foundry and are available in the **model files** that are used in SPICE simulations to accurately model the transistor's behavior. 

<img width="1202" height="685" alt="image" src="https://github.com/user-attachments/assets/077dd293-94b8-470d-850c-67c8dec281f2" />


When the drain-source voltage ($V_{DS}$) is very small, the $V_{DS}^2$ term in the equation becomes negligible compared to the term linear in $V_{DS}$.

$$V_{DS}^2 \approx 0 \quad (\text{for small } V_{DS})$$

Applying this approximation simplifies the equation significantly:

$$I_D \approx k_n (V_{GS} - V_t) V_{DS}$$

<img width="1207" height="700" alt="image" src="https://github.com/user-attachments/assets/b80ed0bc-6c80-4d09-8087-2da98d29207b" />


### **Saturation Mode**

A transistor enters the saturation region when the drain-source voltage ($V_{DS}$) is greater than or equal to the overdrive voltage ($V_{GS} - V_t$).

$$V_{DS} \ge V_{GS} - V_t$$

#### **Pinch-Off Condition**

**Pinch-off** is the beginning of saturation. As $V_{DS}$ increases, the voltage difference between the gate and the channel at the drain end decreases. When this difference, $V_{GD}$ (Gate-to-Drain voltage), drops to the threshold voltage ($V_t$), the channel at the drain end disappears or "pinches off."

At this point, the current no longer increases significantly with $V_{DS}$ and is said to be saturated. The transistor now acts like a current source controlled by $V_{GS}$. 💡

<img width="1210" height="625" alt="image" src="https://github.com/user-attachments/assets/70a2e5e5-2fb0-4e6b-9ce5-200789100b98" />

The saturation current equation is derived by substituting the boundary condition ($V_{DS} = V_{GS} - V_t$) into the linear region equation.

$$I_D = k_n \left( (V_{GS} - V_t)V_{DS} - \frac{V_{DS}^2}{2} \right)$$

**Substitute $V_{DS} = V_{GS} - V_t$:**
    
  $$I_{D,sat} = k_n \left( (V_{GS} - V_t)(V_{GS} - V_t) - \frac{(V_{GS} - V_t)^2}{2} \right)$$

  $$I_{D,sat} = k_n \left( (V_{GS} - V_t)^2 - \frac{(V_{GS} - V_t)^2}{2} \right)$$
  
  This results in the ideal saturation current equation:
    
  $$I_{D,sat} = \frac{1}{2} k_n (V_{GS} - V_t)^2$$

This equation shows that in the ideal case, the current is only dependent on the gate voltage and is independent of the drain voltage. This can act as a constant current source.

<img width="1212" height="636" alt="image" src="https://github.com/user-attachments/assets/a051e4aa-2b64-4c98-8d67-8ee38ea528c9" />

#### **Equation with Channel Length Modulation**

In reality, increasing $V_{DS}$ beyond the pinch-off point causes the pinch-off region to extend slightly towards the source, effectively shortening the conductive channel length. This phenomenon is called **channel length modulation**. A shorter channel leads to a slight increase in current.

To account for this, a correction factor is added to the ideal equation:

$$I_{D,sat} = \frac{1}{2} k_n (V_{GS} - V_t)^2 (1 + \lambda V_{DS})$$

* **$\lambda$ (Lambda)** is the **channel length modulation parameter**. It's a technology-dependent constant (with units of $1/V$) provided in the SPICE model files. A smaller value of $\lambda$ indicates a more ideal transistor with a flatter saturation current.

<img width="1207" height="668" alt="image" src="https://github.com/user-attachments/assets/29e11eae-2af9-415f-a504-c11186e48df1" />

---

## **3. SPICE Introduction**

SPICE (Simulation Program with Integrated Circuit Emphasis), developed at UC Berkeley, solves nonlinear equations for devices such as transistors, resistors, and capacitors.
Modern SPICE tools include both free versions (Ngspice, LTSpice) and commercial tools (HSPICE, PSPICE). The inputs given to a spice are:

* **Netlist:** A description of all components (transistors, resistors, etc.) and how they are connected.
* **Device Models:** This is the crucial link to the real world. A **technology file** (loaded with `.LIB` or `.include`) provides the physical and electrical parameters from the foundry (like $V_t$, mobility, etc.) that define how a transistor actually behaves.
* **Stimulus:** The input signals, such as DC voltages or time-varying pulses, applied to the circuit.
* **Analysis Commands:** Instructions telling SPICE what kind of simulation to perform. The most common types are:
    * **`.DC`:** Find the circuit's DC operating point or sweep a DC voltage.
    * **`.AC`:** Analyze the circuit's response to different frequencies.
    * **`.TRAN`:** Analyze the circuit's behavior over time (transient response).

If we give these inputs to the SPICE engine it will result is graphs explaining the functioning of the netlist. 

<img width="1208" height="676" alt="image" src="https://github.com/user-attachments/assets/622f938f-3dec-4de9-b5f0-371c06e2ccae" />

In SPICE, a node is a point in a circuit where two or more components connect. Every connection point that has a distinct voltage must be assigned a unique name or number.

Nodes are the fundamental way SPICE understands a circuit's topology, or how its components are wired together.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/21e9afd4-5d82-4a60-9e67-537f5d4b619c" />

<p align = "center"> Nodes </p>

**SPICE Netlist Example**:  

| Line in Netlist | Explanation |
| --------------- | ----------- |
| `M1 vdd n1 0 0 nmos W=1.8u L=1.2u` | Defines NMOS transistor `M1`. Node order: **Drain (vdd), Gate (n1), Source (0), Bulk (0)** — sequence **D G S B**. Model name `nmos` comes from technology file. `W` = gate width (1.8µm), `L` = gate length (1.2µm). |
| `R1 in n1 55` | Series resistor `R1` of 55 ohms between input node `in` and gate node `n1`. |
| `Vdd vdd 0 2.5` | Voltage source `Vdd` applying 2.5V between `vdd` and ground. |
| `Vin in 0 2.5` | Voltage source `Vin` applying 2.5V between input node `in` and ground. |

<img width="1216" height="644" alt="image" src="https://github.com/user-attachments/assets/9cb0ac11-802c-4daa-952c-d29144d9888c" />

<img width="1205" height="790" alt="image" src="https://github.com/user-attachments/assets/0646b0a8-0ffc-422c-ba3c-59554e896847" />

- The `.include` or `.LIB` command loads the **technology model file** into the simulation (`xxxx_025um_model.mod`).
- The NMOS/PMOS **model names** (like `nmos`, `pmos`) used in the netlist must match those defined in the technology file.

---
