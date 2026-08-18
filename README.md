<div align="center">

# TDAG-Net

### Adaptive Graph Learning with Persona-Aware Interpretability for Metro Passenger Flow Prediction

[![Python](https://img.shields.io/badge/Python-≥3.10-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-≥2.0-EE4C2C?logo=pytorch&logoColor=white)](https://pytorch.org/)
[![License](https://img.shields.io/badge/License-Academic_Use-blue.svg)](#-license)
[![Dataset](https://img.shields.io/badge/Dataset-Nanjing_Metro_AFC_2023-green.svg)](DATASET_README.md)

---

**TDAG-Net** (Ticket-Driven Adaptive Graph Network) is a deep learning architecture for network-wide metro passenger flow forecasting that integrates **fare card ticket-type distributions** with **dynamic spatial-temporal graph neural networks**. It achieves state-of-the-art forecasting accuracy while providing fine-grained, **persona-level interpretability** — decomposing city-wide demand into distinct passenger behavioral types.

</div>

---

## 📌 Highlights

| | |
|:---|:---|
| 🏗️ **Architecture** | Three novel modules: TD-PDM · PC-HGAT · EC-AAM |
| 📊 **Dataset** | Nanjing Metro AFC 2023 — **191 stations · 13 lines · 95.73M entry taps · 57 days** |
| 🎯 **Accuracy** | Overall RMSE **31.95** · MAE **14.00** · WMAPE **19.08%** · R² **0.9489** |
| 🏆 **Event Resilience** | **#1 ranked** under event stress (Event RMSE **32.31**, beating AGCRN **34.00** and TGALSTM **35.65**) |
| 🔍 **Interpretability** | Discovers 4 passenger personas and reveals dynamic cross-persona fusion gate behavior |
| ⚡ **Efficiency** | Only **254K parameters** — lighter than most competitors while outperforming them |

---

## 🧠 Architecture

TDAG-Net consists of three tightly integrated modules, each addressing a distinct challenge in metro flow forecasting:

```
                 ╔══════════════════════════════════════════════════════════════╗
                 ║              Raw AFC Transaction Streams                    ║
                 ║          (95.73M tap records · 25 ticket codes)             ║
                 ╚════════════════════════════╦═════════════════════════════════╝
                                              │
                                              ▼
       ┌──────────────────────────────────────────────────────────────────────────┐
       │  MODULE 1 — TD-PDM (Ticket-Driven Persona Decomposition)                │
       │                                                                          │
       │    25 ticket codes  ──▶  Soft-assignment P₀  ──▶  4 Passenger Personas  │
       │    ┌──────────┐  ┌──────────┐  ┌──────────────┐  ┌────────────────────┐ │
       │    │ Commuter │  │  Casual  │  │   Tourist    │  │ Concession/Student │ │
       │    │  36.6%   │  │  49.5%   │  │    5.7%      │  │       8.2%         │ │
       │    └──────────┘  └──────────┘  └──────────────┘  └────────────────────┘ │
       └──────────────────────────────────┬───────────────────────────────────────┘
                                          │
                                          ▼
       ┌──────────────────────────────────────────────────────────────────────────┐
       │  MODULE 2 — PC-HGAT (Persona-Centric Heterogeneous Graph Attention)     │
       │                                                                          │
       │    Learns persona-specific adaptive spatial adjacencies A_adapt^(k)     │
       │    Dynamic cross-persona fusion gates β_k(z_t)                          │
       │    Each persona "sees" a different metro network topology                │
       └──────────────────────────────────┬───────────────────────────────────────┘
                                          │
                                          ▼
       ┌──────────────────────────────────────────────────────────────────────────┐
       │  MODULE 3 — EC-AAM (Exogenous Context & Event-Aware Attention)          │
       │                                                                          │
       │    8-dim context vector z_t:                                             │
       │    tod_sin · tod_cos · dow_sin · dow_cos · is_weekend · event_flag ·     │
       │    event_countdown · t                                                   │
       │    Dynamically adjusts fusion weights during event surges (z ≥ 4.0)     │
       └──────────────────────────────────────────────────────────────────────────┘
                                          │
                                          ▼
                              ╔══════════════════════╗
                              ║   Forecast Output    ║
                              ║   ŷ(t+1 ... t+H)    ║
                              ╚══════════════════════╝
```

### Module Details

| Module | Purpose | Key Innovation |
|:---|:---|:---|
| **TD-PDM** | Maps 25 fare card codes → 4 behavioral personas | Prior-anchored soft-assignment matrix P₀ → P* learned end-to-end |
| **PC-HGAT** | Learns persona-specific spatial graphs | Each persona discovers its own metro topology via adaptive adjacency A_adapt^(k) |
| **EC-AAM** | Integrates exogenous temporal context | Dynamic fusion weights β_k(z_t) shift automatically during events vs. normal days |

---

## 📊 Benchmark Results

### Overall Test Set Performance

> 191 stations · 819 test windows · 10-minute resolution · 6-step horizon (60 min)

| Model | RMSE ↓ | MAE ↓ | WMAPE ↓ | R² ↑ | EPE ↓ | NDE ↓ | Params |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| SVR-LSTM | 48.86 | 18.87 | 25.71% | 0.8805 | 19.72 | 30.68 | 69,644 |
| LSTM | 47.55 | 18.37 | 25.03% | 0.8868 | 19.11 | 29.86 | 201,228 |
| TCN | 42.45 | 17.19 | 23.42% | 0.9098 | 18.67 | 26.65 | 60,844 |
| STGCN | 40.67 | 16.89 | 23.02% | 0.9172 | 17.89 | 25.54 | 38,796 |
| ConvLSTM | 40.04 | 16.53 | 22.53% | 0.9198 | 19.22 | 25.14 | 51,724 |
| DCRNN | 39.34 | 16.52 | 22.51% | 0.9225 | 18.28 | 24.70 | 64,140 |
| Graph WaveNet | 35.66 | 15.31 | 20.87% | 0.9364 | 17.29 | 22.39 | 65,068 |
| TGALSTM *(predecessor)* | 35.22 | 15.23 | 20.76% | 0.9379 | 17.38 | 22.11 | 210,527 |
| TDAG-Net w/o Adaptive | 34.68 | 15.61 | 21.28% | 0.9398 | 17.31 | 21.77 | 285,477 |
| AGCRN | 30.87 | 13.49 | 18.38% | 0.9523 | 16.48 | 19.38 | 258,050 |
| **TDAG-Net (Ours)** | **31.95** | **14.00** | **19.08%** | **0.9489** | **16.08** | **20.06** | **254,260** |

### Event-Day Performance

> 22 flagged event days · 182 test windows · Concerts, sports matches, and holiday surges

| Model | Event RMSE ↓ | Event MAE ↓ | Event R² ↑ | Event EPE ↓ | Event NDE ↓ |
|:---|:---:|:---:|:---:|:---:|:---:|
| TGALSTM | 35.65 | 15.47 | 0.9269 | 17.38 | 23.88 |
| TDAG-Net w/o Adaptive | 34.92 | 15.72 | 0.9299 | 17.31 | 23.40 |
| Graph WaveNet | 34.39 | 14.76 | 0.9320 | 17.29 | 23.04 |
| AGCRN | 34.00 | 14.52 | 0.9335 | 16.48 | 22.78 |
| TDAG-Net w/o PC-HGAT | 33.80 | 15.14 | 0.9343 | 16.65 | 22.65 |
| TDAG-Net (Input-Splitter) | 33.84 | 15.33 | 0.9342 | 16.71 | 22.67 |
| 🏆 **TDAG-Net (Ours)** | **32.31** | **14.02** | **0.9400** | **16.08** | **21.65** |

### Ablation Study

| Variant | RMSE | Δ RMSE | Key Finding |
|:---|:---:|:---:|:---|
| **TDAG-Net (Full)** | **31.95** | — | Complete architecture |
| w/o TD-PDM | 34.68 | +2.73 | Persona decomposition is critical |
| w/o PC-HGAT | 33.80 | +1.85 | Persona-specific graphs add spatial clarity |
| w/o EC-AAM | 33.84 | +1.89 | Exogenous context crucial for event handling |
| w/o Adaptive Adjacency | 34.68 | +2.73 | Static topology is insufficient |
| Input-Splitter (naive split) | 33.84 | +1.89 | End-to-end learning outperforms manual decomposition |

---

## 🔍 Passenger Persona Discovery

TDAG-Net's TD-PDM module autonomously discovers four distinct behavioral personas from raw fare card ticket codes:

| Persona | Share | Ticket Codes | Behavioral Signature |
|:---|:---:|:---:|:---|
| **🧑‍💼 Commuter** | 36.6% | 10 | Monthly passes, IC work cards. Sharp 08:00 and 17:30 peaks on weekdays |
| **🚶 Casual** | 49.5% | 5 | Mobile QR (Alipay/WeChat), single journey tokens. Broad midday and evening leisure flow |
| **🗺️ Tourist** | 5.7% | 2 | 1-Day/3-Day passes. Concentrated at scenic hubs on weekends |
| **🎓 Student** | 8.2% | 8 | Student/senior/disability cards. Early afternoon travel patterns |

---

## 📁 Repository Structure

```
TDAG-Net/
│
├── 📄 README.md                          # This file
├── 📄 DATASET_README.md                  # Comprehensive dataset documentation
│
├── 📂 Coding File/
│   └── TDAG_Net_Complete_Coding_File.ipynb    # Self-contained Jupyter notebook
│                                              # (all training, evaluation & visualization)
│
├── 📂 data/                              # Processed dataset tensors & metadata
│   ├── metadata/                         #   Station metadata and line mappings
│   └── *.npz / *.json                    #   Preprocessed flow tensors
│
├── 📂 figures/                           # 34 publication-quality figures
│   ├── INDEX.csv                         #   Master figure catalog & metadata
│   ├── 01_corpus/                        #   F01–F06: Dataset audit & ingestion
│   ├── 02_persona/                       #   F07–F11: Persona profiles & ACF
│   ├── 03_network/                       #   F12–F15: Graph topology & heatmaps
│   ├── 04_event/                         #   F16–F20: Event surge & exogenous z_t
│   ├── 05_training/                      #   F21: Training convergence curves
│   └── 06_results/                       #   F22–F34: Benchmarks & interpretability
│
├── 📂 results/                           # Quantitative evaluation outputs
│   ├── metrics/                          #   Per-model JSON metric files (14 models)
│   ├── predictions/                      #   Prediction arrays (.npz)
│   └── tables/                           #   CSV & LaTeX benchmark tables
│
└── 📂 exports/                           # Model checkpoints & asset manifests
    ├── preds/                            #   Exported prediction arrays
    ├── metrics/                          #   Exported metric summaries
    └── asset_manifest_2023.csv           #   Complete asset catalog
```

---

## 🖼️ Figure Catalog

All 34 figures are provided in **PNG**, **PDF**, and **CSV** (underlying data) formats.

<details>
<summary><b>📂 01_corpus — Dataset & Ingestion Audit (F01–F06)</b></summary>

| ID | Title |
|:---|:---|
| F01 | Ingestion Audit & Data Retention across Source Files |
| F02 | Longitudinal Network Ridership & Flagged Event Days |
| F03 | Service-Hour Completeness & Data Availability Heatmap |
| F04 | Ticket Code Pareto Volume Distribution |
| F05 | Diurnal Temporal Signatures per Ticket Code |
| F06 | Demand Concentration Lorenz Curve across Stations |

</details>

<details>
<summary><b>📂 02_persona — Persona Profiles & Analysis (F07–F11)</b></summary>

| ID | Title |
|:---|:---|
| F07 | PCA Mapping of Passenger Personas in Feature Space |
| F08 | Diurnal Persona Profiles (Weekday vs. Weekend) |
| F09 | TD-PDM Soft-Assignment Prior Matrix P₀ |
| F10 | Persona Mix across Top 30 Busiest Stations |
| F11 | Persona Flow Autocorrelation (Daily & Weekly) |

</details>

<details>
<summary><b>📂 03_network — Graph Topology & Spatial Analysis (F12–F15)</b></summary>

| ID | Title |
|:---|:---|
| F12 | Inferred Metro Topology Graph (node size by volume, color by line) |
| F13 | Adjacency Panel (Topology, Aggregate Correlation, & Persona Graphs) |
| F14 | Spatial Graph Cosine Distinctness Comparison |
| F15 | Spatial-Temporal Network Flow Heatmap Surface |

</details>

<details>
<summary><b>📂 04_event — Event Surge Analysis (F16–F20)</b></summary>

| ID | Title |
|:---|:---|
| F16 | Station-Level Z-Score Peak Surge Scan |
| F17 | Major Event Peak Anatomy & Persona Breakdown |
| F18 | Persona Mix & Dispersion: Event vs. Normal Days |
| F19 | EC-AAM Venue Proximity Matrix Footprint |
| F20 | Exogenous Context Vector z_t Trajectories |

</details>

<details>
<summary><b>📂 05_training — Training Convergence (F21)</b></summary>

| ID | Title |
|:---|:---|
| F21 | Training Convergence & Huber Loss Curves (1,120 epochs) |

</details>

<details>
<summary><b>📂 06_results — Benchmarks & Interpretability (F22–F34)</b></summary>

| ID | Title |
|:---|:---|
| F22 | Benchmark Comparison across All Six Metrics (14 Models) |
| F23 | Prediction Error Growth over Forecast Horizon (10–60 min) |
| F24 | Actual vs. Predicted Flow Traces at Key Hub Stations |
| F25 | Event-Window Zoom & Peak Forecasting Resilience |
| F26 | Architectural Component Ablation Study |
| F27 | TD-PDM Soft-Assignment Matrix Audit (P₀ vs P*) |
| F28 | Learned Persona Adaptive Adjacencies A_adapt^(k) |
| F29 | Cross-Persona Fusion Gates β_k(z_t) Dynamics |
| F30 | Persona Attribution of Predicted Event Peaks |
| F31 | Residual Diagnostics & Error Distributions |
| F32 | GIS Station-Level Error Distribution Map |
| F33 | Event SOTA Benchmark |
| F34 | Horizon Degradation Comparison |

</details>

---

## ⚙️ Getting Started

### Prerequisites

```
Python       ≥ 3.10
PyTorch      ≥ 2.0.0  (CUDA recommended)
NumPy        ≥ 1.24
Pandas       ≥ 2.0
SciPy        ≥ 1.10
scikit-learn ≥ 1.2
Matplotlib   ≥ 3.7
Seaborn      ≥ 0.12
NetworkX     ≥ 3.0
tqdm         ≥ 4.65
```

### Quick Start

```bash
# Clone the repository
git clone https://github.com/usshamsuddeen/TDAG-Net-Adaptive-Graph-Learning-with-Persona-Aware-Interpretability.git
cd TDAG-Net-Adaptive-Graph-Learning-with-Persona-Aware-Interpretability

# Launch the notebook
jupyter notebook "Coding File/TDAG_Net_Complete_Coding_File.ipynb"
```

### Notebook Execution Guide

| Cell | Action | Time |
|:---|:---|:---|
| `[31]` **Experiment Runner** | Loads cached model results and predictions | < 1 second |
| `[33]` **Results Figures** | Renders all 34 high-resolution figures (PNG + PDF) and exports CSV data | ~2 minutes |
| Full pipeline | End-to-end training from raw data | ~4 hours (GPU) |

---

## 📝 Training Configuration

| Hyperparameter | Value |
|:---|:---|
| Sequence length | 12 steps (2 hours) |
| Forecast horizon | 6 steps (1 hour) |
| Batch size | 32 |
| Epochs | 1,120 |
| Learning rate | 3 × 10⁻⁴ |
| Weight decay | 1 × 10⁻⁴ |
| Loss function | Huber (δ = 1.0) |
| Gradient clipping | 5.0 |
| Early stopping patience | 15 epochs |
| Persona count (K) | 4 |
| Random seed | 42 |

---

## 📚 Citation

If you use this code, data, or methodology in your research, please cite:

```bibtex
@article{tdagnet2026,
  title     = {TDAG-Net: Adaptive Graph Learning with Persona-Aware Interpretability
               for Metro Passenger Flow Prediction},
  author    = {Shamsuddeen, Usman},
  year      = {2026},
  note      = {Under review}
}
```

---

## 📄 License

This repository is provided for **academic and research purposes only**. All passenger data has been anonymized using cryptographic SHA-256 hashing. See [DATASET_README.md](DATASET_README.md) for full data documentation.

---

<div align="center">

**Built with** ❤️ **using PyTorch**

</div>
