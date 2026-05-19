# PulseForge-QC

## Pulse-Level Quantum Control and Optimization of a Noisy Superconducting Qubit

PulseForge-QC is a compact quantum control simulation framework for modeling, optimizing, and visualizing microwave-driven qubit dynamics under decoherence.

The project is built around a realistic control workflow:

1. define the qubit and pulse parameters,
2. generate a shaped microwave control pulse,
3. evolve the state with open-system dynamics,
4. optimize the pulse amplitude,
5. inspect the Bloch-vector trajectory,
6. compare the result against an ideal Qiskit reference gate.

The codebase is intentionally compact, but the architecture mirrors the same core ideas that appear in pulse-level quantum control software:

- Hamiltonian modeling,
- decoherence-aware simulation,
- waveform engineering,
- numerical optimization,
- and physical interpretation of control dynamics.

---

# Project Motivation

Real superconducting qubits are not controlled by abstract gates alone. They are controlled through carefully engineered microwave pulses whose:

- amplitude,
- phase,
- duration,
- and shape

determine the resulting quantum evolution.

That creates a real control-engineering problem:

- the pulse must rotate the qubit by the correct amount,
- decoherence continuously degrades the state,
- and control parameters must be calibrated to maximize gate fidelity.

This project simulates that workflow computationally.

The goal is physically meaningful:

> Start from a qubit in the ground state, apply a shaped microwave pulse, evolve the system under realistic noise, and optimize the control pulse to maximize population transfer into the target state.

---

# Physics Background

## Driven Qubit Dynamics

A driven two-level system evolves according to the Schrödinger equation:

\[
i\frac{d}{dt}|\psi(t)\rangle = H(t)|\psi(t)\rangle
\]

In the rotating frame, the driven qubit Hamiltonian used in this project is:

\[
H(t)=\frac{\Delta}{2}\sigma_z+\frac{\Omega(t)}{2}\sigma_x
\]

where:

- \(\Delta = \omega_q - \omega_d\) is the detuning,
- \(\sigma_x,\sigma_z\) are Pauli operators,
- \(\Omega(t)\) is the microwave pulse envelope.

The current configuration uses:

\[
\omega_q = \omega_d
\]

so that:

\[
\Delta = 0
\]

This removes off-resonant drift and isolates the effect of:

- the control pulse,
- and the decoherence channels.

That choice makes the dynamics easier to interpret physically.

---

## Gaussian Pulse Engineering

The control field is modeled using a Gaussian pulse:

\[
\Omega(t)=A\exp\left(-\frac{(t-t_0)^2}{2\sigma^2}\right)
\]

where:

- \(A\) is the pulse amplitude,
- \(t_0\) is the pulse center,
- \(\sigma\) controls pulse width.

A Gaussian envelope is not chosen arbitrarily.

Smooth pulses are widely used in superconducting-qubit control because abrupt waveform discontinuities introduce high-frequency spectral components that can excite unwanted transitions and distort the control dynamics.

So even this simple pulse already reflects an important hardware-aware control principle:

> smoother pulses generally produce cleaner dynamics.

---

## Open Quantum Systems

Real qubits are not isolated systems.

They interact with:

- electromagnetic environments,
- substrate defects,
- control electronics,
- thermal noise,
- and surrounding circuitry.

As a result:

- energy decays,
- phase coherence is lost,
- and gate operations become imperfect.

The project models these effects using the Lindblad master equation:

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

This formalism is important because pure state evolution alone is insufficient for realistic pulse simulation.

The density matrix framework captures:

- coherent dynamics,
- population transfer,
- relaxation,
- and decoherence simultaneously.

---

## Relaxation and Dephasing

The simulation includes two standard decoherence channels.

### T1 Relaxation

Relaxation transfers population:

\[
|1\rangle \rightarrow |0\rangle
\]

This directly competes with the goal of preparing the excited state.

Even if the pulse initially drives the qubit upward, relaxation continuously pulls population back toward the ground state.

---

### T2 Dephasing

Dephasing destroys phase coherence:

\[
\rho_{01}\rightarrow 0
\]

without necessarily changing the populations immediately.

This weakens coherent rotations and suppresses interference effects required for accurate state preparation.

---

## Fidelity

The target state is:

\[
|1\rangle
\]

The simulator evaluates the final-state fidelity:

\[
F=\langle1|\rho_f|1\rangle
\]

which measures how successfully the pulse prepares the excited state.

The optimization routine attempts to maximize this quantity.

---

## Bloch-Sphere Observables

The expectation values:

\[
\langle X\rangle,\quad
\langle Y\rangle,\quad
\langle Z\rangle
\]

represent the Cartesian coordinates of the qubit on the Bloch sphere.

Physically:

