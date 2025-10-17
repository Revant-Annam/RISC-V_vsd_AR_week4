# CMOS Noise Margin robustness evaluation

## Introduction to Noise Margin

**Noise margin** is a measure of how much noise a CMOS circuit can withstand before its logic state is corrupted.

<img width="949" height="559" alt="image" src="https://github.com/user-attachments/assets/9efdd871-681d-4133-bd62-1317bb8f76ee" />

This image contrasts two types of inverter characteristics:
* An **ideal inverter** (left) switches instantaneously at half the supply voltage ($V_{dd}/2$), featuring an infinitely steep slope.
* A **real inverter** (right) exhibits a gradual transition, with a finite slope in its switching region.

---

## Noise Margin Definition — VTC and Undefined Region

This diagram explains how the **Noise Margin** is determined using the inverter's **Voltage Transfer Characteristic (VTC)** curve.

<img width="982" height="582" alt="image" src="https://github.com/user-attachments/assets/08ff0d49-1aed-4b3c-837c-b848b53acef5" />

* The points where the VTC's slope equals **-1** define key thresholds:
    * $V_{IL}$: The maximum input voltage that is reliably interpreted as a logic low.
    * $V_{IH}$: The minimum input voltage that is reliably interpreted as a logic high.
    * $V_{OH}$ & $V_{OL}$: Represent the guaranteed output high and low voltage levels.
* **Noise Margins**:
    * **Noise Margin High ($N_M H$)**: $N_M H = V_{OH} - V_{IH}$. This is the circuit's tolerance to noise when the input is a logic '1'.
    * **Noise Margin Low ($N_M L$)**: $N_M L = V_{IL} - V_{OL}$. This is the circuit's tolerance to noise when the input is a logic '0'.
* **Undefined Region**:
    * The voltage range between $V_{IL}$ and $V_{IH}$ is the **undefined** or **transition region**.
    * If input noise pushes the voltage into this zone, the output becomes unpredictable.
    * A key design goal is to **maximize both $N_M H$ and $N_M L$** to make the circuit more robust against noise.

### Conditions for Proper Operation

For a logic gate to function correctly, the output voltage from one gate must be correctly interpreted by the input of the next. This requires two conditions to be met:

* The highest voltage that a gate will output for a logic '0' ($V_{OL_{MAX}}$) must be lower than the highest voltage the next gate will accept as a logic '0' ($V_{IL_{MAX}}$).
* The lowest voltage a gate will output for a logic '1' ($V_{OH_{MIN}}$) must be higher than the lowest voltage the next gate requires to see a logic '1' ($V_{IH_{MIN}}$).

### Behavior in Different Input Ranges

The inverter's behavior changes dramatically depending on the input voltage ($V_{in}$) level, making it suitable for different applications in each range. 

* **For $V_{in} \le V_{IL}$:**
    In this region, the magnitude of the inverter's gain is less than one. This means the circuit actually suppresses noise, ensuring a stable and predictable output. Due to this stability and low gain, the circuit operates reliably as a digital logic element.

* **For $V_{in} \ge V_{IH}$:**
    Similarly, the gain magnitude here is also less than one, providing the same noise-dampening effect for a stable output. This predictable behavior is essential for its function in digital circuits.

* **For $V_{IL} < V_{in} < V_{IH}$:**
    Within this range, known as the undefined region, the gain magnitude is greater than one. Here, the inverter becomes a high-gain amplifier, meaning that small input changes are magnified at the output. While this makes the output unstable for digital logic, this high-gain characteristic is useful for analog circuit applications, such as in amplifiers or comparators.
  
### Noise Margin Equations

The noise margins are calculated to quantify the voltage buffer a circuit has against noise.

* **Low-Level Noise Margin ($N_{ML}$):**
    This margin represents the maximum noise voltage that can be added to a logic '0' signal without causing a logic error.
    $$N_{ML} = V_{IL_{MAX}} - V_{OL_{MAX}}$$

* **High-Level Noise Margin ($N_{MH}$):**
    This margin represents the maximum noise voltage that can be subtracted from a logic '1' signal before it is no longer recognized as high.
    $$N_{MH} = V_{OH_{MIN}} - V_{IH_{MIN}}$$

* **Overall Noise Margin (NM):**
    The overall noise immunity of the circuit is determined by the smaller of the two margins, as this represents the worst-case vulnerability to noise.
    $$NM = \min(N_{ML}, N_{MH})$$

<img width="1163" height="554" alt="image" src="https://github.com/user-attachments/assets/771ea0d5-7917-47e0-85e6-171b17e9d516" />

<img width="1215" height="681" alt="image" src="https://github.com/user-attachments/assets/510cf78e-2ab4-4543-be18-1e17b030daaf" />

<img width="1215" height="688" alt="image" src="https://github.com/user-attachments/assets/d5d254a3-242c-4350-83d7-a7739371e975" />

### Impact of Transistor Sizing on Noise Margin

The table from the analysis demonstrates how changing the relative sizes of the PMOS and NMOS transistors impacts the inverter's noise margins.

<img width="1204" height="675" alt="image" src="https://github.com/user-attachments/assets/910df819-4d98-47a6-93bc-1c8fe74b067c" />

* **Noise Margin High ($N_{MH}$):** As the width of the PMOS transistor increases relative to the constant-sized NMOS (from `Wn/Ln` to `5Wn/Ln`), the **$N_{MH}$** steadily improves (from 0.3 to 0.42). This is because the PMOS transistor acts as the **pull-up device**. A wider, stronger PMOS can pull the output voltage to the high supply rail more effectively, which directly contributes to a better high noise margin.

* **Noise Margin Low ($N_{ML}$):** In contrast, the **$N_{ML}$** remains nearly the same, showing only a slight decrease (from 0.3 to 0.27). This is expected because the low noise margin is determined by the **pull-down device**, which is the NMOS transistor. Since the dimensions of the NMOS are not being changed, its performance in pulling the output to ground is unaffected, resulting in a relatively constant $N_{ML}$.
