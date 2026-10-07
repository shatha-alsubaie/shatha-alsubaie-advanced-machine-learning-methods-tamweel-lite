# بطاقة النموذج | Model Card
## الحالة
READY_FOR_REVIEW — جودة التفسير تحتاج مراجعة بشرية، وليست درجة آلية.
## الغرض والاستخدام | Purpose and use
Teaching use only: rank fictional Tamweel Lite applications for a simulated manual-review queue under a 10*FN + FP loss and a 12% capacity. It must not be used for real financing decisions, to judge real people or regions, or as a credit score; decision=0 is not a guarantee that an application is safe.
بيانات Tamweel Lite اصطناعية؛ الهدف حدث خلال 90 يومًا بعد الطلب. 22 خاصية متاحة وقت الطلب؛ لا معرفات أو تواريخ أو هدف في المدخلات. الاستخدام التعليمي فقط؛ لا قرارات تمويل فعلية.
## البيانات والتحقق | Data and validation
حوض التدريب والاختيار: 6576 طلبًا. المعايرة: 836 طلبًا و78 موجبًا. حجز عملاء المعايرة، 90 يومًا لنضج التسميات، وOOF أمامي متداخل مع فصل العملاء في المستويين. طيات المقارنة: 2023Q1 و2023Q3 و2024Q1. نستبعد النتائج التي لم تنضج قبل الأدوار التالية؛ آخر بيانات التدريب لا تستخدم تلقائيًا.
Selection used nested forward OOF on 2,155 requests across three periods (2023Q1, 2023Q3, 2024Q1) with customers kept on one side and labels matured for 90 days. OOF is used for model and threshold selection, so it is not an untouched test; three related periods are not a significance test, and fold SD is descriptive only. 2,588 rows were excluded by gaps and customer conflicts, and the data are synthetic.
## النموذج والقرار | Model selection
KEEP SINGLE / Logistic. انحراف AP المرجعي: 0.029808. ارجع إلى artifacts/ensemble_comparison.csv وday5_fold_scores.csv للأرقام الكاملة.
The Worth-It Gate chose KEEP_SINGLE with Logistic Regression. Over three forward folds Logistic had the highest mean AP (0.392, fold SD 0.030), the lowest Brier (0.063) and low ECE (0.019). LightGBM (0.345) and XGBoost (0.353) were clearly lower, and no ensemble passed the gate: the best, Weighted, was 0.389 (lift -0.002), smaller than one fold SD. OOF residuals of the three models were 0.98-0.99 correlated, so averaging could not fix different errors, and the simpler model is also easier to explain.
## المعايرة | Calibration
Sigmoid على عينة محجوزة من التدريب والاختيار؛ الرسم والمقاييس تشخيص على عينة تعلم المعاير، وليسا اختبارًا مستقلاً. لا ادعاء بتحسن على تحدٍّ مجهول التسميات.
Sigmoid was learned on 836 held-out calibration requests (78 defaults) that were not used for training. The diagnostic is scored on the same calibration-fit rows, so it is not an independent test: raw Brier 0.0765 and ECE 0.021 vs sigmoid 0.0781 and ECE 0.035, so calibration did not improve here because Logistic scores were already close to calibrated. Bins above 0.4 hold few requests and are noisy. No calibration claim is made on the unlabeled challenge.
## السياسة والسعة والمناطق | Policy and regions
خسارة 10 FN + FP، عتبة OOF الخام 0.16892161427109176 والمنقولة 0.12225843144286948. سعة الدفعة 300؛ المرشحون 330؛ الإشارات النهائية 300. كتلة الدرجات المتساوية لا تقسم. 1=إشارة مراجعة تعليمية، 0=عدم رفع الإشارة.
On OOF, raw threshold 0.1689 flags 245 of 2,155 (11.4%, max period 11.7%) with recall 46.9%, precision 34.3% and loss 1,111; it stays the same for FN cost 8, 10 or 12. On the 2,500 challenge requests, 330 exceed the transported threshold 0.1223, and the 12% full-batch cap keeps 300 and removes 30 (boundary score 0.1299). Regional OOF false-positive rates range from 6.3% (eastern) to 10.3% (western), and recall from 44.7% to 50.0%; every region has more than 480 non-defaults. This is descriptive, not a fairness certification.
## التفسير وحدوده | Explanation scope
تفسير اليوم الرابع يخص نموذج اليوم الرابع؛ لا يُنسب تلقائيًا إلى هذه النسخة. تغيير النموذج أو خصائصه أو معايرته يستلزم مراجعة التفسير.
No. The Day 4 SHAP and permutation results explain the Day 4 weighted LightGBM, not this final calibrated Logistic Regression. The direction of bureau_score and dti should be rechecked using the Logistic coefficients or a new permutation/SHAP run on the final model before reason codes are reused.
## المتابعة والقيود | Monitoring and limitations
Each batch: track application volume, score distribution and the share above the threshold against the 12% cap; when matured 90-day labels arrive, recompute AP, recall, precision, Brier and ECE by period; recheck regional false-positive rate and recall; and trigger review if the flag share approaches 12%, calibration error rises, or a regional gap widens. Re-run the honest validation before any retraining.
OOF يستخدم للاختيار، وثلاث فترات ليست اختبار دلالة. العتبة قد تتغير سعتها عند نقلها إلى نموذج معاد التدريب. لا تسميات للتحدي، ولا مقاييس أداء أو شهادة عدالة له. البيانات لا تمثل أشخاصًا أو مناطق حقيقية.
## إعادة الإنتاج | Reproducibility
seed=211; trees=80; CPU مجاني. الإصدارات في artifacts/environment.json. المصادر/بصماتها في artifacts/day5_run.json. النموذج artifacts/final_model؛ inference.predict يعيد ID واحتمالًا؛ السياسة تطبق بعد جمع الدفعة. replay_final يعيد التنبؤ المحفوظ؛ rebuild_final يعيد التدريب. لا تدرب النموذج بعد تثبيت المعاير.
## ملكيتك للتسليم | Submission ownership
أكمل أدلة الأيام السابقة والعرض، واحفظ الدفتر المنفذ، ثم سجل SHA وtag مستودعك في قناة التسليم الخاصة. دعم الدورة f486fc50dd9ac8403016facc58cf6a62beb4abf4 ليس SHA تسليمك.
