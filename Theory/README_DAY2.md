# **Week 4: Day 2 - Velocity saturation and basics of CMOS inverter VTC**

## 📚 Contents

  - [SPICE Simulation for Lower Nodes](#spice-simulation-for-lower-nodes)
      - [Observation 1: Long vs. Short Channel NMOS Characteristics](#observation-1-long-vs-short-channel-nmos-characteristics)
      - [Observation 2: Peak Current in Long vs. Short Channel Devices](#observation-2-peak-current-in-long-vs-short-channel-devices)
  - [Labs: Sky130 Id-Vgs](#labs-sky130-id-vgs)
  - [CMOS Voltage Transfer Characteristics](#cmos-voltage-transfer-characteristics)
  - [CMOS Inverter: Transistor and Switch-Level Views](#cmos-inverter-transistor-and-switch-level-views)
  - [Load Line Curves for NMOS and PMOS](#load-line-curves-for-nmos-and-pmos)

-----

## `SPICE simulation for lower nodes`

The plot below illustrates the output characteristics of an NMOS device with dimensions W=1.8μm and L=1.2μm, giving a W/L ratio of 1.5.

<img width="1307" height="721" alt="image" src="https://github.com/user-attachments/assets/bab7b10e-60d3-41fb-ae84-0faca4b494ce" />


  * **Linear (Resistive) Region:** In this region, defined by $V_{ds} < (V_{gs} - V_t)$, the drain current ($I_d$) changes linearly with the drain-source voltage ($V_{ds}$).
  * **Saturation Region:** In this region, defined by $V_{ds} \ge (V_{gs} - V_t)$, the drain current ($I_d$) becomes less dependent on $V_{ds}$ and is primarily influenced by channel length modulation.

Essentially, the device operates in the **Linear Region** before the pinch-off point ($V_{ds} = V_{gs} - V_t$) and enters the **Saturation Region** after this point.

--- 

### Observation 1: Long vs. Short Channel NMOS Characteristics

The following plot contrasts the output characteristics of a long-channel and a short-channel NMOS device, both having the **same W/L ratio**.

<img width="1294" height="666" alt="image" src="https://github.com/user-attachments/assets/0643368e-bbcf-46b4-ae2c-a415232d1a94" />


The **left plot** represents a long-channel device (W = 1.8μm, L = 1.2μm), while the **right plot** is for a short-channel device (W = 0.375μm, L = 0.25μm). Since its channel length is 0.25μm or less, the second device is considered a short-channel device.

Although both transistors share the same W/L ratio, their electrical behaviors differ significantly due to their absolute dimensions.

When sweeping the gate-source voltage ($V_{gs}$) at a constant drain-source voltage ($V_{ds}$):

  * In **long-channel devices**, the drain current ($I_d$) exhibits a nearly perfect **quadratic relationship** with $V_{gs}$.
  * In **short-channel devices**, $I_d$ is quadratic at lower $V_{gs}$ values but transitions to a **linear relationship** at higher $V_{gs}$ levels. This phenomenon is caused by **velocity saturation**, where charge carriers reach their maximum possible velocity as the electric field intensifies, thus limiting the current flow.

<img width="1206" height="582" alt="image" src="https://github.com/user-attachments/assets/793d47dd-751a-40e0-b3ab-6164818047cf" />

This comparison clearly shows how **velocity saturation** in short-channel devices modifies the drain current's behavior, shifting it from quadratic to linear as $V_{gs}$ increases.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/4dadabf1-6341-4e29-95a5-5948f4783a68" />

<img width="1203" height="607" alt="image" src="https://github.com/user-attachments/assets/97cd2a4d-04ba-4d71-a38d-2b3b2c9742ec" />


The relationship between drift velocity ($v_d$) and the electric field ($E$) is typically described by a model that covers both low and high field conditions. A commonly used equation that combines both behaviors is:

$$v_d = \frac{\mu E}{1 + \frac{E}{E_c}}$$

**At Low Electric Field (Linear Region)**

At low electric fields, the drift velocity is directly proportional to the electric field strength. The relationship is linear.

$$v_d = \mu E$$

* **$v_d$** is the **drift velocity** of the charge carriers (e.g., electrons).
* **$\mu$** (mu) is the **carrier mobility**, a constant that measures how easily carriers can move through the material.
* **$E$** is the magnitude of the **electric field**.

**High Electric Field (Saturation Region)**

At high electric fields, the velocity no longer increases and saturates at a maximum value, $v_{sat}$. This happens due to increased scattering.

$$v_d = v_{sat}$$

* **$v_{sat}$** is the **saturation velocity**, the maximum speed the carriers can achieve in the material.

The general equation shown at the top smoothly transitions between these two regions. The term **$E_c$** is the critical electric field at which the velocity begins to saturate.

The **velocity saturation** effect introduces an additional operating mode in short-channel devices.

  * **Long Channel (\> 250 nm) Modes:** Cutoff → Resistive → Saturation
  * **Short Channel (\< 250 nm) Modes:** Cutoff → Resistive → **Velocity Saturation** → Saturation


<img width="1300" height="719" alt="image" src="https://github.com/user-attachments/assets/f90e9c39-442e-4b78-baa9-b54428df40d8" />

### Observation 2: Peak Current in Long vs. Short Channel Devices

The figure below compares the **peak drain current ($I_d$)** between long-channel and short-channel NMOS devices.

<img width="1396" height="719" alt="image" src="https://github.com/user-attachments/assets/9c5a1a7f-40c6-43d7-b67e-f9131885f35c" />

  * **Left Plot (Long-channel):** W = 1.8μm, L = 1.2μm → Peak current = **410 μA**
  * **Right Plot (Short-channel):** W = 0.375μm, L = 0.25μm → Peak current = **210 μA**

Despite enabling faster switching and higher integration density, **short-channel devices** exhibit a **lower peak drain current** compared to their long-channel counterparts.

This reduction in current is a direct consequence of **velocity saturation**, which caps the speed of carriers in short-channel devices. In long-channel transistors, carriers can accelerate more freely, leading to a higher peak $I_d$.

-----

## `CMOS Voltage Transfer Characteristics`

**The MOSFET as a Switching Element:**

A MOSFET can be simplified as a voltage-controlled switch.

  * **OFF State:** When the gate-source voltage is below the threshold voltage ($|V_{gs}| < |V_{th}|$), the MOSFET acts as an **open switch** with nearly infinite resistance.
  * **ON State:** When the gate-source voltage exceeds the threshold voltage ($|V_{gs}| > |V_{th}|$), the MOSFET acts as a **closed switch** with a finite ON resistance.

<img width="1173" height="703" alt="image" src="https://github.com/user-attachments/assets/b0f302ea-82d1-4524-a1ef-d5d83a5e764f" />


### `CMOS Inverter: Transistor and Switch-Level Views`

The diagram below presents the **CMOS inverter** from two perspectives: a **transistor-level** schematic and a simplified **switch-level** model.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/0ec93129-5a05-4f40-af44-eb9e4452e95f" />


The **left schematic** shows the standard transistor-level inverter. A PMOS transistor connects to the high voltage supply ($V_{dd}$), an NMOS transistor connects to the ground ($V_{ss}$), and the input ($V_{in}$) drives both gates. The output ($V_{out}$) is tapped from the shared drain connection, driving a load capacitance ($C_L$).

The **middle diagram** shows the switch model for a high input ($V_{in} = V_{dd}$). The NMOS is ON (a resistor, $R_n$) and the PMOS is OFF (an open circuit), pulling $V_{out}$ to 0.

The **right diagram** shows the model for a low input ($V_{in} = 0$). The PMOS is ON (a resistor, $R_p$) and the NMOS is OFF (an open circuit), pulling $V_{out}$ to $V_{dd}$.

* If **$V_{in} = V_{dd}$**, then **$V_{out} = 0$** (NMOS conducts, PMOS is off).

* If **$V_{in} = 0$**, then **$V_{out} = V_{dd}$** (PMOS conducts, NMOS is off).

This fundamental operation ensures that one transistor is always off in steady state, leading to the **low static power consumption** and **sharp switching characteristics** that define CMOS logic.

---

### `Load Line Curves for NMOS and PMOS`

**Step 1**

First, express the PMOS gate-source voltage ($V_{gsP}$) in terms of the input voltage, $V_{in}$. All internal node voltages should be rewritten using only $V_{in}$, $V_{out}$, $V_{dd}$, and $V_{ss}$. This is done to easily understand the inverter characteristics.

<img width="1493" height="721" alt="image" src="https://github.com/user-attachments/assets/9629ce34-c32b-40ed-8f31-381da29643f0" />

**Steps 2 & 3**

Next, express both the PMOS and NMOS drain-source voltages ($V_{dsP}$ and $V_{dsN}$) as functions of the output voltage, $V_{out}$. From the $I_{dsN}$ v/s. $V_{out}$ graph we get the load curves of PMOS and NMOS.

<img width="1204" height="655" alt="image" src="https://github.com/user-attachments/assets/abbbaa23-4100-4135-a24c-93e656a6c11d" />

<img width="1207" height="399" alt="image" src="https://github.com/user-attachments/assets/fae6399f-f302-47b7-806e-c334051ff918" />


**Step 4**

Finally, combine the NMOS and PMOS load curves by equating their drain current ($I_{ds}$) equations. By sweeping $V_{in}$ and finding the corresponding intersection point, we can plot the **Voltage Transfer Characteristic (VTC)**. This VTC graph illustrates the inverter's switching behavior as the input transitions from high to low. From this graph we can also understand the region of operation for a analog circuits and the region of operation for a digital circuit. 

<img width="1214" height="677" alt="image" src="https://github.com/user-attachments/assets/c2fe7523-4f7b-496e-a526-242a97ac047b" />
