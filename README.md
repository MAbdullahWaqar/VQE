# VQE — Variational Quantum Eigensolver for Nuclear Binding Energies

## Overview

This project applies the **Variational Quantum Eigensolver (VQE)** algorithm to compute nuclear ground-state binding energies using quantum simulation. The implementation focuses on the **deuteron nucleus**, constructing its Hamiltonian in a harmonic oscillator basis via second quantization and mapping it to qubit operators through the Jordan-Wigner transformation. By leveraging Qiskit's quantum simulation stack, the project demonstrates how near-term quantum hardware (and classical simulators) can be used to tackle problems in nuclear structure physics. The research supporting this work targets medium-to-heavy nuclei and is documented in an accompanying research article.

---

## Key Features

- Constructs the deuteron Hamiltonian in a truncated harmonic oscillator basis for configurable matrix sizes (N = 1, 2, 3)
- Encodes fermionic operators using the Jordan-Wigner mapping via `qiskit-nature`
- Applies the `TwoLocal` parameterized ansatz with RZ/RY rotation layers and CX entanglement
- Minimizes expectation values using the SLSQP classical optimizer
- Runs on Qiskit Aer's `statevector_simulator` for exact, noise-free simulation
- Includes a generalized VQE script for arbitrary Pauli-sum Hamiltonians

---

## Tech Stack

| Category   | Technology                                                              |
|------------|-------------------------------------------------------------------------|
| Language   | Python 3.10                                                             |
| Framework  | Qiskit (qiskit-terra 0.24.1, qiskit-aer 0.12.2)                        |
| Libraries  | qiskit-nature 0.6.2, qiskit-algorithms 0.2.1, NumPy 1.23.5, SciPy 1.9.3 |
| Simulator  | Qiskit Aer — `statevector_simulator`                                    |
| Tools      | Python virtual environment (`venv`)                                     |

---

## Installation

### Prerequisites

- Python 3.10
- `pip`

### Steps

```bash
# 1. Clone the repository
git clone https://github.com/vanix056/VQE.git
cd VQE

# 2. Create and activate a virtual environment
python3.10 -m venv venv
source venv/bin/activate        # macOS / Linux
venv\Scripts\activate           # Windows

# 3. Install dependencies
pip install -r Requirements.txt
```

---

## Usage

### Compute Deuteron Binding Energies (H₁, H₂, H₃)

```bash
python VQE_BE.py
```

**Sample output:**
```
H_1 binding energy ≈ -1.749... MeV
H_2 binding energy ≈ -2.032... MeV
H_3 binding energy ≈ -2.224... MeV
```

Each Hₙ corresponds to an N-qubit truncation of the deuteron Hamiltonian. The VQE ansatz depth scales with N (`reps=i`).

### Run the Generalized Ground State Energy Script

```bash
python VQE_GE.py
```

This script demonstrates VQE on user-defined Pauli-sum Hamiltonians and is intended as a configurable template for extending the approach to other operators.

---

## Project Structure

```
VQE/
├── VQE_BE.py              # Deuteron Hamiltonian construction + VQE binding energy computation
├── VQE_GE.py              # Generalized VQE ground state energy script
├── Requirements.txt       # Python dependency list
├── Reseach Article/       # Supporting research paper (DOCX)
│   └── Quantum Computing for Nuclear Binding Energies_ Medium-Heavy-Nuclei.docx
└── qiskit-fix/            # Local virtual environment (not tracked)
```

---

## Model Architecture

### Hamiltonian Construction

The deuteron Hamiltonian is formulated in the harmonic oscillator (HO) basis:

- **Kinetic term**: Matrix elements derived from HO ladder operators, scaled by ℏω = 7.0 MeV
- **Potential term**: Contact interaction V₀ = −5.68658111 applied at the (0,0) matrix element
- **Mapping**: Jordan-Wigner transformation converts the fermionic Hamiltonian to a qubit operator via `qiskit-nature`

### Ansatz

`TwoLocal` circuit with:
- Rotation layers: `RZ` + `RY`
- Entanglement: `CX` gates
- Repetitions (`reps`): scales with truncation size N

### Optimizer

**SLSQP** (Sequential Least Squares Programming) — gradient-based classical optimizer from `scipy.optimize`.

---

## Dataset

No external dataset is required. The Hamiltonian matrix elements are computed analytically from the harmonic oscillator basis matrix element formulas:

```
T(m,n) = (ℏω/2) × [(2m + 3/2)δ(m,n) − √(m(m+1/2))δ(m,n+1) − √((m+1)(m+3/2))δ(m,n-1)]
V(m,n) = V₀ × δ(m,0)δ(n,0)
```

---

## Training

VQE does not involve a traditional training loop. The optimization proceeds as follows:

1. Parameterized ansatz circuit is initialized with random parameters
2. Expectation value ⟨ψ(θ)|H|ψ(θ)⟩ is evaluated on the statevector simulator
3. SLSQP iteratively minimizes the expectation value with respect to circuit parameters θ
4. Convergence yields the approximate ground-state energy

---

## Evaluation Metrics

| Metric              | Description                                                 |
|---------------------|-------------------------------------------------------------|
| Ground-state energy | Minimum eigenvalue of the Hamiltonian (in MeV)              |
| Convergence         | SLSQP optimizer convergence status and number of iterations |
| Truncation error    | Deviation from exact diagonalization as N increases         |

---

## Results

The VQE simulation produces approximate ground-state binding energies for increasing basis truncations of the deuteron:

| Hamiltonian | Basis Size | Expected Binding Energy |
|-------------|------------|------------------------|
| H₁          | 1 qubit    | ≈ −1.749 MeV           |
| H₂          | 2 qubits   | ≈ −2.032 MeV           |
| H₃          | 3 qubits   | ≈ −2.224 MeV           |

Energies converge toward the experimental deuteron binding energy of **−2.2246 MeV** as the basis size increases, consistent with results from nuclear structure literature.

---

## Configuration

All physical and simulation parameters are defined directly in `VQE_BE.py`:

| Parameter     | Default Value    | Description                                    |
|---------------|------------------|------------------------------------------------|
| `N`           | 1, 2, 3          | Harmonic oscillator basis truncation size      |
| `hbar_omega`  | `7.0`            | HO frequency in MeV                            |
| `V0`          | `-5.68658111`    | Nucleon-nucleon contact interaction strength   |
| `backend`     | `statevector_simulator` | Qiskit Aer backend for simulation        |
| `reps`        | `i` (= N)        | Depth of TwoLocal ansatz                       |

---

## Contributing

Contributions are welcome. To contribute:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes with descriptive messages
4. Push to your fork and open a Pull Request

Please ensure new code follows the existing style and includes docstrings for any new functions.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

## Author

**vanix056**  
Quantum Computing & Computational Nuclear Physics Researcher  
GitHub: [@vanix056](https://github.com/vanix056)

> *Research article: "Quantum Computing for Nuclear Binding Energies: Medium-Heavy Nuclei"*
