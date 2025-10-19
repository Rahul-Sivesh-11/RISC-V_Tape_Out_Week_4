## Introduction

This comprehensive workshop delivers practical experience in CMOS circuit design, combining foundational theory with hands-on SPICE simulation exercises. Participants will gain proficiency in modeling, simulating, and evaluating transistor-level circuits utilizing industry-standard tools alongside the open-source Sky130 PDK.

**Learning Objectives:**
- Grasp transistor operational principles and current-voltage relationships
- Master circuit simulation techniques with SPICE
- Perform timing analysis and delay measurement
- Apply Sky130 technology models to practical circuit design

---

## Module 1: Foundational Concepts in Circuit Design

### SPICE's Importance in Contemporary Circuit Design

SPICE (Simulation Program with Integrated Circuit Emphasis) functions as the cornerstone of pre-fabrication verification, allowing engineers to confirm circuit functionality prior to expensive manufacturing processes.

#### Core Functions

| Function | Advantage |
|---------|---------|
| **Logic Validation** | Confirm design correctness prior to fabrication |
| **Timing Evaluation** | Quantify signal delays and transmission quality |
| **Energy Consumption Analysis** | Determine both static and dynamic power usage |
| **Design Refinement** | Enable rapid iteration on transistor dimensions and circuit structure |

#### Application Example: Analyzing a CMOS Inverter

The CMOS inverter serves as the elementary component of digital circuitry. Its functionality illustrates complementary transistor switching:

<p align="center">
  <img src="Images/cmos_inverter.png" alt="CMOS Inverter Circuit" width="600"/>
  <br>
  <em>Figure 1: CMOS Inverter Circuit Diagram</em>
</p>

**Circuit Configuration:**
- PMOS transistor positioned between supply voltage and output terminal
- NMOS transistor positioned between output terminal and ground reference
- Input signal connected to both transistor gates
- Output capacitance (CL) represents subsequent stage loading

**Operational Characteristics:**

| Input Condition | PMOS State | NMOS State | Output Level | Conduction Path |
|-------------|------|------|--------|--------------|
| LOW (0V) | ON | OFF | HIGH (VDD) | VDD → PMOS → Output |
| HIGH (VDD) | OFF | ON | LOW (0V) | Output → NMOS → GND |

#### Analysis Results

SPICE produces two essential characterizations:

<p align="center">
  <img src="Images/vtc_char.png" alt="Inverter Characteristics" width="700"/>
  <br>
  <em>Figure 2: CMOS Inverter I-V and VTC Characteristics</em>
</p>

1. **I-V Characteristic Curves**: Illustrates drain current versus output voltage under varying input scenarios
2. **Voltage Transfer Curve (VTC)**: Depicts the relationship between input and output voltages, exposing noise margins and transition points

---

### Fundamentals of Timing Characterization

Gate delays vary dynamically—they're influenced by operating conditions requiring precise modeling.

#### Delay Characterization Tables

Contemporary timing libraries employ two-dimensional tables referenced by:
- **Input Transition Time**: The rate of input signal edge change
- **Load Capacitance**: Capacitive burden at the output terminal

<p align="center">
  <img src="Images/delay_table.png" alt="Delay LUT Structure" width="650"/>
  <br>
  <em>Figure 3: Delay Lookup Table Organization</em>
</p>

#### Computing Aggregate Output Capacitance

When a logic gate supplies multiple destinations, calculate combined capacitance using:

**C_total = C_pin_output + Σ(C_pin_input_fanouts) + Σ(C_wire_segments)**

**Example Application:**

For driver gate G1 supplying loads G2, G3, and G4 via an interconnect network:

```
C_total(G1) = C_out(G1) + C_in(G2) + C_in(G3) + C_in(G4) 
              + C_wire1 + C_wire2 + C_wire3 + C_wire4
```
---

## Module 2: NMOS Device Physics

### Device Architecture and Functionality

An NMOS transistor comprises four connection points:
- **Gate (G)**: Control electrode
- **Drain (D)**: Current exit point
- **Source (S)**: Current entry point
- **Bulk/Body (B)**: Substrate terminal

<p align="center">
  <img src="Images/nmos_structure.png" alt="NMOS Structure" width="500"/>
  <br>
  <em>Figure 6: NMOS Transistor Physical Structure</em>
</p>

#### Operational Modes

>V_GS - Gate Voltage  
>V_th - Threshold Voltage

**Understanding Surface Inversion:**
- Surface inversion occurs when sufficient positive gate voltage converts the p-type substrate surface to n-type behavior.

**Cut-off Mode (`V_GS` < `V_th`):**
- Inversion layer absent
- Drain current approaches zero (leakage only)
- Device functions as open circuit

