# 4×4 RRAM Crossbar MAC Array (Compute-in-Memory)

## Overview

This project implements a **4×4 RRAM-based crossbar array** capable of performing **analog Multiply–Accumulate (MAC)** operations using **Compute-in-Memory (CIM)** principles.

Instead of moving data between memory and compute units, this design performs computation **directly inside the memory array**, enabling efficient vector–matrix multiplication.

---

## Working Principle

The crossbar computes:

[
I = G^T V
]

Where:

* (V) → input voltages (rows)
* (G) → conductance matrix (stored weights)
* (I) → output currents (columns)

Each column performs:

[
I_j = \sum_{i=1}^{4} G_{ij} V_i
]

This is equivalent to **vector–matrix multiplication**.

---

## Architecture

### 4×4 Crossbar Structure

* **4 Rows → Inputs (Voltages)**
* **4 Columns → Outputs (Currents)**
* **16 RRAM Cells → Weights**

```
          Col1      Col2      Col3      Col4
           │         │         │         │
Row1 ──   R11  ──   R12  ──   R13  ──   R14
           │         │         │         │
Row2 ──   R21  ──   R22  ──   R23  ──   R24
           │         │         │         │
Row3 ──   R31  ──   R32  ──   R33  ──   R34
           │         │         │         │
Row4 ──   R41  ──   R42  ──   R43  ──   R44
```

---

##  Circuit Implementation

### Connections

* **TE (Top Electrode)** → Row input voltage
* **BE (Bottom Electrode)** → Column node

Each column is connected to a load resistor:

```
Col_j → Rload → GND
```

---

## RRAM Model

The RRAM device follows:

[
I = I_0 e^{-g/g_0} \sinh\left(\frac{V}{V_0}\right)
]

In small-signal regime:

[
I \approx G \cdot V
]

Where:

[
G = \frac{I_0}{V_0} e^{-g/g_0}
]

---

## Weight Programming (gap_ini)

Weights are stored by setting:

```
gap_ini
```

Smaller gap → higher conductance → larger weight

### Example Mapping

| gap (nm) | Conductance |
| -------- | ----------- |
| 1.2 nm   | ~33 µS      |
| 1.3 nm   | ~22 µS      |
| 1.4 nm   | ~15 µS      |
| 1.5 nm   | ~10 µS      |
| 1.7 nm   | ~4 µS       |

---

## Input Configuration

Example input vector:

```
V1 = 0.20 V
V2 = 0.15 V
V3 = 0.10 V
V4 = 0.05 V
```

Use **small voltages (≤ 0.2 V)** to ensure read mode (no switching).

---

## Output Interpretation

Column currents:

[
I_j = G_{1j}V_1 + G_{2j}V_2 + G_{3j}V_3 + G_{4j}V_4
]

Column voltages:

[
V_j = I_j \cdot R_{load}
]

---

## Simulation Setup

* Analysis: DC / Transient
* Recommended:

  * Rload = 1kΩ – 10kΩ
  * Input voltages ≤ 0.2 V
  * Moderate pulse rise/fall times

---

## Non-Idealities Observed

### 1. Column Loading (IR Drop)

[
V_{device} = V_{row} - V_{column}
]

This reduces effective voltage across RRAM.

---

### 2. Nonlinear Device Behavior

Due to:

[
\sinh(V/V_0)
]

MAC deviates from ideal linear behavior at higher voltages.

---

### 3. Limited Conductance Range

Due to:

```
gap_min = 0.2 nm
gap_max = 1.7 nm
```

---

## Verification Strategy

### Test 1: Single Row Activation

```
V1 = 0.2 V, others = 0
```

Output should match **Row1 weights**.

---

### Test 2: Superposition

```
V1 + V2 active
```

Output ≈ sum of individual contributions.

---

## Key Features

✔ Analog matrix multiplication
✔ RRAM-based weight storage
✔ Scalable crossbar architecture
✔ No digital multipliers required

---

## Project Structure

```
/verilogA
    rram_model.va
/schematics
    4x4_crossbar
/results
    waveforms
```

---

## Conclusion

This project demonstrates a **4×4 RRAM-based analog MAC array**, a fundamental building block for:

* AI accelerators
* Neuromorphic systems
* In-memory computing architectures

It highlights both the **power and challenges** of analog CIM systems.

---

