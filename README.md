# 4×4 RRAM Crossbar MAC Array (Compute-in-Memory)

## Overview

This project implements a 4×4 RRAM-based crossbar array capable of performing analog multiply–accumulate (MAC) operations using compute-in-memory (CIM) principles.

Instead of moving data between memory and compute units, computation is performed directly inside the memory array, enabling efficient vector–matrix multiplication.

---

## Working Principle

The crossbar computes:

$$
I = G^T V
$$

where:

* $V$ is the input voltage vector
* $G$ is the conductance matrix
* $I$ is the output current vector

At the circuit level, each column computes:

$$
I_j = \sum_{i=1}^{4} G_{ij} V_i
$$

This represents vector–matrix multiplication.

---

## Architecture

The 4×4 crossbar consists of:

* 4 rows (input voltages)
* 4 columns (output currents)
* 16 RRAM cells (programmable weights)
---

          Col1      Col2      Col3      Col4
           │         │         │         │
Row1 ──   R11  ──   R12  ──   R13  ──   R14
           │         │         │         │
Row2 ──   R21  ──   R22  ──   R23  ──   R24
           │         │         │         │
Row3 ──   R31  ──   R32  ──   R33  ──   R34
           │         │         │         │
Row4 ──   R41  ──   R42  ──   R43  ──   R44

---

## Circuit Implementation

### Connections

* Top electrode (TE) of each RRAM is connected to a row input
* Bottom electrode (BE) of each RRAM is connected to a column node

Each column node is connected to a load resistor:

```
Col_j → Rload → GND
```

---

## RRAM Device Model

The RRAM device follows:

$$
I = I_0 e^{-g/g_0} \sinh\left(\frac{V}{V_0}\right)
$$

In the small-signal (read) regime where $V \ll V_0$:

$$
I \approx G \cdot V
$$

with:

$$
G = \frac{I_0}{V_0} e^{-g/g_0}
$$

---

## Weight Programming

Weights are programmed using the internal state variable `gap_ini`.

* Smaller gap → higher conductance
* Larger gap → lower conductance

Typical mapping:

| gap (nm) | Conductance |
| -------- | ----------- |
| 1.2      | ~33 µS      |
| 1.3      | ~22 µS      |
| 1.4      | ~15 µS      |
| 1.5      | ~10 µS      |
| 1.7      | ~4 µS       |

Constraints from the model:

```
gap_min = 0.2 nm
gap_max = 1.7 nm
```

---

## Input Configuration

Example input vector:

```
V1 = 0.20 V
V2 = 0.15 V
V3 = 0.10 V
V4 = 0.05 V
```

To maintain linear behavior, inputs should remain in the read regime:

```
V ≤ 0.2 V
```

---

## Output Interpretation

Column currents are given by:

$$
I_j = \sum_{i=1}^{4} G_{ij} V_i
$$

The output voltage at each column is:

$$
V_{out,j} = I_j \cdot R_{load}
$$

---

## Non-Ideal Effects

### Voltage Drop (Column Loading)

The effective voltage across each RRAM is:

$$
V_{device} = V_{row} - V_{column}
$$

This reduces current and introduces deviation from ideal MAC behavior.

---

### Nonlinear Device Behavior

The current follows a nonlinear relation due to the $\sinh$ term. Linear approximation is valid only at low voltages.

---

### Limited Conductance Range

Due to bounded gap values, the conductance range is limited, affecting weight resolution.

---

## Simulation Setup

Recommended settings:

* Analysis: DC or transient
* Load resistance: 1 kΩ to 10 kΩ
* Input voltages: ≤ 0.2 V
* Moderate rise/fall times for transient signals

---

## Verification Strategy

### Single Row Activation

Activate one row at a time:

```
V1 = 0.2 V, others = 0
```

The output should correspond to the first row of weights.

---

### Superposition Check

Activate multiple rows:

```
V1 and V2 active
```

The output should approximate the sum of individual contributions.

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

This project demonstrates a 4×4 RRAM-based analog MAC array capable of performing vector–matrix multiplication using compute-in-memory principles. It highlights both the potential and practical limitations of analog crossbar-based computation.
