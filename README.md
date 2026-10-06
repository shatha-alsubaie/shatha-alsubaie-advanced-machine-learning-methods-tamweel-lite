# Tamweel Lite – 90-Day Financing Default Risk & Decision System

Applied capstone project for the **Advanced Machine Learning Methods (SDA-DSC-211)** program – **SDAIA Academy** · https://github.com/SDAIAAcademy

**Author:** Shatha Alsubaie

---

## 1. Problem statement
Estimate the probability that a financing application defaults within 90 days, using only information available at application time, and turn that probability into an **approve / review / decline** decision.
This is a teaching exercise on **synthetic data** and must not be used for real financing decisions.

## 2. Data
| Item | Value |
|---|---|
| Applications | 10,000 (synthetic) |
| Predictors | 22 application-time features |
| Target | `default_within_90d` (1 = default within 90 days) |
| Default rate | ≈ 7.9% (imbalanced) |
| Decision moment | `application_date` |

## 3. Results by day

### Day 1 – Baseline & boosting
Compared three models on 2,000 comparison rows (random stratified split, seed 211).

| Model | ROC-AUC | AP (PR-AUC) | Train time (s) | Trees |
|---|---|---|---|---|
| Logistic Regression | 0.8213 | 0.3258 | 0.04 | – |
| XGBoost | 0.8124 | **0.3338** | 0.25 | 61 |
| LightGBM | 0.8138 | 0.3248 | 0.22 | 41 |

- **Initial candidate:** XGBoost (highest AP), but the gap over the baseline is only 0.008.
- **Limitation:** one random split; 1,226 customers appeared in both development and comparison.

### Day 2 – Honest validation & tuning
| Protocol | Mean AP | Shared customers |
|---|---|---|
| Leaky random (unsafe) | 0.999 | ~1,700 |
| Clean random (unsafe) | 0.311 | ~1,700 |
| **Honest, fixed settings** | **0.315 ± 0.043** | **0** |
| Honest, Optuna settings | 0.313 | 0 |

- **Leakage removed:** `days_past_due_60` and `collection_calls` (only known after the application).
- **Honest split:** 3 forward time folds, 0 shared customers, only labels at least 90 days old.
- **Tuning:** a bounded Optuna search (8 trials, 1,935 reserved rows) did not beat the fixed settings; the difference is smaller than the fold-to-fold spread.
- **Model note:** the Day 1 gap between XGBoost and LightGBM (0.009 AP) was within noise, so the honest protocol continued with LightGBM.
- **Limitation:** AP drops to 0.27 in the latest period; OOF predictions cover 50.39% of rows.

### Day 3 – Cost-sensitive decision
_To be added._

### Day 4 – Explainability & calibration
_To be added._

### Day 5 – Final model & delivery
_To be added._

## 4. How to run
1. Open a notebook from `notebooks/` in Google Colab.
2. Select **Runtime → Change runtime type → CPU**.
3. Select **Runtime → Run all** (`FAST_MODE = True`, seed 211).
4. Outputs are saved to `artifacts/`.

No GPU, API key or paid subscription is required.

## 5. Repository structure
| Folder | Content |
|---|---|
| `notebooks/` | Executed notebooks for each day |
| `artifacts/` | CSV, JSON and PNG evidence produced by the labs |
| `reports/` | Decision Card, Interpretability Report, Model Card |
| `submission/` | Final predictions and submission files |
| `data/` | Synthetic course data and data contract |

## 6. Acknowledgement
Course design, notebooks and project template prepared and delivered by **Meaad Al-Marri | ميعاد المري** for SDAIA Academy. See [NOTICE.md](NOTICE.md) for attribution and educational use.