- \(\langle Z\rangle\) tracks population inversion,
- \(\langle X\rangle\) and \(\langle Y\rangle\) track quantum coherence,
- transverse decay indicates decoherence,
- coherent oscillations indicate active pulse-driven control.

Monitoring these observables provides much more physical insight than looking only at the final fidelity.

---

# Features

- Gaussian pulse engineering
- Time-dependent Hamiltonian simulation
- Lindblad master equation evolution
- T1 relaxation modeling
- T2 dephasing modeling
- Pulse amplitude optimization
- Bloch-sphere trajectory visualization
- Qiskit-based ideal gate verification
- Modular scientific software architecture

---

# Software Stack

| Library | Purpose |
|---|---|
| QuTiP | Open quantum system simulation |
| Qiskit | Quantum circuit verification |
| NumPy | Numerical computation |
| SciPy | Numerical optimization |
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

# Module Breakdown

## `system_config.py`

Contains all physical and numerical parameters:

- qubit frequency,
- drive frequency,
- decoherence times,
- pulse duration,
- Gaussian width,
- simulation resolution.

The resonant-drive condition is important because it removes unnecessary detuning effects and isolates the control dynamics.

---

## `pulse_shapes.py`

Defines the Gaussian pulse envelope.

The pulse shape is separated into its own module because waveform generation is a core abstraction in pulse-level control systems.

---

## `noise_model.py`

Constructs Lindblad collapse operators for:

- relaxation,
- and dephasing.

These operators encode irreversible interactions between the qubit and the environment.

Without them, the simulation would represent an idealized closed system rather than realistic noisy hardware.

---

## `pulse_simulator.py`

Core simulation engine.

Responsible for:

- Hamiltonian construction,
- time-dependent control evolution,
- Lindblad dynamics,
- fidelity evaluation.

The simulation connects pulse design directly to physical outcomes.

This is the heart of the project.

---

## `optimize_pulse.py`

Uses numerical optimization to tune the pulse amplitude.

The optimizer is not just searching for a better number.

It is effectively searching for the control strength that best balances:

- coherent rotation,
- decoherence losses,
- and pulse duration.

The optimization landscape is nontrivial because coherent control and decoherence compete against each other throughout the evolution.

---

## `bloch_visualization.py`

Plots:

- \(\langle X\rangle\),
- \(\langle Y\rangle\),
- \(\langle Z\rangle\)

throughout the pulse evolution.

This is critical because the Bloch trajectory reveals *how* the state evolves, not just whether the final fidelity is high or low.

---

## `qiskit_verification.py`

Constructs an ideal single-qubit X gate using Qiskit.

This provides a clean reference against which the noisy pulse-driven evolution can be compared.

The distinction between:
- ideal gate evolution,
- and realistic pulse dynamics

is one of the central ideas in quantum control.

---

# Installation

## Clone Repository

```bash
git clone https://github.com/SoumyajitPal-2210/PulseForge-QC.git

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

The execution pipeline performs:

1. baseline pulse simulation,
2. pulse optimization,
3. optimized pulse re-simulation,
4. Bloch-sphere visualization,
5. Qiskit reference verification.

---

# Example Runtime Output

```text
==============================
 QUANTUM CONTROL SIMULATION
==============================

Baseline fidelity: 0.312912

Optimization Results
----------------------
Optimal amplitude: 0.662160
Optimal fidelity: 0.577298

Reference X-Gate Circuit
     ┌───┐
q_0: ┤ X ├
     └───┘

Ideal Final Statevector:
Statevector([0.+0.j, 1.+0.j],
            dims=(2,))
```

---

# Results and Physical Interpretation

The project was executed end-to-end using the current system parameters.

Observed output:

```text
==============================
 QUANTUM CONTROL SIMULATION
==============================

Baseline fidelity: 0.312912

