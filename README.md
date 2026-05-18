# PulseForge-QC

## Pulse-Level Quantum Control and Optimization of a Noisy Superconducting Qubit

PulseForge-QC is a quantum control simulation framework that models and optimizes microwave-driven qubit dynamics in the presence of decoherence.

The project combines:

- Open quantum system simulation
- Gaussian pulse engineering
- Lindblad master equation dynamics
- Pulse amplitude optimization
- Bloch sphere visualization
- Qiskit-based ideal gate verification

The framework is designed to demonstrate concepts relevant to:

- quantum control engineering,
- superconducting qubit control,
- pulse-level quantum computing,
- noisy quantum dynamics,
- scientific quantum software development.

---

# Project Motivation

Real quantum computers are controlled using microwave pulses.

In superconducting quantum hardware:
- control electronics generate analog microwave waveforms,
- pulses drive qubit transitions,
- environmental noise introduces decoherence,
- control optimization is required to achieve high-fidelity quantum gates.

This project simulates that workflow computationally.

The goal is to:

> Start from a qubit in the ground state, apply a shaped control pulse, and optimize the pulse to maximize the fidelity of the target quantum state under realistic noise.

---

# Physics Background

---

## Qubit Dynamics

The quantum state evolves according to the Schrödinger equation:

\[
i\frac{d}{dt}|\psi(t)\rangle = H(t)|\psi(t)\rangle
\]

The driven qubit Hamiltonian in the rotating frame is:

\[
H(t)
=
\frac{\Delta}{2}\sigma_z
+
\frac{\Omega(t)}{2}\sigma_x
\]

where:

- \(\Delta\) is the detuning,
- \(\sigma_x\) and \(\sigma_z\) are Pauli operators,
- \(\Omega(t)\) is the microwave pulse envelope.

---

## Gaussian Pulse

The control pulse is modeled using a Gaussian waveform:

\[
\Omega(t)
=
A\exp
\left(
-\frac{(t-t_0)^2}{2\sigma^2}
\right)
\]

Gaussian pulses are commonly used in superconducting qubit systems because they reduce spectral leakage and unwanted transitions.

---

## Open Quantum System Dynamics

Real qubits interact with their environment.

This produces:
- relaxation,
- dephasing,
- decoherence.

The project models noisy dynamics using the Lindblad master equation:

\[
\dot{\rho}
=
-i[H,\rho]
+
\sum_k
\left(
L_k\rho L_k^\dagger
-
\frac12
\{L_k^\dagger L_k,\rho\}
\right)
\]

where:
- \(\rho\) is the density matrix,
- \(L_k\) are collapse operators.

---

## Fidelity Optimization

The project optimizes the pulse amplitude to maximize:

\[
F
=
\langle1|\rho_f|1\rangle
\]

which measures the probability of successfully preparing the target state.

---

# Features

- Gaussian pulse engineering
- Time-dependent Hamiltonian simulation
- Lindblad master equation evolution
- T1 relaxation modeling
- T2 dephasing modeling
- Pulse amplitude optimization
- Bloch sphere expectation-value visualization
- Qiskit-based ideal gate verification
- Modular scientific software architecture

---

# Software Stack

| Library | Purpose |
|---|---|
| QuTiP | Open quantum system simulation |
| Qiskit | Quantum circuit verification |
| NumPy | Numerical computation |
| SciPy | Optimization |
| Matplotlib | Visualization |

---

# Project Structure

```text
PulseForge-QC/
│
├── README.md
├── requirements.txt
├── main.py
│
├── config/
│   └── system_config.py
│
├── pulses/
│   └── pulse_shapes.py
│
├── noise/
│   └── noise_model.py
│
├── simulation/
│   └── pulse_simulator.py
│
├── optimization/
│   └── optimize_pulse.py
│
├── visualization/
│   └── bloch_visualization.py
│
├── qiskit_layer/
│   └── qiskit_verification.py
│
└── results/
```

---

# Module Description

---

## `system_config.py`

Contains:
- physical constants,
- qubit frequencies,
- decoherence times,
- pulse parameters,
- simulation resolution.

---

## `pulse_shapes.py`

Defines:
- Gaussian pulse envelopes,
- control waveform generation.

---

## `noise_model.py`

Constructs:
- Lindblad collapse operators,
- amplitude damping channels,
- dephasing channels.

---

## `pulse_simulator.py`

Core simulation engine.

Responsible for:
- Hamiltonian construction,
- time evolution,
- Lindblad dynamics,
- fidelity evaluation.

---

## `optimize_pulse.py`

Uses numerical optimization to:
- search pulse amplitudes,
- maximize target-state fidelity.

---

## `bloch_visualization.py`

Visualizes:
- Bloch sphere expectation values,
- qubit dynamics over time.

---

## `qiskit_verification.py`

Constructs an ideal X-gate using Qiskit and verifies the ideal target state.

This serves as a comparison between:
- ideal quantum gate behavior,
- noisy pulse-driven dynamics.

---

# Installation

---

## Clone Repository

```bash
git clone https://github.com/yourusername/PulseForge-QC.git

cd PulseForge-QC
```

---

## Create Virtual Environment (Recommended)

### Linux / macOS

```bash
python -m venv venv

source venv/bin/activate
```

### Windows

```bash
python -m venv venv

venv\Scripts\activate
```

---

## Install Dependencies

```bash
pip install -r requirements.txt
```

---

# Running the Project

```bash
python main.py
```

---

# Expected Output

The program produces:

- baseline pulse fidelity,
- optimized pulse amplitude,
- optimized fidelity,
- Bloch sphere dynamics plots,
- Qiskit ideal state verification.

Example:

```text
Baseline fidelity: 0.73

Optimization Results
----------------------
Optimal amplitude: 2.184
Optimal fidelity: 0.992

Reference X-Gate Circuit
     ┌───┐
q_0: ┤ X ├
     └───┘

Ideal Final Statevector:
Statevector([0.+0.j, 1.+0.j],
            dims=(2,))
```

---

# Scientific Concepts Demonstrated

This project demonstrates:

- quantum control theory,
- open quantum systems,
- Lindblad dynamics,
- density matrix evolution,
- pulse engineering,
- microwave-driven qubit control,
- numerical optimization,
- fidelity analysis,
- quantum software architecture.

---

# Relevance to Quantum Control Engineering

The project models several concepts used in real superconducting quantum hardware workflows:

| Simulation Concept | Hardware Interpretation |
|---|---|
| Gaussian pulse | Microwave control waveform |
| Pulse amplitude | DAC voltage amplitude |
| Lindblad noise | Environmental decoherence |
| Fidelity optimization | Gate calibration |
| Bloch dynamics | Qubit state evolution |
| Qiskit verification | Ideal gate reference |

---

# Future Extensions

Potential upgrades include:

- DRAG pulse implementation
- GRAPE optimal control
- Multi-qubit simulation
- Transmon Hamiltonian modeling
- Quantum process tomography
- Closed-loop calibration
- Reinforcement learning control
- Qiskit Dynamics integration
- Hardware backend calibration workflows

---

# Author

Soumyajit Pal

Project focused on:
- quantum control,
- pulse engineering,
- noisy quantum dynamics,
- scientific quantum software development.
