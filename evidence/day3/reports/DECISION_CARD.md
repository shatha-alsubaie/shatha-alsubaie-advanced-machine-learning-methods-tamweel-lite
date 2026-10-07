# بطاقة قرارك — Tamweel Lite

**الحالة:** جاهزة للمراجعة؛ لا تعني اعتمادًا أو درجة
**مصدر الأرقام:** LIVE · **الاستراتيجية:** weighted · **الصفوف:** 5,039 OOF

**المهمة:** الفئة الموجبة `default_within_90d=1` تعني حدث تعثر اصطناعي خلال90 يومًا بعد الطلب. كل طية تحقق طلبات لاحقة، وتستبعد عملاءها من التدريب وتشترط نضج نتيجة التدريب قبل بدايتها. المعرّفات والتاريخ خارج المدخلات.

| الدليل | القيمة |
|---|---:|
| العتبة المقيدة، بالقيمة الكاملة | 0.6583471436014694 |
| عتبة أقل خسارة دون قيد | 0.44863935722081005 |
| Recall | 40.89% |
| Precision | 29.85% |
| AP مجمع منOOF | 0.3100 |
| الإشارات | 526 من 5,039 |
| FN / FP | 227 / 369 |
| الخسارة التعليمية | 2639 وحدة |
| خسارة0.5 | 2403 وحدة؛ ضمن السعة: False |
| التغير عن0.5 | +236 وحدة؛ الموجب زيادة |
| خسارة لكل10,000 طلب، تطبيع حسابي | 5237.15 وحدة |
| فجوة معدل الإنذار الخاطئ بين المناطق | 0.648 نقطة مئوية |

**القاعدة:** درجة ≥ 0.6583471436014694 تعني إشارة مراجعة داخل التمرين؛ غير ذلك بلا إشارة. لا تتخذ موافقة أو رفض تمويل حقيقي. احفظ الدقة الكاملة؛ تقريب العتبة قد يغيّر حجم الطابور.

**السياسة:** FN=10 وFP=1 وحدات تعليمية، وسعة 12% لكل فترة بعد التقريب لأسفل. ليست ريالات فعلية أو رسوم أدوات أو خصمًا من الدرجة.

**دليل السعة:** الفترة 1: 137/195, الفترة 2: 183/200, الفترة 3: 206/207.

## لماذا اخترت هذه العتبة؟
The threshold 0.6583 is the lowest-loss rule that keeps flags under the 12% capacity in every period (8.4%, 10.9% and 11.9%). The default 0.5 flags 19.9% of requests, so it is not feasible. The threshold also stays the same when the FN cost is 8, 10 or 12, so the decision is driven by capacity, not by the exact cost value.

## الخسارة والسعة
Respecting capacity costs 2,639 loss units, which is +236 vs the default 0.5 (2,403) and +364 vs the unconstrained minimum at 0.449 (2,275). Recall drops from 57.8% to 40.9%, but precision rises from 22.1% to 29.9% and false positives fall from 783 to 369, with 526 flags instead of 1,005.

## فرق المناطق وما يحتاج إلى مراجعة
With one shared threshold, the false-positive rate ranges from 7.6% (other) to 8.3% (western), a gap of 0.65 percentage points, and every region has more than 1,000 non-default cases. Recall varies more, from 34.6% (western) to 48.1% (other), so recall by region needs review and monitoring. This is a descriptive audit on synthetic data, not a fairness certification.

## حدود النتيجة
The threshold was chosen on the same OOF development rows, so the reported loss is optimistic and is not final-test performance. OOF covers only 5,039 of 10,000 rows (50.39%). The weighted scores are not calibrated probabilities. Period 3 is at 11.9%, very close to the 12% ceiling, so future volume changes could break capacity. The data is synthetic.

OOF تغطي 50.39% من التدريب و100% من الصفوف المؤهلة؛ 4,961 صفًا تمهيديًا بلا تنبؤ. اختيار العتبة وتقدير خسارتها هنا يستخدمان أهدافOOF نفسها؛ هذه نتيجة تطوير لا اختبار نهائي. لم نستخدم التحدي. المقارنة الجغرافية وصفية وليست شهادة عدالة، والأوزان لا تضمن معايرة الدرجات.

## سؤالك الأول: لماذا قد تخدعكAccuracy؟
Only 7.6% of requests default. Flagging nobody gives 92.4% accuracy with 0% recall, and the unweighted model at 0.5 has 92.6% accuracy but catches only 12.5% of defaults. Accuracy rewards ignoring the rare class, so we use AP, recall, precision, flags and loss instead.

## سؤالك الثاني: لماذا تختار علىOOF؟
Training predictions come from models that already saw those labels, so they are over-confident and a threshold chosen on them would be too optimistic. OOF predictions come from models that did not see those rows, so they behave more like new applications and give a more honest basis for choosing the threshold.

أدلتك في `artifacts/threshold_metrics.json` و`day3_period_capacity.csv` و`day3_region_audit.csv` و`day3_cost_sensitivity.csv` و`cost_curve.png`. الحساسية سيناريوهات ±20% لخسارةFN، وليست فترات ثقة. راجع السعة والمعايرة عند تغير البيانات؛ لا تفترض ثباتهما مستقبلًا.