Optimization Results
----------------------
Optimal amplitude: 0.662160
Optimal fidelity: 0.577298
```

Generated output:

```text
results/bloch_dynamics.png
```

---

## Baseline Fidelity Analysis

The initial fidelity:

\[
F \approx 0.31
\]

is relatively poor.

This means the original Gaussian pulse does not produce an accurate qubit inversion under the current noisy dynamics.

That outcome is physically meaningful.

The pulse must simultaneously:

- rotate the qubit correctly,
- avoid overshooting,
- and compete against decoherence.

A naive pulse amplitude rarely succeeds immediately in realistic control problems.

So the low baseline fidelity is not evidence of a broken simulator. It is evidence that calibration matters.

---

## Optimization Behavior

After optimization:

\[
F \approx 0.58
\]

The optimizer significantly improves the state-transfer performance.

This confirms several things:

- the simulator responds correctly to pulse parameters,
- the control landscape contains recoverable structure,
- and the optimization loop is functioning properly.

However, the fidelity still remains far below fault-tolerant thresholds.

That limitation is expected because the current control model is intentionally minimal.

Only one parameter is optimized:

- pulse amplitude.

Everything else remains fixed:

- pulse width,
- pulse phase,
- pulse center,
- pulse family,
- and drive structure.

So the optimizer is solving a constrained control problem rather than a fully expressive one.

---

## Bloch-Sphere Analysis

The Bloch trajectory is the most physically informative part of the simulation.

### Why \(\langle X\rangle\) remains near zero

The control Hamiltonian is proportional to:

\[
H(t)\propto \sigma_x
\]

A Hamiltonian generated by \(\sigma_x\) produces rotations *around the x-axis* of the Bloch sphere.

That means:
- the x-component is approximately conserved,
- while the y-z plane undergoes rotation.

This is exactly what appears in the simulation.

So the near-zero behavior of:

\[
\langle X\rangle
\]

is actually an important sanity check.

It confirms that:
- the Hamiltonian implementation is internally consistent,
- the pulse coupling direction is correct,
- and the Bloch dynamics match the expected geometry of the control operator.

---

### Why \(\langle Y\rangle\) develops strong oscillations

Initially the qubit starts near:

\[
\langle Z\rangle \approx +1
\]

corresponding to the ground state:

\[
|0\rangle
\]

As the pulse drives the system, coherent superpositions begin to form.

That coherence appears as a strong transient signal in:

\[
\langle Y\rangle
\]

The large negative excursion indicates active coherent rotation generated by the control pulse.

Later:

\[
\langle Y\rangle
\]

decays back toward zero.

That decay is physically important because it reflects:
- dephasing,
- coherence loss,
- and environmental damping during the pulse evolution.

So the plot is not merely showing oscillation — it is directly visualizing coherence generation and coherence decay.

---

### Why \(\langle Z\rangle\) never reaches -1

The quantity:

\[
\langle Z\rangle
\]

tracks population inversion.

A perfect X-gate would ideally drive:

\[
\langle Z\rangle \rightarrow -1
\]

which corresponds to successful preparation of:

\[
|1\rangle
\]

In the simulation, \(\langle Z\rangle\) decreases substantially but never fully reaches -1.

Instead:
- the trajectory bends,
- partially relaxes,
- and settles at a less negative value.

That behavior explains the limited fidelity directly.

The pulse is producing partial inversion, but:
- decoherence removes population,
- coherence decays during the drive,
- and the restricted pulse model cannot fully compensate.

---

## Most Important Physical Insight

The key result is:

> the optimization is not failing numerically — the control model itself is limited.


The optimizer successfully finds the best amplitude *within the chosen pulse family*.

But a single Gaussian amplitude parameter cannot fully compensate for:
- decoherence,
- phase accumulation,
- control distortion,
- and imperfect trajectories.

This is exactly why real quantum-control pipelines use:
- DRAG pulses,
- GRAPE optimization,
- CRAB methods,
- closed-loop calibration,
- hardware transfer-function correction,
- and multi-parameter waveform optimization.

---

# Critical Assessment

The current implementation is intentionally simple, but physically meaningful.

What the project already demonstrates well:

- pulse-driven qubit control,
- open-system dynamics,
- decoherence-aware simulation,
- fidelity optimization,
- Bloch-sphere diagnostics,
- ideal-versus-noisy gate comparison.

What still limits the current performance:

- only one control degree of freedom is optimized,
- the pulse family is fixed,
- no phase calibration is included,
- no DRAG correction is used,
- the model is single-qubit only,
- the noise model is generic rather than hardware-specific.

Those limitations are actually useful because they expose the real challenges of quantum control instead of artificially producing unrealistically high fidelities.

---

# Scientific Concepts Demonstrated

This project demonstrates:

- quantum control theory,
- pulse-level quantum dynamics,
- open quantum systems,
- Lindblad evolution,
- density matrix simulation,
- coherence and decoherence,
- microwave-driven qubit control,
- numerical optimization,
- Bloch-sphere diagnostics,
- fidelity analysis.

---

# Future Extensions

Potential upgrades include:

- DRAG pulse implementation
- optimization of phase and pulse width
- GRAPE optimal control
- CRAB optimization
- multi-qubit dynamics
- transmon Hamiltonian modeling
- stochastic noise channels
- pulse scheduling
- calibration-loop simulation
- Qiskit Dynamics integration
- quantum process tomography

---

# Requirements

```text
numpy>=1.24
scipy>=1.10
matplotlib>=3.7
qutip>=5.0
qiskit>=1.0
```

---

# Author

Soumyajit Pal

Project focus:
- pulse-level quantum control,
- open quantum systems,
- noisy quantum dynamics,
- scientific quantum software,
- numerical optimization.
