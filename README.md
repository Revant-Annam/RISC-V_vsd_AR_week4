# Week 4: CMOS Circuit Design (sky130-style) 

Welcome to this repository detailing the fundamental characterization of CMOS circuits, using the workhorse of digital logic—the inverter—as our case study. This project bridges the critical gap between semiconductor device physics and circuit-level performance metrics.

All simulations are performed using the open-source **`ngspice`** circuit simulator and the **Google/SkyWater `sky130`** open-source Process Design Kit (PDK).

---

## 🎯 Project Overview

The goal of this project is to build a transistor-level understanding of a CMOS inverter by performing a series of fundamental analyses. We explore everything from the basic current-voltage (I-V) characteristics of individual MOSFETs to the dynamic switching behavior and operational robustness of the complete inverter gate.

This repository is structured into two main parts:
* **`/Theory`**: Contains daily markdown files that explain the theoretical concepts behind each analysis.
* **`/Tasks`**: Contains the practical `ngspice` netlists and simulation results for each analysis described in the theory section.

---

## 🛠️ Tech Stack & Tools

* **Circuit Simulator:** `ngspice`
* **Process Design Kit (PDK):** Google / SkyWater `sky130`

---

## 🔬 Analysis Breakdown: From Transistor to Gate

This project is broken down into five core modules, each with a theoretical and practical component.

| Day / Topic | Theoretical Focus (`/Theory`) | Practical Simulation (`/Tasks`) | Key Insights |
| :--- | :--- | :--- | :--- |
| **[Day 1: The Foundation 🏗️](./Theory/README_DAY1.md)** | **MOSFET I-V Behavior**: Exploring the cutoff, linear (triode), and saturation regions of an NMOS transistor. | **`Id vs. Vds` Plots**: Generating the characteristic output curves for a sky130 NMOS device. | Visualizing how a transistor acts as a voltage-controlled switch and current source. |
| **[Day 2: The Switch 💡](./Theory/README_DAY2.md)** | **Threshold Voltage & Short-Channel Effects**: Understanding $V_t$ and the impact of **velocity saturation** on current flow. | **$V_t$ Extraction**: Using the linear extrapolation method on the $I_d$ vs. $V_{gs}$ curve to find the threshold voltage. | Discovering why modern transistors have a linear (not quadratic) response in saturation. |
| **[Day 3: The Logic Gate & Speed 🚀](./Theory/README_DAY3.md)** | **VTC & Transient Analysis**: Analyzing the DC Voltage Transfer Characteristic and the dynamic **propagation delay**. | **VTC & Pulse Response Simulation**: Sweeping input voltage for VTC and applying a pulse to measure rise/fall delays. | Defining the inverter's DC switching behavior and quantifying its speed for STA. |
| **[Day 4: Noise Immunity 🛡️](./Theory/README_DAY4.md)** | **Noise Margin Analysis**: Understanding how to calculate noise immunity ($NM_L$, $NM_H$) from the VTC curve. | **Noise Margin Extraction**: Finding the gain=-1 points on the VTC to determine $V_{IL}$, $V_{IH}$, $V_{OL}$, and $V_{OH}$. | Quantifying the gate's robustness against input voltage fluctuations. |
| **[Day 5: Real-World Effects 🌍](./Theory/README_DAY5.md)** | **PVT Variation**: Observing how Process, Voltage, and Temperature variations affect inverter performance and reliability. | **Variation Analysis**: Re-simulating the VTC with different supply voltages and transistor sizes. | Understanding what makes a design reliable under non-ideal, real-world conditions. |

---

## 🚀 How to Run the Simulations

To replicate the results found in the `/Tasks` directory, you need `ngspice` installed.

1.  Navigate to one of the task directories.
2.  Run the simulation using the following command:
    ```bash
    ngspice <name_of_netlist_file>.spice
    ```
3.  The simulation will run, and if you are in interactive mode, you can use `ngspice` commands like `plot` or `display` to view the results. The `.meas` commands within the files will automatically calculate key parameters.

---

## 📜 Conclusion

This project serves as a comprehensive logbook for understanding CMOS technology from the ground up. By characterizing these fundamental properties, we establish the foundation for designing complex digital systems and performing critical verification tasks like **Static Timing Analysis (STA)**, where metrics like propagation delay and noise margins are paramount.