**Triode/Linear Mode (`V_GS` > `V_th`, `V_DS` < `V_GS` - `V_th`):**
- Uninterrupted conduction channel exists
- Current varies with (V_GS - V_th)·V_DS
- Device operates as voltage-dependent resistor

**Saturation Mode (`V_GS` > `V_th`, `V_DS` ≥ `V_GS` - `V_th`):**
- Channel constricts at drain end
- Current largely independent of V_DS
- Current follows (V_GS - V_th)² relationship

**Adjusted Threshold Voltage:**

<p align="center">
  <img src="Images/threshold_eqn.png" alt="Body Effect Equation" width="400"/>
</p>

```
V_th(V_SB) = V_th0 + γ × [√(|2φ_F| + V_SB) - √(|2φ_F|)]
```

Where:
- **γ**: Body effect parameter (foundry-supplied)
- **φ_F**: Fermi level potential (intrinsic material property)
- **V_th0**: Threshold voltage at zero body bias

**Physical Mechanism:**
- Reverse substrate bias expands depletion width
- Increased gate voltage needed for channel formation
- Impacts series-connected transistors in complex gates

### Current Modeling Equations

#### Cut-off Region Behavior

```
When V_GS < V_th:
├── Insufficient gate voltage for band bending
├── No carrier inversion at silicon-oxide interface
├── Only reverse-biased junction present (substrate-drain)
└── Current confined to leakage levels (~pA to nA range)
```

#### Linear Region Behavior

```
When V_GS > V_th and V_DS small:
├── Robust inversion channel established
├── Uniform channel depth throughout length
├── Gradual voltage drop along channel
└── Current governed by drift-diffusion mechanics

I_D = µ_n × C_ox × (W/L) × [(V_GS - V_th)×V_DS - V_DS²/2]
     ↑       ↑       ↑              ↑
  mobility oxide  geometry    voltage terms
```

#### Saturation Region Behavior

```
When V_DS ≥ V_GS - V_th:
├── Channel voltage at drain reaches V_GS - V_th
├── Carrier density approaches zero at drain (pinch-off)
├── Channel "closes" near drain terminal
└── Additional V_DS increases don't modify channel charge

I_Dsat = (µ_n × C_ox × W)/(2L) × (V_GS - V_th)² × (1 + λ×V_DS)
         ↑___________________________↑              ↑
         drive strength term                     CLM correction
```

Where: (Process Constants)
- **μ_n**: Carrier mobility for electrons
- **C_ox**: Oxide capacitance density
- **W/L**: Transistor aspect ratio
- **λ**: Channel length modulation coefficient

### Carrier Drift Mechanism

Current conduction arises from electric field-driven carrier movement:

**Carrier Drift Velocity:**
```
v_drift = μ_n × E_field
```

**Resulting Drain Current:**
```
I_D = (Charge density) × (Drift velocity) × (Channel width)
I_D = Q_i(x) × v(x) × W
```

This establishes the basis for deriving transistor current models from fundamental physics.

---

## Module 3: Practical SPICE Simulation

### Design Abstraction Hierarchy

VLSI design environments function across several abstraction tiers:

<p align="center">
  <img src="Images/spice_working.png" alt="Simulation Hierarchy" width="650"/>
  <br>
  <em>Figure 12: VLSI Design Simulation Hierarchy</em>
</p>

| Abstraction Tier | Tool Examples | Application |
|-------|---------------|---------|
| **Fabrication Process** | SUPREME, TCAD | Simulate manufacturing impact on devices |
| **Circuit Level** | SPICE, Spectre, HSPICE | Examine transistor-level performance |
| **Logic Level** | VCS, ModelSim, Questa | Validate HDL design correctness |
| **System Architecture** | Gem5, SystemC | Assess overall system behavior |

### SPICE Simulation Methods

| Simulation Type | Syntax | Application |
|----------|---------|----------|
| **DC Operating Point** | `.op` | Determine equilibrium voltages/currents |
| **DC Sweep** | `.dc` | Create I-V characteristic plots |
| **Transient** | `.tran` | Perform time-domain signal analysis |
| **AC** | `.ac` | Evaluate frequency-dependent response |
| **Noise** | `.noise` | Assess device noise contributions |

### SPICE Netlist Format

#### Device Specification

**MOSFET:**
```spice
M<n> <drain> <gate> <source> <bulk> <model> W=<width> L=<length>
```

**Resistor:**
```spice
R<n> <node1> <node2> <resistance>
```

**Voltage Source:**
```spice
V<n> <positive_node> <negative_node> <DC_value>
```

