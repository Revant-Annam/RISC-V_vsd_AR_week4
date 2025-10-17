### Static Behavior Evaluation: CMOS Inverter Robustness and Noise Margin

#### 📚 Contents

* Introduction to noise margin
* Noise Margin Definition — VTC and Undefined Region
* Noise Margin Summary — Handling Input "Bumps"
* Sky130 Noise margin labs

***

### Introduction to noise margin

**Noise margin** is a measure of how much noise a CMOS circuit can withstand before its logic state is corrupted.



This image contrasts two types of inverter characteristics:
* An **ideal inverter** (left) switches instantaneously at half the supply voltage ($V_{dd}/2$), featuring an infinitely steep slope.
* A **real-world inverter** (right) exhibits a gradual transition, with a finite slope in its switching region.

***

### Noise Margin Definition — VTC and Undefined Region

This diagram explains how the **Noise Margin** is determined using the inverter's **Voltage Transfer Characteristic (VTC)** curve.



* **Left Plot**: The points where the VTC's slope equals **-1** define two key thresholds:
    * $V_{IL}$: The maximum input voltage that is reliably interpreted as a logic low.
    * $V_{IH}$: The minimum input voltage that is reliably interpreted as a logic high.
* **Right Diagram**: This shows the valid output voltage levels and the input thresholds.
    * $V_{OH}$ & $V_{OL}$: Represent the guaranteed output high and low voltage levels.
    * $V_{IL}$ & $V_{IH}$: Mark the input voltage thresholds where the gain is -1.
* **Noise Margins**:
    * **Noise Margin High ($N_M H$)**: $N_M H = V_{OH} - V_{IH}$. This is the circuit's tolerance to noise when the input is a logic '1'.
    * **Noise Margin Low ($N_M L$)**: $N_M L = V_{IL} - V_{OL}$. This is the circuit's tolerance to noise when the input is a logic '0'.
* **Undefined Region**:
    * The voltage range between $V_{IL}$ and $V_{IH}$ is the **undefined** or **transition region**.
    * If input noise pushes the voltage into this zone, the output becomes unpredictable.
    * A key design goal is to **maximize both $N_M H$ and $N_M L$** to make the circuit more robust against noise.

In essence, noise margins quantify a gate's ability to tolerate voltage fluctuations on its input signals, which is critical for ensuring dependable operation in noisy digital systems.

***

### Noise Margin Summary — Handling Input "Bumps"

This illustration shows how a circuit's **Noise Margins** allow it to correctly interpret logic levels despite the presence of noise, often visualized as "bumps" on the signal.

[Image showing noise bumps on digital signals]

* **Input Thresholds**:
    * An input voltage below $V_{IL}$ (e.g., < 10% of $V_{dd}$) is recognized as a **logic '0'**.
    * An input voltage above $V_{IH}$ (e.g., > 90% of $V_{dd}$) is recognized as a **logic '1'**.
* **Output Thresholds**:
    * An output voltage near 0V ($V_{OL}$) acts as a valid **logic '0'** for the next gate.
    * An output voltage near $V_{dd}$ ($V_{OH}$) acts as a valid **logic '1'** for the next gate.
* **Noise Scenarios**:
    * **Case (a)**: A noise bump on a low signal that remains between $V_{OL}$ and $V_{IL}$ is safely ignored, and the signal is correctly read as **logic '0'**.
    * **Case (b)**: A bump that pushes the voltage into the **undefined region** (between $V_{IL}$ and $V_{IH}$) causes the output to become unstable or indeterminate.
    * **Case (c)**: A noise-induced dip on a high signal that remains between $V_{IH}$ and $V_{OH}$ is tolerated, and the signal is still correctly read as **logic '1'**.

For reliable logic operation, a signal must remain within its respective noise margin ($N_M L$ for logic '0', $N_M H$ for logic '1') and stay clear of the undefined region.
