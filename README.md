[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/kayesbinyousuf/ReliabilityAware-HEMS/blob/main/ReliabilityAware_HEMS_KayesBinYousuf.ipynb)

# Reliability-Aware HEMS: Extension of the LT-ST MPC Framework

**Author:** [Kayes Bin Yousuf](https://linkedin.com/in/kayes-bin-yousuf) — ML Researcher | Renewable Energy & Industrial AI | Reviewer @Elsevier

> **Base paper:** Lin, Zamora, Jiang, Chen, Srivastava — *A Multi-Level Home Energy Management System (HEMS) for DC-Microgrids*, IEEE Transactions on Smart Grid

---

## Overview

This repository presents a **reliability-aware extension** to the long-term/short-term (LT-ST) Model Predictive Control (MPC) framework for Home Energy Management Systems (HEMS). The original paper formulates a deterministic two-layer MPC for coordinating battery, supercapacitor (SC), PV generation, and controllable loads in a DC microgrid. This work augments that framework with principled uncertainty quantification and CVaR-robust dispatch.

---

## Key Contributions

| # | Contribution | Reference |
|---|---|---|
| 1 | **Heteroscedastic Conformal Prediction Intervals** for PV forecasting — interval width scales with PV magnitude, producing wider bounds at midday peak | Vovk et al.; NREL statistics |
| 2 | **CVaR(95%) robust dispatch** evaluated post-hoc on both deterministic and CVaR-aware plans using the paper's full cost function | Eq. 33–35 |
| 3 | **ST uncertainty correction** — true extension of Eq. 36–42: original tracking terms fully preserved, uncertainty penalty term added on top | Eq. 36–42 |
| 4 | **Full 24h rolling horizon simulation** — 1h LT steps × 12 five-minute ST steps = 288 inner optimization steps with live SoC propagation | Paper Section V |
| 5 | **Exact paper cost models** — battery degradation (DoD-dependent lifetime curve), SC degradation, asymmetric grid buy/sell pricing, IDC/TDC discomfort | Eq. 6–20, 33–35 |
| 6 | **Plamp and PCAC** modeled as separate controllable variables with full IDC/TDC feedback (not lumped into a generic load) | Section II-A/B/C |
| 7 | Sign-corrected cost reporting; unit assertions on every module |  |

---

## Methodology

### LT Layer (1-hour horizon)
- Solves the full paper objective (Eq. 33–35) over a 24-step horizon
- Outputs deterministic dispatch plan and CVaR-aware alternative
- CVaR computed over 50 scenario samples using paper's exact cost function

### ST Layer (5-minute horizon)
- Tracks LT reference dispatch while correcting for real-time uncertainty
- Extends Eq. 36–42 with an uncertainty ratio term derived from conformal prediction intervals
- Issues a binary risk flag when SoC is low and uncertainty is high (>40%)

### Uncertainty Quantification
- Split conformal prediction with heteroscedastic residuals (σ scales with PV level)
- 90% coverage guaranteed by construction (distribution-free, finite-sample valid)
- Applied to both PV and load forecasts independently

---

## Results

| Figure | Content |
|--------|---------|
| **A** | LT battery dispatch: deterministic baseline vs CVaR-aware plan |
| **B** | PV conformal prediction interval — wider at midday, narrow at night |
| **C** | Operating cost distribution across 50 scenarios (batt + SC + grid + comfort) |
| **D** | ST uncertainty ratio across 288 steps with risk flag events |
| **E** | Battery & SC SoC trajectories over full 24h rolling horizon |

---

## How to Run

### Option 1 — Google Colab (no setup required)
Click the **Open in Colab** badge above. All dependencies install automatically. No external dataset required — NREL-like profiles are generated programmatically.

### Option 2 — Local

```bash
git clone https://github.com/kayesbinyousuf/ReliabilityAware-HEMS.git
cd ReliabilityAware-HEMS
pip install -r requirements.txt
jupyter notebook ReliabilityAware_HEMS_KayesBinYousuf.ipynb
```

### Dependencies
```
numpy
scipy
matplotlib
```

---

## Repository Structure

```
ReliabilityAware-HEMS/
├── ReliabilityAware_HEMS_KayesBinYousuf.ipynb   # Main notebook (11 cells)
├── requirements.txt
├── LICENSE
└── README.md
```

---

## Citation

If you use this work, please also cite the base paper:

```
Lin, J., Zamora, R., Jiang, Y., Chen, T., & Srivastava, A. K.
A Multi-Level Home Energy Management System (HEMS) for DC-Microgrids.
IEEE Transactions on Smart Grid.
```

---

## License

[MIT License](LICENSE)
