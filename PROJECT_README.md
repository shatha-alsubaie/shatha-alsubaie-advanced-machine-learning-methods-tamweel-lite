# Tamweel Lite | مشروعك النهائي

## الملخص التنفيذي
بعد تحقق زمني صادق بدون تسرب، اخترت نموذج Logistic Regression مفرد لأنه حقق أعلى AP (0.392) وأقل Brier، والتجميع لم يضف قيمة لأن أخطاء النماذج متطابقة تقريبًا. حد القرار 0.1689 يمسك 46.9% من المتعثرين بدقة 34.3% ضمن طاقة 12%، وفي دفعة التحدي رُفع 300 طلب للمراجعة من 2,500. الاستخدام تعليمي فقط على بيانات اصطناعية.

## Executive summary
After honest time- and customer-aware validation with leaked fields removed, I kept a single Logistic Regression: it had the highest mean AP (0.392) and lowest Brier, and ensembles added no stable value because model errors were almost identical. The 0.1689 threshold catches 46.9% of defaults at 34.3% precision within the 12% review capacity, and 300 of 2,500 challenge requests were flagged for review. Teaching use only on synthetic data.

Decision: KEEP SINGLE / Logistic. Full-batch flags: 300/2500.

اقرأ reports/MODEL_CARD.md والسياسة في artifacts/final_policy.json. الحزمة للتدريب؛ ليست نتيجة تقييم نهائية أو إثبات تسليم. ادمج أدلة أيامك السابقة واحفظ الدفتر المنفذ والعرض.
