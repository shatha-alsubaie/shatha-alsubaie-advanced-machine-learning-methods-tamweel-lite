# قرار التجميع

KEEP SINGLE — Logistic

The Worth-It Gate chose KEEP_SINGLE with Logistic Regression. Over three forward folds Logistic had the highest mean AP (0.392, fold SD 0.030), the lowest Brier (0.063) and low ECE (0.019). LightGBM (0.345) and XGBoost (0.353) were clearly lower, and no ensemble passed the gate: the best, Weighted, was 0.389 (lift -0.002), smaller than one fold SD. OOF residuals of the three models were 0.98-0.99 correlated, so averaging could not fix different errors, and the simpler model is also easier to explain.

Selection used nested forward OOF on 2,155 requests across three periods (2023Q1, 2023Q3, 2024Q1) with customers kept on one side and labels matured for 90 days. OOF is used for model and threshold selection, so it is not an untouched test; three related periods are not a significance test, and fold SD is descriptive only. 2,588 rows were excluded by gaps and customer conflicts, and the data are synthetic.

الدليل: artifacts/ensemble_comparison.csv وday5_ensemble_gate.json. SD وصفي، وليس اختبار دلالة.
