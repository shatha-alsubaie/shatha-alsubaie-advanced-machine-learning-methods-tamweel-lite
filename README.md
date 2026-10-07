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
Policy: loss = 10 × FN + 1 × FP, with at most 12% of applications flagged in every validation period.

| Rule | Threshold | Recall | Precision | Flags | Loss | Capacity |
|---|---|---|---|---|---|---|
| Default | 0.500 | 57.8% | 22.1% | 1,005 (19.9%) | 2,403 | ❌ |
| Lowest loss, no limit | 0.449 | 64.1% | 21.6% | 1,141 (22.6%) | 2,275 | ❌ |
| **Chosen (capacity-safe)** | **0.6583** | **40.9%** | **29.9%** | **526 (10.4%)** | **2,639** | ✅ |

- **Imbalance:** unweighted, weighted and oversampled models ranked almost the same (AP 0.309–0.316); weights move the threshold, not the ranking.
- **Accuracy trap:** flagging nobody gives 92.4% accuracy with 0% recall.
- **Capacity:** periods at 8.4%, 10.9% and 11.9%; the threshold is unchanged for FN cost 8, 10 or 12, so capacity drives the decision.
- **Regions:** FPR gap 0.65 pp (7.6%–8.3%), but recall ranges 34.6%–48.1% and needs monitoring.
- **Decision Card:** [reports/DECISION_CARD.md](reports/DECISION_CARD.md)

### Day 4 – Explainability & calibration
Roles frozen before evaluation: fit (2,516) · calibration Jul–Sep 2023 (584) · policy Jan–Mar 2024 (589) · evaluation Jul–Dec 2024 (1,733).

| Probabilities | ROC-AUC | AP | Brier | ECE |
|---|---|---|---|---|
| Raw (weighted LightGBM) | 0.771 | 0.259 | 0.113 | 0.147 |
| **Sigmoid-calibrated** | 0.771 | 0.259 | **0.067** | **0.022** |

- **Drivers:** bureau_score (permutation AP drop 0.127, mean |SHAP| 0.90) and dti (0.069, 0.54); SHAP is in log-odds, not probability points.
- **Local example:** TR-009585, raw 0.903 → calibrated 0.48; low bureau_score 497 (+2.27) and high dti 1.28 (+1.20).
- **Stability:** AP 95% interval 0.197–0.338; Brier improvement stays negative (−0.054 to −0.037).
- **Capacity:** the policy threshold flags 109 of 107 allowed in 2024Q4 → CAPACITY_REVIEW_REQUIRED; not retuned on evaluation.
- **Report:** [reports/INTERPRETABILITY_REPORT.md](reports/INTERPRETABILITY_REPORT.md)

### Day 5 – Final model & delivery
| Model | Mean AP | Fold SD | Brier | ECE |
|---|---|---|---|---|
| LightGBM | 0.345 | 0.043 | 0.066 | 0.023 |
| XGBoost | 0.353 | 0.029 | 0.066 | 0.023 |
| **Logistic (chosen)** | **0.392** | 0.030 | **0.063** | 0.019 |
| Weighted ensemble | 0.389 | 0.029 | 0.063 | 0.018 |
| Stack | 0.383 | 0.029 | 0.066 | 0.031 |

- **Worth-It Gate: KEEP SINGLE → Logistic Regression.** No ensemble beat it by more than one fold SD; OOF residuals were 0.98–0.99 correlated, so averaging could not fix different errors.
- **Final policy:** raw OOF threshold 0.1689 (calibrated 0.1223): recall 46.9%, precision 34.3%, 11.4% flagged, loss 1,111; unchanged for FN cost 8–12.
- **Challenge batch:** 330 of 2,500 above threshold; the 12% full-batch cap keeps **300**. No challenge accuracy is claimed (labels unavailable).
- **Regions (OOF):** false-positive rate 6.3% (eastern) to 10.3% (western) — descriptive, needs monitoring.
- **Reproducibility:** REPLAY_MATCH and BUNDLE_BYTES_VERIFIED.
- **Reports:** [Model Card](reports/MODEL_CARD.md) · [Ensemble decision](reports/ENSEMBLE_DECISION.md) · [Executive summary](PROJECT_README.md) · [Presentation](presentation/final_presentation.pdf)

### Key lesson
On Day 1 XGBoost looked best on one random split (gap 0.008). After honest, leakage-free validation, the simplest model won clearly. Complexity is not evidence of improvement.

## 4. How to run
1. Open a notebook from `notebooks/` in Google Colab.
2. Select **Runtime → Change runtime type → CPU**.
3. Select **Runtime → Run all** (`FAST_MODE = True`, seed 211).
4. Outputs are saved to `artifacts/`.

No GPU, API key or paid subscription is required.

## 5. Repository structure
| Folder | Content |
|---|---|
| `notebooks/` | Executed notebooks for each day (01–05) |
| `evidence/` | Daily evidence bundles imported by the Day 5 exporter |
| `presentation/` | Final five-slide presentation (PDF) |
| `artifacts/` | CSV, JSON and PNG evidence produced by the labs |
| `reports/` | Decision Card, Interpretability Report, Model Card |
| `submission/` | Final predictions and submission files |
| `data/` | Synthetic course data and data contract |

## 6. Acknowledgement
Course design, notebooks and project template prepared and delivered by **Meaad Al-Marri | ميعاد المري** for SDAIA Academy. See [NOTICE.md](NOTICE.md) for attribution and educational use.
