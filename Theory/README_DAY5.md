# Week 4 Day 5 - CMOS power supply and device variation robustness evaluation

---

## Static behaviour evaluation — CMOS inverter robustness — Power supply variation

Scaling the power supply voltage directly influences the static characteristics of a CMOS inverter. This affects its switching threshold ($V_m$), noise margins, and overall performance.

### SPICE Simulation

<img width="1272" height="711" alt="image" src="https://github.com/user-attachments/assets/4e65cb7f-f3a5-4f52-a31a-721b545ba265" />


Simulation of the CMOS inverter using multiple supply voltages, scaling **Vdd** down from **2.5V** to **0.5V**. The dimensions of the transistors are kept constant throughout the simulation, with the PMOS width ($W_p$) at 0.9375 μm and the NMOS width ($W_n$) at 0.375 μm.

<img width="1107" height="707" alt="image" src="https://github.com/user-attachments/assets/f9df1d59-4c18-41b5-a398-7c548d0e6c20" />

The plot shows how the **Voltage Transfer Characteristics (VTC)** of the inverter change with different power supply levels. Major observations from the plot are:

* **Switching Threshold ($V_m$)**: As the supply voltage **Vdd** is reduced, the inverter's switching threshold, $V_m$, tends to shift closer to the middle of the voltage range. However, this shift comes with the trade-off of shrinking noise margins.

* **Noise Margins**: A lower **Vdd** leads to reduced noise immunity. This makes the circuit more susceptible to noise and fluctuations in the power supply.

### Advantages and Disadvantages of low power supply:

* **Advantages of using low power supply**: Operating at a lower **Vdd**, such as 0.5V, offers substantial benefits like an approximate **50% improvement in gain** and a **90% reduction in energy consumption**. This highlights the efficiency of scaling the power supply for CMOS inverters.

* **Disadvantage of using low power supply**: Although lowering **Vdd** enhances gain and energy efficiency, it can negatively impact performance. With reduced drive strength, it fails to charge the load capacitor completely thus resulting in an unexpected result. Due to this the functionality of the CMOS inverter is affected at higher frequency. 

<img width="1212" height="683" alt="image" src="https://github.com/user-attachments/assets/f544b95e-763d-439e-a13d-35a41a720246" />

The variation in the switching threshold or the Noise margin is very less thus revealing the robustness of the CMOS inverter.

---

## Static behaviour evaluation-CMOS inverter robustness-Device variation

Device variation is a critical factor that determines the robustness of a CMOS inverter. These variations primarily stem from two sources during fabrication:
* Etching Variation
* Oxide Thickness Variation

#### Etching Variation
**Etching** is a fundamental step in the manufacturing of semiconductors that defines the physical dimensions of the CMOS layout, such as the **Width (W)** and **Length (L)** of transistors. During fabrication, slight differences often arise between the designed dimensions and the final physical structures.

* The **P-diffusion region** sets the width of the PMOS gate.
* The **N-diffusion region** sets the width of the NMOS gate.
* The **Poly-silicon layer thickness** determines the gate length (L), which typically corresponds to the technology node (e.g., 20nm, 45nm).

Etching also impacts other layout features, including metal layers and the contacts between them. The final fabricated **W** and **L** of transistors often deviate from their ideal design values. Since the drain current ($I_d$) is dependent on both **W** and **L**, any variations from etching directly influence the transistor's current. This, in turn, causes variations in the CMOS inverter's:
* Switching Threshold ($V_m$)
* Noise Margins

<img width="1204" height="689" alt="image" src="https://github.com/user-attachments/assets/a323384c-b9ec-47b8-aa89-94ae2c72f444" />


The frist image shows the contrast between the ideal design mask and the actual fabricated structure, illustrating how **etching variation** occurs. Limitations in the manufacturing process cause these variations in **W** and **L**, which affect the device's performance and current drive capability.

The second image shows an **inverter chain**, which is a series of interconnected CMOS inverters. The layout displays the physical structure of each inverter, indicating key layers such as Poly (Gate), P-Diffusion, N-Diffusion, VDD, and VSS. These chains are frequently used to analyze **delay, robustness, and process variations** across several logic stages.

#### Oxide Thickness Variation
During MOSFET manufacturing, the actual gate **oxide thickness** often differs from the ideal, intended thickness. Since the drain current ($I_d$) is dependent on the oxide capacitance ($C_{ox}$), any variation in the oxide's thickness directly affects $I_d$, thereby influencing the CMOS inverter's performance.

<img width="1170" height="589" alt="image" src="https://github.com/user-attachments/assets/a550e21f-c69f-4f47-814c-8545c6fc5b9e" />

The image provided illustrates the discrepancy between the **ideal** and **actual** oxide thickness that can occur during fabrication.

These two primary sources of deviation—**etching variation** (affecting W and L) and **oxide thickness variation** affect the performance of the CMOS inverter. Due to these variations the affect on performance is negligible thus proving the overall robustness of CMOS inverters. We will now explore the effects of sweeping the PMOS and NMOS widths.

---

### Transistor Strength Definitions:

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/9a0c745a-c6cc-49bf-9edd-173e61c39535" />


* **Strong PMOS**: A PMOS transistor with low resistance, which allows it to charge the output capacitor more quickly. This is achieved by designing the PMOS with a **wider** gate.
* **Weak NMOS**: An NMOS transistor with higher resistance. Since resistance is inversely proportional to the device's area, this is achieved by making the NMOS **narrower**.
* **Weak PMOS**: A PMOS transistor with higher resistance, created by designing it with a **narrower** gate.
* **Strong NMOS**: An NMOS transistor that features low resistance. This is accomplished by fabricating it with a **wider** gate.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1f7f7159-a305-4684-9e92-08f013af172d" />

As we can see from the plot the variations due to the change in the width and length of NMOS or PMOS doesnot affect the basic functionality of the CMOS inverter.

The **CMOS inverter Robustness** can be understood by determining various parameters which might affect the functionality of the CMOS Inverter. After analyzing the various parameters we can conclude that the CMOS inverter is indeed a robust circuit.