<p align="center">
  <img src="Images/example_for_spice_netlist_syntax.png" alt="Spice Netlist Syntax Example" width="500"/>
  <br>
  <em>Figure 13: Spice Netlist Syntax Example
  </em>
</p>

#### Example: NMOS Device Characterization

<p align="center">
  <img src="Images/nmos_inverter.png" alt="NMOS Test Circuit" width="500"/>
  <br>
  <em>Figure 13: NMOS Characterization Test Circuit</em>
</p>

```spice
*Model Description
.param temp=27
*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt
*Netlist Description
XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2
R1 n1 in 55
Vdd vdd 0 1.8V
Vin in 0 1.8V
*simulation commands
.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2
.control
run
display
setplot dc1
.endc
.end
```

**Circuit Description:**

| Entry | Device Type | Function |
|------|-----------|-------------|
| `M1 vdd n1 0 0 ...` | NMOS transistor | Drain=vdd, Gate=n1, Source=GND, Bulk=GND |
| `R1 in n1 55` | Gate resistor | 55Ω between input and gate (current limiting) |
| `Vdd vdd 0 2.5` | Supply voltage | 2.5V DC source |
| `Vin in 0 2.5` | Gate voltage | Gate bias sweep parameter |
| `.dc Vdd 0 2.5 0.01` | DC sweep | V_DS sweep with 10mV increments |

---

## Initial Setup

### System Requirements

- Foundational semiconductor physics knowledge
- Circuit analysis fundamentals
- Linux-based operating system (preferred)

### Software Installation

**Repository Setup:**

```bash
mkdir -p SPICE
git clone https://github.com/kunalg123/sky130CircuitDesignWorkshop.git
cd sky130CircuitDesignWorkshop
```

<p align="center">
  <img src="Images/git_clone.png" alt="Git Clone" width="500"/>
  <br>
  <em>Figure 13: Git Clone
  </em>
</p>

### Directory Organization
```
SPICE/
│
└── sky130CircuitDesignWorkshop/
    └── design/
        ├── day1_nfet_idvds_L2_W5.spice
        ├── day2_nfet_idvgs_L015_W039.spice
        ├── day2_nfet_idvds_L015_W039.spice
        ├── day3_inv_vtc_Wp084_Wn036.spice
        ├── day3_inv_tran_Wp084_Wn036.spice
        ├── day4_inv_noisemargin_wp1_wn036.spice
        ├── day5_inv_supplyvariation_Wp1_Wn036.spice
        ├── day5_inv_devicevariation_wp7_wn042.spice
        │
        └── sky130_fd_pr/
            ├── models/
            │   ├── all.spice
            │   └── sky130.lib.spice
            │
            └── cells/
                ├── nfet_01v8/
                │   ├── sky130_fd_prnfet_01v8.pm3.spice
                │   ├── sky130_fd_prnfet_01v8__tt.pm3.spice
                │   ├── sky130_fd_prnfet_01v8ff.corner.spice
                │   ├── sky130_fd_prnfet_01v8ff.pm3.spice
                │   ├── sky130_fd_prnfet_01v8fs.corner.spice
                │   ├── sky130_fd_prnfet_01v8fs.pm3.spice
                │   ├── sky130_fd_prnfet_01v8sf.corner.spice
                │   ├── sky130_fd_prnfet_01v8sf.pm3.spice
                │   ├── sky130_fd_prnfet_01v8ss.corner.spice
                │   ├── sky130_fd_prnfet_01v8ss.pm3.spice
                │   ├── sky130_fd_prnfet_01v8tt.corner.spice
                │   └── sky130_fd_prnfet_01v8mismatch.corner.spice
                │
                └── pfet_01v8/
                    ├── sky130_fd_prpfet_01v8.pm3.spice
                    ├── sky130_fd_prpfet_01v8__ss.pm3.spice
                    ├── sky130_fd_prpfet_01v8ff.corner.spice
                    ├── sky130_fd_prpfet_01v8ff.pm3.spice
                    ├── sky130_fd_prpfet_01v8fs.corner.spice
                    ├── sky130_fd_prpfet_01v8fs.pm3.spice
                    ├── sky130_fd_prpfet_01v8sf.corner.spice
                    ├── sky130_fd_prpfet_01v8sf.pm3.spice
                    ├── sky130_fd_prpfet_01v8ss.corner.spice
                    ├── sky130_fd_prpfet_01v8tt.corner.spice
                    ├── sky130_fd_prpfet_01v8tt.pm3.spice
                    ├── sky130_fd_prpfet_01v8tt_discrete.corner.spice
                    └── sky130_fd_prpfet_01v8mismatch.corner.spice
```

