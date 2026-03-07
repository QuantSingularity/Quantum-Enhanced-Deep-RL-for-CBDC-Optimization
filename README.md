# Quantum-Enhanced Deep Reinforcement Learning for CBDC Liquidity Management

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18666885.svg)](https://doi.org/10.5281/zenodo.18666885)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](requirements.txt)
[![PennyLane](https://img.shields.io/badge/PennyLane-Quantum-purple.svg)](requirements.txt)
[![MLflow](https://img.shields.io/badge/MLflow-Tracking-blue.svg)](logs/mlruns)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A production-grade implementation of **Quantum-Enhanced Soft Actor-Critic (QSAC)** for optimizing Central Bank Digital Currency liquidity management under stress scenarios. Integrates Variational Quantum Circuits (VQC) into the SAC algorithm to optimize commercial bank funding strategies under CBDC-induced liquidity shocks.

---

## Table of Contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [Configuration](#configuration)
- [Environment](#environment)
- [Quantum Architecture](#quantum-architecture)
- [Results](#results)
- [Advanced Usage](#advanced-usage)
- [Monitoring](#monitoring)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Overview

| Feature                        | Description                                                              |
| :----------------------------- | :----------------------------------------------------------------------- |
| **Quantum-Enhanced SAC**       | Hybrid quantum-classical critic with 4-qubit Variational Quantum Circuit |
| **Realistic CBDC Environment** | Stochastic liquidity dynamics with jump-diffusion CBDC shocks            |
| **Regulatory Constraints**     | LCR enforcement and capital adequacy requirements                        |
| **Benchmarking**               | Classical SAC, QSAC, and rule-based baselines                            |
| **Statistical Validation**     | Paired t-tests, bootstrap CI, Wilcoxon tests                             |
| **Reproducibility**            | Fixed seeds, MLflow tracking, deterministic training                     |

---

## Quick Start

### Installation

```bash
cd Quantum-Enhanced-Deep-RL-for-CBDC-Optimization

python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
pip install -e .
```

### Training

```bash
# Full experimental suite (Classical SAC, QSAC, rule-based)
python experiments/run_all_experiments.py

# Individual models
python training/train_sac.py --config configs/sac.yaml
python training/train_qsac.py --config configs/qsac.yaml
```

### Evaluation and Analysis

```bash
python training/evaluate.py --model_dir logs/trained_models/
python experiments/ablation_studies.py
python experiments/statistical_tests.py

jupyter notebook notebooks/results_analysis.ipynb
```

---

## Project Structure

```
Quantum-Enhanced-Deep-RL-for-CBDC-Optimization/
├── configs/                    # YAML configuration files
│   ├── default.yaml            # Shared parameters and seeds
│   ├── sac.yaml                # Classical SAC hyperparameters
│   ├── qsac.yaml               # Quantum SAC hyperparameters
│   └── environment.yaml        # Environment dynamics and constraints
├── env/                        # CBDC Liquidity Environment
│   ├── cbdc_env.py             # Gymnasium environment
│   ├── liquidity_dynamics.py   # Stochastic processes
│   ├── constraints.py          # LCR and capital constraints
│   └── reward.py               # Reward function
├── models/                     # Neural network models
│   ├── actor.py                # SAC actor network
│   ├── critic_classical.py     # Classical critic
│   ├── critic_quantum.py       # Quantum-enhanced critic
│   ├── vqc.py                  # Variational Quantum Circuit
│   └── sac_agent.py            # SAC algorithm
├── training/                   # Training and evaluation scripts
│   ├── train_sac.py
│   ├── train_qsac.py
│   ├── evaluate.py
│   └── replay_buffer.py
├── baselines/
│   └── rule_based_policy.py
├── experiments/
│   ├── run_all_experiments.py
│   ├── ablation_studies.py
│   └── statistical_tests.py
├── logs/                       # Generated outputs
│   ├── mlruns/                 # MLflow tracking
│   ├── trained_models/         # Model checkpoints
│   ├── metrics/                # CSV metrics
│   └── plots/                  # Figures
├── notebooks/
│   └── results_analysis.ipynb
└── tests/
    ├── test_environment.py
    ├── test_vqc.py
    └── test_sac.py
```

---

## Configuration

All hyperparameters are managed via YAML configs in `configs/`. Edit these files to customize experiments.

| File               | Purpose                                      |
| :----------------- | :------------------------------------------- |
| `default.yaml`     | Shared parameters, seeds, logging            |
| `sac.yaml`         | Classical SAC hyperparameters                |
| `qsac.yaml`        | Quantum SAC hyperparameters (qubits, layers) |
| `environment.yaml` | Environment dynamics and constraints         |

To reproduce paper results exactly:

```bash
python experiments/run_all_experiments.py  # uses SEED = 42 from default.yaml
```

---

## Environment

### State Space (8D)

| Index | Variable                 |
| :---- | :----------------------- |
| 1     | Current liquidity buffer |
| 2     | Short-term liabilities   |
| 3     | Projected inflows        |
| 4     | Projected outflows       |
| 5     | Interbank funding rate   |
| 6     | CBDC demand shock        |
| 7     | Market volatility proxy  |
| 8     | Previous action          |

### Action Space (3D, Continuous)

| Index | Variable                        | Range           |
| :---- | :------------------------------ | :-------------- |
| 1     | Borrow amount                   | [0, max_borrow] |
| 2     | Liquid asset reallocation ratio | [0, 1]          |
| 3     | Emergency funding decision      | [0, 1]          |

### Stochastic Dynamics

| Process            | Model                                |
| :----------------- | :----------------------------------- |
| Inflows / Outflows | Geometric Brownian Motion            |
| Funding Rates      | Ornstein-Uhlenbeck process           |
| CBDC Shocks        | Jump-diffusion with Poisson arrivals |

### Constraints

- **LCR:** Liquidity Coverage Ratio must remain at or above 100%
- **Capital Adequacy:** Minimum capital buffer enforced
- **Penalties:** Cost penalties applied for constraint violations

---

## Quantum Architecture

### VQC Design

| Parameter            | Value                                         |
| :------------------- | :-------------------------------------------- |
| Qubits               | 4 (configurable)                              |
| Encoding             | RY rotation encoding                          |
| Entanglement         | CNOT ring topology                            |
| Parameterized Layers | 2 layers of RY-RZ rotations                   |
| Measurement          | Pauli-Z expectation values                    |
| Backend              | PennyLane default.qubit (CPU) or Qiskit (GPU) |

### Noise Mitigation

- Zero Noise Extrapolation (ZNE) for hardware noise
- Configurable noise injection for robustness testing

---

## Results

Expected performance improvements of QSAC vs Classical SAC:

| Metric                   | Improvement              |
| :----------------------- | :----------------------- |
| Funding Cost Reduction   | 8-12%                    |
| LCR Violation Reduction  | 15-20%                   |
| Stability Index          | +10-15%                  |
| Statistical Significance | p < 0.01 (paired t-test) |

See `notebooks/results_analysis.ipynb` for full analysis.

---

## Advanced Usage

### Custom Training

```python
from training.train_qsac import train_qsac
from hydra import compose, initialize

initialize(config_path="../configs", version_base=None)
cfg = compose(config_name="qsac")

train_qsac(cfg, n_steps=2000000, eval_freq=10000)
```

### Custom Environment

```python
from env.cbdc_env import CBDCLiquidityEnv

env = CBDCLiquidityEnv(
    initial_liquidity=1000000,
    lcr_threshold=1.0,
    cbdc_shock_intensity=0.3,
    seed=42
)
```

### Load Trained Model

```python
from models.sac_agent import SACAgent

agent = SACAgent.load("logs/trained_models/qsac_final.pt")

obs, _ = env.reset()
done = False
while not done:
    action = agent.select_action(obs, deterministic=True)
    obs, reward, done, truncated, info = env.step(action)
```

### Testing

```bash
pytest tests/ -v
```

---

## Monitoring

```bash
# MLflow UI
mlflow ui --backend-store-uri logs/mlruns
# Open http://localhost:5000

# TensorBoard (optional)
tensorboard --logdir logs/tensorboard
```

---

## Troubleshooting

| Issue                   | Fix                                                                      |
| :---------------------- | :----------------------------------------------------------------------- |
| PennyLane install fails | `pip install pennylane --no-cache-dir`                                   |
| CUDA out of memory      | Reduce `batch_size` or `n_qubits` in config                              |
| Slow training           | Enable GPU, reduce circuit depth, or use classical SAC for iteration     |
| Numerical instability   | Adjust learning rates, increase `target_entropy`, normalize observations |

---

## License

Licensed under the **MIT License**. See [LICENSE](LICENSE) for details.
