# CMOS Power Supply and Device Variation Robustness Evaluation

Modify supply voltage (Vdd) and regenerate VTC plots to examine switching threshold displacement. Adjust transistor dimensions (e.g., W/L ratios for PMOS or NMOS) to emulate device variability, and analyze resulting impacts on VTC characteristics, noise margins, and propagation delays.

---

## Significance of This Analysis

- **Guarantees Dependable Functionality:** Digital circuits encounter fluctuations in supply voltage (V<sub>DD</sub>) or transistor characteristics (threshold voltage, carrier mobility, etc.) stemming from fabrication process, temperature variations, or aging phenomena. Investigating these variations ensures proper circuit operation under all operational scenarios.

- **Forecasts Performance Deviation:** Parametric variations can alter the **switching threshold (V<sub>M</sub>)**, noise immunity margins, signal propagation delays, and energy consumption. Comprehending these impacts enables designers to predict performance trajectory shifts.

- **Enhances Circuit Immunity:** Simulating extreme operating scenarios empowers designers to **ensure correct logic state recognition** and mitigate failures arising from marginal operational conditions.

- **Facilitates Yield Enhancement:** Device characteristic disparities across a semiconductor wafer can result in specification failures for certain chips. Variation analysis aids in **maximizing manufacturing yield** by engineering circuits capable of accommodating these disparities.

- **Establishes Design Safety Margins:** Assists designers in **determining safe operational boundaries** for voltage levels, timing parameters, and current magnitudes to guarantee reliable functionality even under stressed conditions.

- **Essential for High-Performance and Energy-Efficient Designs:** Even minor parametric variations can substantially influence **timing precision and noise tolerance**, rendering these investigations indispensable in contemporary VLSI circuit development.

---

## Simulation Procedure for `day5_inv_supplyvariation_Wp1_Wn036.spice` Netlist

### View Netlist Configuration

```bash
vim day5_inv_supplyvariation_Wp1_Wn036.spice
```
<img width="1920" height="922" alt="image" src="https://github.com/user-attachments/assets/fe86def6-a6cc-4e33-b028-77e8b05ca98b" />

### Generate Waveform Using ngspice

```bash
ngspice day5_inv_supplyvariation_Wp1_Wn036.spice
```
<img width="700" height="620" alt="image" src="https://github.com/user-attachments/assets/9675dc94-214d-4650-8657-f50f3822371d" />

### View Device Variation Netlist

```bash
vim day5_inv_devicevariation_wp7_wn042.spice
```
<img width="926" height="791" alt="image" src="https://github.com/user-attachments/assets/2ecc166f-5572-46dd-8238-ca2145bd2777" />

### Plot Waveform for Device Variation Analysis

```bash
ngspice day5_inv_devicevariation_wp7_wn042.spice
plot out vs in
```
<img width="926" height="791" alt="image" src="https://github.com/user-attachments/assets/24d9fa32-814f-4c48-89d1-a530a64e5600" />

---
