# تقريرك: التفسير والمعايرة — Tamweel Lite

**الحالة:** جاهز للمراجعة؛ لا يعني اعتمادًا أو درجة

**مصدر التفسير:** LIVE. **النموذج والمعايرة:** LIVE. **السعة:** CAPACITY_REVIEW_REQUIRED.

## النموذج والأدوار
LightGBM موزون، 80 شجرة. الهدف حدث تعثر اصطناعي خلال90 يومًا بعد الطلب. الأدوار منفصلة زمنيًا وبالعملاء: تدريب 2516، معايرة 584 (40 موجب)، سياسة 589، تقييم 1733. الفجوات والتداخلات مستبعدة. سبق استخدام بيانات التقييم في الدورة، فهي ليست اختبارًا نهائيًا لم يمسّ.

## التفسير العام والمحلي
Permutation يقيس انخفاضAP على التقييم؛ إشارات المنطقة تُبدّل معًا. SHAP يفسر النموذج الخام بوحدةlog-odds وخلفية مسارات أشجار التدريب. base+sum(SHAP)=raw margin، ثمsigmoid للمجموع فقط. القيم ليست نقاط احتمال ولا تفسيرًا مباشرًا للنموذج المعاير.

Globally, permutation importance on 1,733 evaluation requests shows bureau_score (AP drop 0.127) and dti (0.069) dominate, and mean |SHAP| agrees (0.90 and 0.54 log-odds). Locally, request TR-009585 is driven by a low bureau_score of 497 (+2.27) and a high dti of 1.28 (+1.20). Global importance describes the whole sample; a local explanation describes one request and can differ from the global ranking.

SHAP values are in raw log-odds, not probability points: they add up from the base value E[f(X)] = -1.79 to f(x) = 2.23 for this request (additivity error 5.7e-15). A +2.27 contribution is not +227% or +2.27 percentage points. Probabilities are shown separately: raw score 0.903, calibrated 0.48.

الطلب الاصطناعي TR-009585: الدرجة الخام 0.90308 والاحتمال المعاير 0.47952. اختير أعلى درجة داخل عينةSHAP دون استخدام النتيجة الفعلية.
- استخدم النموذج درجة ائتمانية اصطناعية عند الطلب بالقيمة 497 لرفع درجته الخام؛ لا يثبت ذلك السببية. (SHAP=+2.2693 log-odds؛ قيمة معوضة: False)
- استخدم النموذج نسبة الالتزام مع القسط المقترح إلى الدخل بالقيمة 1.2806 لرفع درجته الخام؛ لا يثبت ذلك السببية. (SHAP=+1.1981 log-odds؛ قيمة معوضة: False)
- استخدم النموذج مبلغ التمويل المطلوب بالقيمة 93437.3 لرفع درجته الخام؛ لا يثبت ذلك السببية. (SHAP=+0.2007 log-odds؛ قيمة معوضة: False)

SHAP explains the fitted model, not real-world causes, and correlated features (income, obligations, savings) can share or swap credit. Gain in training and held-out permutation rank features differently (months_employed is high in gain but near zero in permutation). Reason codes are not proof of fairness or legal compliance, and the data are synthetic.

## دليل المعايرة
على 1733 صفًا و139 موجب: Brier 0.113027 → 0.067112؛ ECE 0.146871 → 0.022486. عشر حاويات متساوية العرض مع أعدادها فيday4_reliability_bins.csv. AP 0.258677 → 0.258677؛ ROC-AUC 0.770804 → 0.770804. هذه نتائج هذه العينة وليست ضمانًا لتحسن مستقبلي.

Sigmoid calibration was learned only on the Jul-Sep 2023 calibration rows and measured on evaluation. Brier fell from 0.113 to 0.067 and ECE from 0.147 to 0.022, while ROC-AUC (0.771) and AP (0.259) did not change, because calibration changes probability values, not ranking. Bins above 0.4 hold few requests, so they are noisy.

## الاستقرار
200 تكرارbootstrap صالح بسحب العملاء؛ فترات مئينية95% مع تثبيت النموذج والمعاير. لا تشمل تعلم النموذج أو المعايرة أو الانجراف المستقبلي، ولا تصف احتمال فرد. انحرافAP بين ربعي التقييم وصفي فقط. اختبارbureau_score±1 نُفذ؛ راجع day4_local_stability.csv.

With 200 customer-cluster bootstrap replicates, the 95% interval for AP is 0.197 to 0.338, so a single AP value is uncertain. The Brier change stays negative in the whole interval (-0.054 to -0.037), and both evaluation quarters improved (ECE 0.134 to 0.032 and 0.159 to 0.024). The local top three reasons stayed the same when bureau_score moved by +/-1.

## العتبة ومنطقة المراجعة
العتبة الخام 0.5881953696965011 اختيرت علىpolicy بخسارة10×FN+FP وسقف12% ثم نُقلت إلى 0.17331013263107387. لم تعدل باستخدام التقييم. المنطقة[0.15331, 0.19331] تشخيصية بعرض±0.02 وليست فترة ثقة. الاتحاد يحسب الطلب مرة واحدة.
- 2024Q3: السقف 100، الإشارات 97، اتحاد المراجعة 109.
- 2024Q4: السقف 107، الإشارات 109، اتحاد المراجعة 122.

The policy threshold (raw 0.588, calibrated 0.173) was feasible on policy rows (68 flags vs capacity 70). On evaluation it flags 97 of 100 allowed in 2024Q3 but 109 of 107 in 2024Q4, and the +/-0.02 review band raises this to 109 and 122. Status is CAPACITY_REVIEW_REQUIRED: the threshold must not be retuned on evaluation data; the overrun must be escalated and monitored.

عند تجاوز السعة، وثّق الحاجة إلى تصميم سياسة جديدة على بيانات تطوير وتقييمها بدليل جديد. لا ترفع السقف ولا تقص الحالات بعد رؤية النتيجة. التفسير ليس سببية أو شهادة عدالة، والخسارة وحدات تعليمية لا رسوم أو خصم درجات. لا يستخدم هذا التمرين لتمويل حقيقي.
