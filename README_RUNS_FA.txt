نسخه‌ی کامنت‌گذاری‌شده‌ی PAMNet QM9
===================================

برای استفاده، فایل‌های زیر را جایگزین فایل‌های هم‌نام در پروژه کن:

  main_qm9.py
  models.py

بقیه‌ی ساختار پروژه مثل layers.py، datasets.py، utils.py و پوشه‌ی data بدون تغییر می‌ماند.

حالت‌های قابل اجرا
------------------

  original : معماری اصلی PAMNet/PAMNet_s
  ws       : فقط weight sharing
  br       : فقط basis reduction
  wsbr     : ترکیب weight sharing و basis reduction

پیش‌فرض دیتاست
--------------

به صورت پیش‌فرض همان QM9-5k اجرا می‌شود:

  train = 4000
  val   = 500
  test  = 500

یعنی همان کاری که در کد ساده با این بخش انجام شده بود:

  small_dataset = dataset[:5000]
  train_dataset = small_dataset[:4000]
  val_dataset   = small_dataset[4000:4500]
  test_dataset  = small_dataset[4500:5000]

در نسخه‌ی جدید این اعداد hard-code نیستند و با آرگومان‌های زیر قابل تغییرند:

  --train_size
  --val_size
  --test_size

رفتار target در QM9
-------------------

رفتار پیش‌فرض مطابق پروژه‌ی اصلی PAMNet حفظ شده است:

  --target 7  -> ستون واقعی 12 یعنی U0_atom
  --target 8  -> ستون واقعی 13 یعنی U_atom
  --target 9  -> ستون واقعی 14 یعنی H_atom
  --target 10 -> ستون واقعی 15 یعنی G_atom

اگر خواستی mapping خاموش شود و خود ستون 7، 8، 9 یا 10 استفاده شود:

  --no_atom_mapping

نمونه دستورها
-------------

1) معماری اصلی PAMNet:
python -u main_qm9.py --dataset QM9 --model PAMNet --variant original --target 7 --epochs 10 --batch_size 32 --dim 128 --n_layer 6 --lr 1e-4 --run_name pamnet_original_t7

2) فقط weight sharing:
python -u main_qm9.py --dataset QM9 --model PAMNet --variant ws --target 7 --epochs 10 --batch_size 32 --dim 128 --n_layer 6 --lr 1e-4 --run_name pamnet_ws_t7

3) فقط basis reduction:
python -u main_qm9.py --dataset QM9 --model PAMNet --variant br --target 7 --epochs 10 --batch_size 32 --dim 128 --n_layer 6 --lr 1e-4 --run_name pamnet_br_t7

4) مدل پیشنهادی، یعنی weight sharing + basis reduction:
python -u main_qm9.py --dataset QM9 --model PAMNet --variant wsbr --target 7 --epochs 10 --batch_size 32 --dim 128 --n_layer 6 --lr 1e-4 --run_name pamnet_wsbr_t7

5) baseline با PAMNet_s:
python -u main_qm9.py --dataset QM9 --model PAMNet_s --variant original --target 7 --epochs 10 --batch_size 32 --dim 128 --n_layer 6 --lr 1e-4 --run_name pamnets_original_t7

فایل‌های خروجی
--------------

  results/<run_name>_epochs.csv   لاگ هر epoch
  results/summary.csv             خلاصه‌ی هر run برای جدول مقایسه
  results/<run_name>_config.json  تنظیمات دقیق همان run
  save/QM9/<run_name>/best_model.pt  بهترین checkpoint براساس validation MAE

گزارش‌هایی که در terminal چاپ می‌شوند
-------------------------------------

  - تعداد پارامترهای قابل‌آموزش
  - بهترین Validation MAE
  - Test MAE مربوط به بهترین Validation
  - زمان کل آموزش
  - میانگین زمان هر epoch
  - زمان inference روی test
  - peak CUDA memory allocated/reserved

خلاصه‌ی تغییرات نسبت به فایل‌های ساده‌ی ارسالی
----------------------------------------------

main_qm9.py:
  - fixed split 4000/500/500 به آرگومان‌های قابل تنظیم تبدیل شد.
  - variant اضافه شد تا original/ws/br/wsbr بدون تغییر دستی کد اجرا شوند.
  - target mapping به تابع جداگانه منتقل شد.
  - evaluation و inference timing جدا شدند.
  - خروجی CSV/JSON اضافه شد.
  - best model در پوشه‌ی مخصوص هر run ذخیره می‌شود.
  - گزارش زمان آموزش، زمان inference و حافظه‌ی CUDA اضافه شد.

models.py:
  - Config توسعه داده شد تا flagهای use_weight_sharing و use_basis_reduction را نگه دارد.
  - تعداد RBF/SBF از Config کنترل می‌شود.
  - weight sharing به صورت شرطی اجرا می‌شود.
  - برای حالت original، ModuleList با لایه‌های جداگانه ساخته می‌شود.
  - برای ws/wsbr، فقط یک local/global layer ساخته و چند بار تکرار می‌شود.
  - کامنت‌های توضیحی برای graph محلی، graph سراسری، محاسبه‌ی فاصله، زاویه، basis embedding، message passing و attention fusion اضافه شد.
