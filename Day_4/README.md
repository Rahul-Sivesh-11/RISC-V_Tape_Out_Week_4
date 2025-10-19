# Static Behavior Evaluation: CMOS Inverter Robustness and Noise Margin

## Noise Margin Fundamentals

Noise margin represents the maximum tolerable noise voltage magnitude that a CMOS circuit can withstand without generating logic-level errors.

<img width="786" height="534" alt="image" src="https://github.com/user-attachments/assets/69258608-a965-4f24-a4b9-7b4307e378e4" />

## CMOS Inverter Transfer Characteristics and Noise Margin

This figure presents a comparative analysis:

- **Ideal Inverter Response:** Demonstrates instantaneous switching precisely at **V<sub>DD</sub>/2** with infinite slope magnitude (left panel).  
- **Practical Inverter Response:** Exhibits finite slope with a **progressive transition zone** (right panel).

<img width="781" height="492" alt="image" src="https://github.com/user-attachments/assets/7b072f9a-a1b5-4891-b532-7234c424e24e" />

### Noise Margin Characterization

The figure further demonstrates how **Noise Margin** parameters are extracted from the **Voltage Transfer Characteristic (VTC)** of a CMOS inverter.  
The **indeterminate region** within the VTC represents the input voltage span where output logic state ('0' or '1') remains ambiguous. Noise margins quantify the inverter's immunity to input disturbances within this operational zone.

## Voltage Transfer Characteristic (VTC) Analysis and Noise Margin Parameters

### Left Panel Key Features:

- The VTC slope achieves −1 at two critical voltage thresholds:
     - V<sub>IL</sub>: Input Low Threshold Voltage
     - V<sub>IH</sub>: Input High Threshold Voltage

### Right Panel Key Features:

- V<sub>OH</sub> and V<sub>OL</sub>: Validated output voltage levels for high and low logic states
- V<sub>IL</sub> and V<sub>IH</sub>: Input threshold voltages where the slope magnitude equals unity (−1)

### Noise Margin Calculations:

- NMH = V<sub>OH</sub> − V<sub>IH</sub> → Quantifies maximum noise immunity for logic '1' state
- NML = V<sub>IL</sub> − V<sub>OL</sub> → Quantifies maximum noise immunity for logic '0' state

### Indeterminate Region Characteristics:

- Within the voltage range spanning V<sub>IL</sub> to V<sub>IH</sub>, logic state remains undefined.
- Noise disturbances occurring within this range can generate unstable or erroneous output states.

### Design Optimization Objective:

- Maximize both NMH and NML values to enhance circuit immunity.
- Noise margin parameters indicate the degree of signal degradation a logic level can sustain before introducing errors, ensuring dependable operation in electrically noisy environments.

<img width="744" height="484" alt="image" src="https://github.com/user-attachments/assets/100bdb99-43cc-4f3d-bdf3-125a4dda0461" />

<img width="766" height="467" alt="image" src="https://github.com/user-attachments/assets/8cb4cab9-2746-4f73-b609-c5945a098bf3" />

## Noise Margin Response Analysis

**Safe Noise Transient:** When the noise excursion remains confined between V<sub>OL</sub> and V<sub>IL</sub>, it continues to be recognized as a valid logic '0' state.

**Potentially Critical Noise Transient:** When the noise excursion enters the indeterminate region (between V<sub>IL</sub> and V<sub>IH</sub>), the logic state becomes ambiguous — potentially interpreted as either '0' or '1'.

**Hazardous Noise Transient:** When noise magnitude surpasses V<sub>IH</sub> while remaining below V<sub>OH</sub>, the signal will be interpreted as logic '1', potentially introducing errors requiring correction mechanisms.

This analytical framework assists designers in comprehending noise-threshold interactions and ensures adequate circuit robustness.

<img width="1274" height="613" alt="image" src="https://github.com/user-attachments/assets/3c45eb54-4f8b-4c5c-9653-c7929b42dca7" />

## Critical Findings on CMOS Inverter Noise Margin Performance

**Configuration: (W<sub>p</sub>/L<sub>p</sub>) = 2 × (W<sub>n</sub>/L<sub>n</sub>)**
- High-level noise margin (NMH) exhibits increased magnitude.
- Mechanism: The PMOS device demonstrates enhanced drive strength in this arrangement, enabling superior maintenance of output voltage at logic '1' level through effective charge retention on load capacitance.

**Configuration: (W<sub>p</sub>/L<sub>p</sub>) = 4 × (W<sub>n</sub>/L<sub>n</sub>)**
- Low-level noise margin (NML) experiences modest reduction.
- Mechanism: The NMOS device becomes comparatively weaker relative to the PMOS, resulting in diminished capability to discharge the output node completely to logic '0' level.

**Configuration: (W<sub>p</sub>/L<sub>p</sub>) = 5 × (W<sub>n</sub>/L<sub>n</sub>)**
- The NMH approaches a stable plateau value, indicating that additional PMOS width scaling yields minimal high-level noise margin improvement.

**Comprehensive Analysis:**

- Low-level noise margin (NML) demonstrates relative stability across these dimensional configurations.
- High-level noise margin (NMH) increases by approximately 120 mV with PMOS strengthening, emphasizing the CMOS inverter's enhanced immunity against input disturbances at logic '1' state.

---

## Sky130 Noise Margin Laboratory Exercises

### View Circuit Netlist

```bash
vim day4_inv_noisemargin_wp1_wn036.spice 
```
![img](https://github.com/Rahul-Sivesh-11/RISC-V_Tape_Out_Week_4/blob/main/Images/WhatsApp%20Image%202025-10-19%20at%2023.43.43_c3e7cbbd.jpg)

### Execute Simulation and Generate VTC Plot

```bash
ngspice day4_inv_noisemargin_wp1_wn036.spice
plot out vs in
```
![img](https://github.com/Rahul-Sivesh-11/RISC-V_Tape_Out_Week_4/blob/main/Images/WhatsApp%20Image%202025-10-19%20at%2023.43.44_f23e1704.jpg)

---
