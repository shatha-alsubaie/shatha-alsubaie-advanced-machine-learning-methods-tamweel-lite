<div align="center">

# Tamweel Lite · 90-Day Default Risk & Decision System
# نظام تمويل لايت لتقدير خطر التعثر واتخاذ القرار

**SDA-DSC-211 · Advanced Machine Learning Methods | أساليب تعلم الآلة المتقدمة**

**Author | المتدربة:** Shatha Alsubaie · **Student code | رمز المتدرب:** 211

[All labs notebook](notebooks/00_all_labs_combined.ipynb) · [Final presentation](presentation/final_presentation.pdf) · [Model Card](reports/MODEL_CARD.md) · [Submission](submission/submission.csv)

</div>

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

## Data | البيانات

| Item | Value | البند |
|---|---|---|
| Applications | 10,000 (synthetic) | عدد الطلبات |
| Predictors | 22 application-time features | المتغيرات |
| Target | `default_within_90d` | المتغير المستهدف |
| Default rate | ≈ 7.9% (imbalanced) | نسبة التعثر (غير متوازنة) |
| Decision moment | `application_date` | لحظة القرار |

## Five-day build and my results | البناء خلال خمسة أيام ونتائجي

| Day | What I did and found | ما أنجزته ووجدته | Notebook |
|---:|---|---|---|
| 1 | Compared Logistic, XGBoost and LightGBM on one random split. XGBoost had the best AP (0.334) but beat the baseline by only 0.008. | قارنت Logistic وXGBoost وLightGBM على تقسيم عشوائي واحد. حقق XGBoost أعلى AP (0.334) لكن بفارق 0.008 فقط عن خط الأساس. | [01](notebooks/01_baseline_boosting.ipynb) |
| 2 | Removed two leaking features, used 3 forward time folds with 0 shared customers. Honest AP 0.315 ± 0.043 (leaky split gave 0.999). Optuna did not beat fixed settings. | حذفت متغيرين مسرّبين واستخدمت 3 طيات زمنية دون عملاء مشتركين. AP الصادق 0.315 ± 0.043 (التقسيم المسرّب أعطى 0.999). لم يتفوق Optuna على الإعدادات الثابتة. | [02](notebooks/02_validation_tuning.ipynb) |
| 3 | Cost = 10 × FN + FP with a 12% capacity. Chose threshold 0.6583: recall 40.9%, 10.4% flagged, capacity respected in every period. | التكلفة = 10 × FN + FP مع سعة 12%. اخترت عتبة 0.6583: استدعاء 40.9% وإحالة 10.4% مع احترام السعة في كل فترة. | [03](notebooks/03_cost_sensitive_decision.ipynb) |
| 4 | Main drivers: bureau_score and dti (permutation + SHAP). Sigmoid calibration cut Brier 0.113 → 0.067 and ECE 0.147 → 0.022. | أهم العوامل: bureau_score وdti (أهمية التبديل وSHAP). خفّضت المعايرة Brier من 0.113 إلى 0.067 وECE من 0.147 إلى 0.022. | [04](notebooks/04_explain_calibrate.ipynb) |
| 5 | Worth-It Gate: no ensemble beat Logistic by more than one fold SD → KEEP SINGLE. Built final policy, submission and presentation. | بوابة الجدوى: لم يتفوق أي تجميع على Logistic بأكثر من انحراف طية واحد ← الإبقاء على نموذج واحد. أعددت السياسة النهائية والتسليم والعرض. | [05](notebooks/05_final_model.ipynb) |

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
- The model supports human review; it must not decide automatically.

</td>
<td width="50%" valign="top" dir="rtl">

- البيانات اصطناعية ولا تمثل عملاء حقيقيين.
- ينخفض AP إلى نحو 0.27 في أحدث فترة، لذا يلزم رصد الانجراف.
- يتراوح معدل الإيجابيات الخاطئة بين المناطق من 6.3% إلى 10.3%؛ وصفي فقط وليس إثباتًا للعدالة.
- تسميات دفعة التحدي غير متاحة، لذلك لا تُدّعى دقة عليها.
- في اليوم الرابع أحالت العتبة 109 طلبات مقابل 107 مسموحة في 2024Q4 (تتطلب مراجعة السعة).
- النموذج يدعم المراجعة البشرية ولا يتخذ القرار تلقائيًا.

</td>
</tr>
</table>

## Repository map | خريطة المستودع

| Folder | Content | المحتوى |
|---|---|---|
| `notebooks/` | Executed notebooks 00–05, 99 and all labs combined | الدفاتر المنفذة واللابات مجمعة |
| `reports/` | Decision Card, Interpretability Report, Ensemble Decision, Model Card | التقارير والبطاقات |
| `artifacts/` | CSV, JSON, figures and final model | ملفات المخرجات والرسوم والنموذج النهائي |
| `evidence/` | Daily evidence bundles (Days 1–4) | أدلة الأيام |
| `submission/` | Final predictions and manifest | التنبؤات النهائية |
| `presentation/` | Final five-slide presentation (PDF) | العرض النهائي |
| `data/` | Synthetic course data and data contract | بيانات الدورة الاصطناعية |

> This repository contains no passwords, API keys, tokens or personal data.  
> لا يحتوي هذا المستودع على كلمات مرور أو مفاتيح أو رموز وصول أو بيانات شخصية.

Course design, notebooks and project template prepared and delivered by **Meaad Al-Marri | ميعاد المري** for SDAIA Academy · [Attribution and educational use](NOTICE.md).