**Simulator Installation:**

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install ngspice
```

<p align="center">
  <img src="Images/ngspice_install.png" alt="NGSPICE Installation" width="500"/>
  <br>
  <em>Figure 13: NGSPICE Installation
  </em>
</p>

### Executing Initial Simulation

**Launch a SPICE netlist:**

```bash
ngspice day1_nfet_idvds_L2_W5.spice 
```

**Display waveforms:**

```spice
ngspice 1 -> plot -vdd#branch
```

**Export simulation data:**

```spice
ngspice 3 -> write output.raw  #if you wish to generate binary format
ngspice 4 ->wrdata output.csv -vdd#branch  #generates csv files
ngspice 5 -> quit
$less output.csv   #view the csv file
```


### Sample Simulation: NMOS Characteristic Curves

**Netlist (day1_nfet_idvds_L2_W5.spice):**

```spice
*** NMOS DC Characteristic - Sky130 ***

.param temp=27

.lib "models/sky130_fd_pr/sky130.lib.spice" tt

*** Device Under Test ***
XM1 vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2
R1 n1 in 55

*** Voltage Sources ***
Vdd vdd 0 1.8
Vin in 0 1.8

*** Sweep V_DS for multiple V_GS values ***
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control
run
display
setplot dc1
plot -vdd#branch
.endc

.end
```

**Simulation Results:**

<p align="center">
  <img src="Images/iv_waveform.png" alt="NMOS I-V Curves" width="700"/>
  <br>
  <em>Figure 14: Simulated NMOS I-V Characteristics (W=5µm, L=2µm, Sky130)</em>
</p>

- Multiple I_D versus V_DS curves displayed
- Individual curves correspond to distinct V_GS values
- Linear and saturation operational regions clearly visible

---

## Derived NMOS DC Characteristics (output.csv)

| Characteristic | Symbol | Measured Value | Units | Measurement Technique |
|-----------|--------|----------------|------|-------------------|
| **Threshold Voltage (approximate)** | V_th | ~0.45-0.50 | V | Extrapolation from V_GS where I_D becomes significant |
| **Saturation Current (V_GS=0.6V)** | I_Dsat | 194.8 | µA | I_D measured at V_DS = 1.8V |
| **Saturation Current (V_GS=0.8V)** | I_Dsat | 282.6 | µA | I_D measured at V_DS = 1.8V |
| **Saturation Current (V_GS=1.0V)** | I_Dsat | 380.7 | µA | I_D measured at V_DS = 1.8V |
| **On-Resistance Linear Mode (V_GS=0.8V)** | R_on | 1,721 | Ω | V_DS/I_D calculated at V_DS = 0.1V |
| **On-Resistance Linear Mode (V_GS=1.0V)** | R_on | 1,491 | Ω | V_DS/I_D calculated at V_DS = 0.1V |
| **Channel Length Modulation** | λ | 0.034 | V⁻¹ | Saturation region slope |
| **Early Voltage** | V_A | 29.4 | V | 1/λ |
| **Output Conductance (V_GS=0.8V)** | g_ds | 6-9 | µS | ΔI_D/ΔV_DS in saturation region |

## Reference Materials

### Process Technology Files

**Sky130 Model File Paths:**

1. **NMOS Model (Nominal Corner):**
   ```
   models/sky130_fd_pr/cells/nfet_01v8/sky130_fd_pr__nfet_01v8__tt.pm3.spice
   ```

2. **Process Variation Models:**
   ```
   models/sky130_fd_pr/cells/nfet_01v8/sky130_fd_pr__nfet_01v8__tt.corner.spice
   ```

3. **Comprehensive Model Library:**
   ```
   models/sky130_fd_pr/models/sky130.lib.spice
   ```

### Critical Sky130 Model Parameters

| Characteristic | Symbol | Nominal Value | Definition |
|-----------|--------|---------------|-------------|
| Threshold Voltage | V_th | ~0.45V | Gate voltage required for strong inversion |
| Oxide Capacitance | C_ox | ~6.9 fF/µm² | Gate capacitance per unit area |
| Electron Mobility | μ_n | ~400 cm²/V·s | Electron carrier mobility |
| Body Effect Coefficient | γ | ~0.45 V^0.5 | Substrate bias dependency |

### Additional Resources

- **SPICE Documentation**: [Ngspice Manual](http://ngspice.sourceforge.net/docs/ngspice-manual.pdf)
- **Sky130 PDK**: [SkyWater PDK Documentation](https://skywater-pdk.readthedocs.io/)
- **VLSI Design**: *CMOS VLSI Design* by Weste and Harris
- **Original Workshop**: [VSD Hardware Design Program](https://github.com/kunalg123/sky130CircuitDesignWorkshop)

---
