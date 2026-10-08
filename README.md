<div align="center">

# SDA-DSC-211 · Advanced Machine Learning Methods
# أساليب تعلم الآلة المتقدمة

**Tamweel Lite – 90-Day Default Risk & Decision System | نظام تمويل لايت لتقدير خطر التعثر واتخاذ القرار**

**Author | المتدربة:** Shatha Alsubaie · **Student code | رمز المتدرب:** 211 · **Final version | النسخة النهائية:** `v1.1-final`

[Learning portal](https://almiyead-rgb.github.io/advanced-machine-learning-methods-sda-dsc-211/) · [Arabic portal](https://almiyead-rgb.github.io/advanced-machine-learning-methods-sda-dsc-211/ar.html) · [English portal](https://almiyead-rgb.github.io/advanced-machine-learning-methods-sda-dsc-211/en.html)

[All labs notebook](notebooks/00_all_labs_combined.ipynb) · [Final presentation](presentation/final_presentation.pdf) · [Model Card](reports/MODEL_CARD.md) · [Submission](submission/submission.csv)

</div>

<!-- BILINGUAL:EN -->
<!-- BILINGUAL:AR -->

<table>
<tr>
<td width="50%" valign="top" dir="ltr">

## Project idea and goal

Estimate the probability that a financing application **defaults within 90 days**, using only information available at application time, then turn that probability into a **review / approve** decision under a limited review capacity.

The data are **synthetic** course data. This is a learning project and must not be used for real financing decisions.

</td>
<td width="50%" valign="top" dir="rtl">

## فكرة المشروع وهدفه

تقدير احتمال **تعثر طلب التمويل خلال 90 يومًا** باستخدام المعلومات المتاحة وقت تقديم الطلب فقط، ثم تحويل هذا الاحتمال إلى قرار **مراجعة أو موافقة** ضمن سعة مراجعة محدودة.

البيانات **اصطناعية** خاصة بالدورة. المشروع تعليمي ولا يُستخدم لقرارات تمويل حقيقية.

</td>
</tr>
</table>

## Start here | ابدأ هنا

<table>
<tr>
<td width="50%" valign="top" dir="ltr">

1. Read this README for the project idea, results and decisions.
2. Open [Notebook 00 in Colab](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/00_readiness_check.ipynb) to check the environment.
3. Review the daily labs 01 → 05 (links in the five-day table below).
4. See the final check result in [Notebook 99](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/99_final_submission_check.ipynb).
5. Read the reports: [Decision Card](reports/DECISION_CARD.md) · [Interpretability Report](reports/INTERPRETABILITY_REPORT.md) · [Model Card](reports/MODEL_CARD.md).

</td>
<td width="50%" valign="top" dir="rtl">

1. اقرأ هذا الملف لفكرة المشروع ونتائجه وقراراته.
2. افتح [دفتر 00 في Colab](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/00_readiness_check.ipynb) لفحص البيئة.
3. راجع لابات الأيام من 01 إلى 05 (الروابط في جدول الأيام الخمسة أدناه).
4. اطّلع على نتيجة الفحص النهائي في [دفتر 99](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/99_final_submission_check.ipynb).
5. اقرأ التقارير: [بطاقة القرار](reports/DECISION_CARD.md) · [تقرير التفسير](reports/INTERPRETABILITY_REPORT.md) · [بطاقة النموذج](reports/MODEL_CARD.md).

</td>
</tr>
</table>

## Final result at a glance | النتيجة النهائية باختصار

| Item | Value | البند |
|---|---|---|
| Final model | **Logistic Regression** (KEEP SINGLE) | النموذج النهائي |
| Mean AP (honest OOF) | **0.392** (fold SD 0.030) | متوسط AP بتحقق صادق |
| Brier / ECE | 0.063 / 0.019 | جودة الاحتمالات |
| Decision threshold | **0.1689** raw (0.1223 calibrated) | عتبة القرار |
| Recall / Precision | 46.9% / 34.3% | الاستدعاء / الدقة |
| Flag rate | 11.4% (limit 12%) | نسبة الطلبات المحالة للمراجعة |
| Challenge batch | **300** of 2,500 sent to review | دفعة التحدي |
| Reproducibility | REPLAY_MATCH · BUNDLE_BYTES_VERIFIED | قابلية إعادة الإنتاج |

## Decision threshold | عتبة القرار

<table>
<tr>
<td width="50%" valign="top" dir="ltr">

- **Policy:** loss = 10 × FN + 1 × FP, and at most **12%** of applications may be sent to review.
- **Final threshold (Day 5, Logistic):** **0.1689** on the raw score = **0.1223** after sigmoid calibration (same rule, two scales).
- **Selected on** honest out-of-fold predictions only — never on evaluation or challenge data.
- **Result:** recall 46.9%, precision 34.3%, 11.4% flagged, loss 1,111; unchanged for FN cost 8, 10 or 12.
- **Challenge batch:** 330 of 2,500 were above the threshold; the 12% cap keeps the top **300** for review.
- The Day 3 threshold (0.6583) belonged to the Day 3 weighted LightGBM and was a development step, not the final rule.

</td>
<td width="50%" valign="top" dir="rtl">

- **السياسة:** الخسارة = 10 × FN + 1 × FP، وبحد أقصى **12%** من الطلبات تُحال للمراجعة.
- **العتبة النهائية (اليوم الخامس، Logistic):** **0.1689** على الدرجة الخام = **0.1223** بعد المعايرة (القاعدة نفسها بمقياسين).
- **اختيرت** من تنبؤات OOF الصادقة فقط، ولم تُستخدم بيانات التقييم أو التحدي.
- **النتيجة:** استدعاء 46.9% ودقة 34.3% وإحالة 11.4% وخسارة 1,111، ولا تتغير مع تكلفة FN تساوي 8 أو 10 أو 12.
- **دفعة التحدي:** 330 من 2,500 تجاوزت العتبة، وحد 12% يُبقي أعلى **300** للمراجعة.
- عتبة اليوم الثالث (0.6583) كانت لنموذج LightGBM الموزون في اليوم الثالث وهي خطوة تطويرية وليست القاعدة النهائية.

</td>
</tr>
</table>

## Data | البيانات

| Item | Value | البند |
|---|---|---|
| Applications | 10,000 (synthetic) | عدد الطلبات |
| Predictors | 22 application-time features | المتغيرات |
| Target | `default_within_90d` | المتغير المستهدف |
| Default rate | ≈ 7.9% (imbalanced) | نسبة التعثر (غير متوازنة) |
| Decision moment | `application_date` | لحظة القرار |

## Five-day build and my results | البناء خلال خمسة أيام ونتائجي

| Day | What I did and found | ما أنجزته ووجدته | Lab |
|---:|---|---|---|
| 1 | Compared Logistic, XGBoost and LightGBM on one random split. XGBoost had the best AP (0.334) but beat the baseline by only 0.008. | قارنت Logistic وXGBoost وLightGBM على تقسيم عشوائي واحد. حقق XGBoost أعلى AP (0.334) لكن بفارق 0.008 فقط عن خط الأساس. | [Notebook](notebooks/01_baseline_boosting.ipynb) · [Colab](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/01_baseline_boosting.ipynb) · [Guide](DAY1_GUIDE.md) |
| 2 | Removed two leaking features, used 3 forward time folds with 0 shared customers. Honest AP 0.315 ± 0.043 (leaky split gave 0.999). Optuna did not beat fixed settings. | حذفت متغيرين مسرّبين واستخدمت 3 طيات زمنية دون عملاء مشتركين. AP الصادق 0.315 ± 0.043 (التقسيم المسرّب أعطى 0.999). لم يتفوق Optuna على الإعدادات الثابتة. | [Notebook](notebooks/02_validation_tuning.ipynb) · [Colab](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/02_validation_tuning.ipynb) · [Guide](DAY2_GUIDE.md) |
| 3 | Cost = 10 × FN + FP with a 12% capacity. With the Day 3 weighted LightGBM, the development threshold was 0.6583 (later replaced by the Day 5 final threshold 0.1689): recall 40.9%, 10.4% flagged, capacity respected in every period. | التكلفة = 10 × FN + FP مع سعة 12%. مع نموذج LightGBM الموزون في اليوم الثالث كانت العتبة التطويرية 0.6583 (استُبدلت لاحقًا بالعتبة النهائية 0.1689 في اليوم الخامس): استدعاء 40.9% وإحالة 10.4% مع احترام السعة في كل فترة. | [Notebook](notebooks/03_cost_sensitive_decision.ipynb) · [Colab](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/03_cost_sensitive_decision.ipynb) · [Guide](DAY3_GUIDE.md) |
| 4 | Main drivers: bureau_score and dti (permutation + SHAP). Sigmoid calibration cut Brier 0.113 → 0.067 and ECE 0.147 → 0.022. | أهم العوامل: bureau_score وdti (أهمية التبديل وSHAP). خفّضت المعايرة Brier من 0.113 إلى 0.067 وECE من 0.147 إلى 0.022. | [Notebook](notebooks/04_explain_calibrate.ipynb) · [Colab](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/04_explain_calibrate.ipynb) · [Guide](DAY4_GUIDE.md) |
| 5 | Worth-It Gate: no ensemble beat Logistic by more than one fold SD → KEEP SINGLE. Built final policy, submission and presentation. | بوابة الجدوى: لم يتفوق أي تجميع على Logistic بأكثر من انحراف طية واحد ← الإبقاء على نموذج واحد. أعددت السياسة النهائية والتسليم والعرض. | [Notebook](notebooks/05_final_model.ipynb) · [Colab](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/05_final_model.ipynb) · [Guide](DAY5_GUIDE.md) |

All five labs in one file: [00_all_labs_combined.ipynb](notebooks/00_all_labs_combined.ipynb) · كل اللابات في ملف واحد.

### Day 5 model comparison | مقارنة نماذج اليوم الخامس

| Model | Mean AP | Fold SD | Brier | ECE |
|---|---|---|---|---|
| LightGBM | 0.345 | 0.043 | 0.066 | 0.023 |
| XGBoost | 0.353 | 0.029 | 0.066 | 0.023 |
| **Logistic (chosen)** | **0.392** | 0.030 | **0.063** | 0.019 |
| Weighted ensemble | 0.389 | 0.029 | 0.063 | 0.018 |
| Stack | 0.383 | 0.029 | 0.066 | 0.031 |

## Key decisions | أهم القرارات

<table>
<tr>
<td width="50%" valign="top" dir="ltr">

- Dropped `days_past_due_60` and `collection_calls`: known only after the application.
- Used time-ordered folds with no shared customers: random splits overstated AP.
- Kept fixed settings: Optuna's gain was smaller than fold noise.
- Judged by AP, not accuracy: flagging nobody gives 92.4% accuracy and 0% recall.
- Chose a capacity-safe threshold: the lowest-loss threshold exceeds the 12% limit.
- Kept one Logistic model: OOF residuals were 0.98–0.99 correlated, so averaging could not fix different errors.

**Key lesson:** the model that looked best on Day 1 lost after honest validation. Complexity is not evidence of improvement.

</td>
<td width="50%" valign="top" dir="rtl">

- حذفت `days_past_due_60` و`collection_calls` لأنها تُعرف بعد تقديم الطلب فقط.
- استخدمت طيات زمنية دون عملاء مشتركين لأن التقسيم العشوائي بالغ في AP.
- أبقيت الإعدادات الثابتة لأن تحسن Optuna أصغر من تذبذب الطيات.
- اعتمدت AP لا الدقة؛ عدم إحالة أي طلب يعطي دقة 92.4% واستدعاء 0%.
- اخترت عتبة تحترم السعة لأن عتبة أقل خسارة تتجاوز حد 12%.
- أبقيت نموذج Logistic واحدًا لأن أخطاء النماذج مترابطة بنسبة 0.98–0.99 فلا يفيد دمجها.

**الدرس الأهم:** النموذج الذي بدا الأفضل في اليوم الأول خسر بعد التحقق الصادق. التعقيد ليس دليلًا على التحسن.

</td>
</tr>
</table>

## Tools and models | الأدوات والنماذج

| Category | Used | الفئة |
|---|---|---|
| Environment | Google Colab (CPU), Python, Jupyter | بيئة العمل |
| Data & metrics | pandas, NumPy, scikit-learn | البيانات والمقاييس |
| Models | Logistic Regression, XGBoost, LightGBM, weighted ensemble, stacking | النماذج |
| Tuning | Optuna (bounded search) | الضبط |
| Explainability | Permutation importance, SHAP | التفسير |
| Calibration | Sigmoid calibration, Brier, ECE | المعايرة |
| Charts | Matplotlib | الرسوم |

## How to run | طريقة التشغيل

<table>
<tr>
<td width="50%" valign="top" dir="ltr">

1. Open a notebook from `notebooks/` in Google Colab (start with `00_readiness_check.ipynb`).
2. Select **Runtime → Change runtime type → CPU**.
3. Select **Runtime → Run all** (`FAST_MODE = True`, seed **211**).
4. Run the notebooks in order 01 → 05; outputs are saved to `artifacts/`.
5. Run `99_final_submission_check.ipynb` for the final check.

No GPU, API key or paid subscription is required.

</td>
<td width="50%" valign="top" dir="rtl">

1. افتح دفترًا من مجلد `notebooks/` في Google Colab (ابدأ بـ`00_readiness_check.ipynb`).
2. اختر **Runtime → Change runtime type → CPU**.
3. اختر **Runtime → Run all** (`FAST_MODE = True` والبذرة **211**).
4. شغّل الدفاتر بالترتيب من 01 إلى 05؛ تُحفظ المخرجات في `artifacts/`.
5. شغّل `99_final_submission_check.ipynb` للفحص النهائي.

لا يلزم GPU ولا مفتاح API ولا اشتراك مدفوع.

</td>
</tr>
</table>

## Deliverables | المخرجات

| Deliverable | File | المخرج |
|---|---|---|
| Readiness check | [00_readiness_check.ipynb](notebooks/00_readiness_check.ipynb) | فحص الجاهزية |
| Daily labs 1–5 | [01](notebooks/01_baseline_boosting.ipynb) · [02](notebooks/02_validation_tuning.ipynb) · [03](notebooks/03_cost_sensitive_decision.ipynb) · [04](notebooks/04_explain_calibrate.ipynb) · [05](notebooks/05_final_model.ipynb) | لابات الأيام 1–5 |
| All labs combined | [00_all_labs_combined.ipynb](notebooks/00_all_labs_combined.ipynb) | كل اللابات مجمعة |
| Final check | [99_final_submission_check.ipynb](notebooks/99_final_submission_check.ipynb) | الفحص النهائي |
| Decision Card | [DECISION_CARD.md](reports/DECISION_CARD.md) | بطاقة القرار |
| Interpretability Report | [INTERPRETABILITY_REPORT.md](reports/INTERPRETABILITY_REPORT.md) | تقرير التفسير |
| Model Card | [MODEL_CARD.md](reports/MODEL_CARD.md) | بطاقة النموذج |
| Ensemble decision | [ENSEMBLE_DECISION.md](reports/ENSEMBLE_DECISION.md) | قرار التجميع |
| Final policy & metrics | [final_policy.json](artifacts/final_policy.json) · [final_metrics.json](artifacts/final_metrics.json) | السياسة والمقاييس النهائية |
| Submission | [submission.csv](submission/submission.csv) | ملف التسليم |
| Presentation | [final_presentation.pdf](presentation/final_presentation.pdf) | العرض النهائي |

## Limitations and notes | القيود والملاحظات

<table>
<tr>
<td width="50%" valign="top" dir="ltr">

- Synthetic data; results do not describe real customers.
- AP drops to about 0.27 in the latest period, so drift needs monitoring.
- Regional false-positive rate ranges 6.3%–10.3%; descriptive only, not proof of fairness.
- Challenge labels are unavailable, so no challenge accuracy is claimed.
- On Day 4 the policy threshold flagged 109 of 107 allowed in 2024Q4 (CAPACITY_REVIEW_REQUIRED).
- The Interpretability Report (Day 4) explains the Day 4 weighted LightGBM, not the final Logistic model; for Logistic, coefficients describe the drivers.
- The model supports human review; it must not decide automatically.

</td>
<td width="50%" valign="top" dir="rtl">

- البيانات اصطناعية ولا تمثل عملاء حقيقيين.
- ينخفض AP إلى نحو 0.27 في أحدث فترة، لذا يلزم رصد الانجراف.
- يتراوح معدل الإيجابيات الخاطئة بين المناطق من 6.3% إلى 10.3%؛ وصفي فقط وليس إثباتًا للعدالة.
- تسميات دفعة التحدي غير متاحة، لذلك لا تُدّعى دقة عليها.
- في اليوم الرابع أحالت العتبة 109 طلبات مقابل 107 مسموحة في 2024Q4 (تتطلب مراجعة السعة).
- تقرير التفسير (اليوم الرابع) يشرح نموذج LightGBM الموزون في اليوم الرابع، وليس نموذج Logistic النهائي؛ في Logistic تصف المعاملات العوامل المؤثرة.
- النموذج يدعم المراجعة البشرية ولا يتخذ القرار تلقائيًا.

</td>
</tr>
</table>

## Final verification and submission | الفحص والتسليم النهائي

<table>
<tr>
<td width="50%" valign="top" dir="ltr">

1. All learner responses in Notebooks 01–05 are complete (READY_FOR_REVIEW).
2. [Notebook 99](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/99_final_submission_check.ipynb) ran all notebooks: every one **PASS**.
3. Final check result: **READY FOR FINAL SUBMISSION**.
4. File integrity: [submission/final_project_manifest.json](submission/final_project_manifest.json).
5. Final version tag: `v1.1-final` (see the repository Releases).

The automated check is evidence, not a grade or a submission receipt.

</td>
<td width="50%" valign="top" dir="rtl">

1. اكتملت جميع إجابات المتدربة في الدفاتر 01–05 (READY_FOR_REVIEW).
2. شغّل [دفتر 99](https://colab.research.google.com/github/shatha-alsubaie/shatha-alsubaie-advanced-machine-learning-methods-tamweel-lite/blob/main/notebooks/99_final_submission_check.ipynb) جميع الدفاتر: كلها **PASS**.
3. نتيجة الفحص النهائي: **READY FOR FINAL SUBMISSION**.
4. سلامة الملفات: [submission/final_project_manifest.json](submission/final_project_manifest.json).
5. Tag النسخة النهائية: `v1.1-final` (انظر Releases في المستودع).

الفحص الآلي دليل وليس درجة ولا إيصال تسليم.

</td>
</tr>
</table>

## Assessment | التقييم

Project 90 points + presentation and discussion 10 points; pass 70 / 100. Criteria: [RUBRIC.md](RUBRIC.md).
المشروع 90 درجة + العرض والمناقشة 10 درجات؛ النجاح 70 من 100. المعايير: [RUBRIC.md](RUBRIC.md).

## Repository map | خريطة المستودع

<table>
<tr>
<td width="50%" valign="top" dir="ltr">

- `notebooks/` — executed notebooks 00–05, 99 and all labs combined
- `reports/` — Decision Card, Interpretability Report, Ensemble Decision, Model Card
- `artifacts/` — CSV, JSON, figures and final model
- `evidence/` — daily evidence bundles (Days 1–4)
- `submission/` — final predictions and manifests
- `presentation/` — final five-slide presentation (PDF)
- `tamweel/` — reproducible inference interface
- `scripts/` — setup, checks and rebuild tools
- `data/` — synthetic course data and data contract

</td>
<td width="50%" valign="top" dir="rtl">

- `notebooks/` — الدفاتر المنفذة 00–05 و99 واللابات مجمعة
- `reports/` — بطاقة القرار وتقرير التفسير وقرار التجميع وبطاقة النموذج
- `artifacts/` — ملفات CSV وJSON والرسوم والنموذج النهائي
- `evidence/` — أدلة الأيام (1–4)
- `submission/` — التنبؤات النهائية والـmanifest
- `presentation/` — العرض النهائي من خمس شرائح (PDF)
- `tamweel/` — واجهة استدلال قابلة لإعادة الإنتاج
- `scripts/` — أدوات الإعداد والفحص وإعادة البناء
- `data/` — بيانات الدورة الاصطناعية وعقد البيانات

</td>
</tr>
</table>

## Guides and support | الأدلة والدعم

| Need | Resource | الاحتياج |
|---|---|---|
| Colab | [COLAB_GUIDE.md](COLAB_GUIDE.md) | استخدام Colab |
| GitHub | [GITHUB_GUIDE.md](GITHUB_GUIDE.md) | حفظ المشروع في GitHub |
| Submission | [SUBMISSION_GUIDE.md](SUBMISSION_GUIDE.md) · [FINAL_CHECK_GUIDE.md](FINAL_CHECK_GUIDE.md) | التسليم والفحص |
| Terms | [GLOSSARY.md](GLOSSARY.md) | المصطلحات |
| Troubleshooting | [TROUBLESHOOTING.md](TROUBLESHOOTING.md) · [FAQ.md](FAQ.md) | حل المشكلات |

## Sources and disclosures | المصادر والإفصاحات

<table>
<tr>
<td width="50%" valign="top" dir="ltr">

- **Data:** synthetic Tamweel Lite course data (`tamweel-lite-1.0`), seed 211; no real customers.
- **Template and course code:** SDA-DSC-211 student template by Meaad Al-Marri; official code cells were not modified.
- **Execution:** all daily results are LIVE runs on Google Colab CPU; no educational example output is submitted as my own run.
- **Libraries:** scikit-learn, XGBoost, LightGBM, Optuna, SHAP, pandas, NumPy, Matplotlib.
- **AI assistance:** an AI assistant was used for guidance on GitHub steps and README wording; runs, results and decisions are mine.

</td>
<td width="50%" valign="top" dir="rtl">

- **البيانات:** بيانات الدورة الاصطناعية Tamweel Lite (`tamweel-lite-1.0`) بالبذرة 211، ولا تمثل عملاء حقيقيين.
- **القالب والكود:** قالب المتدرب لدورة SDA-DSC-211 من إعداد ميعاد المري، ولم تُعدّل خلايا الكود الرسمية.
- **التنفيذ:** جميع نتائج الأيام تشغيل فعلي LIVE على Colab بالمعالج CPU، ولا تُقدَّم مخرجات أمثلة تعليمية بوصفها تشغيلًا شخصيًا.
- **المكتبات:** scikit-learn وXGBoost وLightGBM وOptuna وSHAP وpandas وNumPy وMatplotlib.
- **المساعدة بالذكاء الاصطناعي:** استُخدم مساعد ذكاء اصطناعي للإرشاد في خطوات GitHub وصياغة هذا الملف؛ التشغيل والنتائج والقرارات من عملي.

</td>
</tr>
</table>

> This repository contains no passwords, API keys, tokens or personal data.  
> لا يحتوي هذا المستودع على كلمات مرور أو مفاتيح أو رموز وصول أو بيانات شخصية.

Course design, notebooks and project template prepared and delivered by **Meaad Al-Marri | ميعاد المري** for SDAIA Academy · [Attribution and educational use](NOTICE.md).

Final submission version: `v1.1-final` · Learner project based on template release `v1.0.0`.
