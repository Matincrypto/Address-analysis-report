# Address-analysis-report

# تحلیلگر تراکنش‌های کریپتویی

این پروژه یک اسکریپت پایتون است که به شما امکان می‌دهد تراکنش‌های کریپتویی خود را از یک فایل CSV تحلیل کرده و گزارش‌های مفیدی را در قالب یک فایل اکسل تمیز و فرمت‌بندی شده دریافت کنید.

## قابلیت‌ها

* **خواندن خودکار فایل CSV**: فایل تراکنش‌های شما را خوانده و ستون‌های لازم را شناسایی می‌کند.
* **پیش‌پردازش داده‌ها**: داده‌های زمانی و مقادیر عددی را به فرمت صحیح تبدیل می‌کند.
* **محاسبه موجودی آدرس**: موجودی توکن‌های یک آدرس کیف پول مشخص را (بر اساس تراکنش‌های ورودی و خروجی) محاسبه و نمایش می‌دهد.
* **شناسایی تراکنش‌های بزرگ**: تراکنش‌هایی را که مقدار توکن آن‌ها از یک آستانه مشخص بیشتر است، شناسایی و لیست می‌کند.
* **شناسایی آدرس‌های پرفعالیت**: آدرس‌هایی که تعداد انتقال‌های (ارسالی یا دریافتی) آن‌ها از یک آستانه مشخص بیشتر است، را شناسایی می‌کند.
* **خروجی اکسل حرفه‌ای**: تمام نتایج تحلیل را در یک فایل اکسل با شیت‌های مجزا، فرمت‌بندی زیبا (رنگ‌بندی سربرگ‌ها، تراز متن، تنظیم خودکار عرض ستون‌ها) و فریز کردن سربرگ‌ها ذخیره می‌کند.

## پیش‌نیازها

قبل از اجرای کد، مطمئن شوید که پایتون (Python 3.x) روی سیستم شما نصب است. سپس، کتابخانه‌های زیر را نصب کنید. می‌توانید از دستورات زیر در ترمینال یا Command Prompt استفاده کنید:

```bash
pip install pandas openpyxl XlsxWriter numpy
pandas: برای کار با داده‌ها و DataFrames.

openpyxl: یک بک‌اند برای خواندن و نوشتن فایل‌های .xlsx.

XlsxWriter: یک بک‌اند پیشرفته‌تر برای نوشتن و فرمت‌بندی فایل‌های .xlsx که در این پروژه برای فرمت‌بندی بهتر استفاده شده است.

numpy: برای برخی عملیات عددی که توسط pandas استفاده می‌شود.

نحوه استفاده
برای استفاده از این تحلیلگر، مراحل زیر را دنبال کنید:

1. آماده‌سازی فایل CSV تراکنش‌ها
فایل CSV تراکنش‌های شما باید شامل ستون‌های زیر (با همین نام‌های دقیق) باشد:

Txn Hash

Block

Time (UTC)

Transaction Type

Method ID

Method Name

From

To

Token

Token Symbol

Amount

Result

Status

محل قرارگیری فایل CSV:

روش توصیه شده (ساده‌ترین): فایل CSV خود را (مثلاً Transactions_20250630.csv) در همان پوشه‌ای که فایل اسکریپت پایتون (.py) را ذخیره کرده‌اید، قرار دهید.

روش جایگزین: اگر می‌خواهید فایل CSV در مسیر دیگری باشد، باید مسیر کامل (Absolute Path) آن را در کد مشخص کنید (مثلاً C:/Users/YourUser/Documents/Transactions_20250630.csv در ویندوز یا /Users/YourUser/Documents/Transactions_20250630.csv در macOS/Linux).

2. پیکربندی اسکریپت پایتون
فایل پایتون را (مثلاً با نام crypto_analyzer.py) باز کنید و قسمت --- تنظیمات و پارامترها --- را ویرایش کنید:

Python

# --- تنظیمات و پارامترها ---
FILE_PATH = 'Transactions_20250630.csv' # <<< نام فایل CSV شما. اگر در پوشه دیگری است، مسیر کامل را اینجا وارد کنید.
OUTPUT_EXCEL_FILE = 'crypto_analysis_report_formatted.xlsx' # نام فایل اکسل خروجی (می‌توانید تغییر دهید)
TARGET_ADDRESS = 'YOUR_WALLET_ADDRESS_HERE' # <<< آدرس کیف پولی که می‌خواهید موجودی آن را محاسبه کنید. **این را حتماً تغییر دهید!**
TOKEN_AMOUNT_THRESHOLD = 100 # <<< آستانه برای شناسایی "تراکنش‌های بزرگ" بر اساس مقدار توکن (می‌توانید تغییر دهید)
TRANSFER_COUNT_THRESHOLD = 4 # <<< آستانه برای شناسایی "آدرس‌های پرفعالیت" بر اساس تعداد انتقال (می‌توانید تغییر دهید)
FILE_PATH: نام یا مسیر فایل CSV تراکنش‌های شما.

OUTPUT_EXCEL_FILE: نام فایل اکسلی که گزارش نهایی در آن ذخیره می‌شود.

TARGET_ADDRESS: این مهمترین پارامتر است. آن را با آدرس کیف پول خود که می‌خواهید موجودی آن را تحلیل کنید، جایگزین کنید.

TOKEN_AMOUNT_THRESHOLD: عددی که نشان می‌دهد چه مقداری از یک توکن، یک "تراکنش بزرگ" محسوب می‌شود. به عنوان مثال، اگر آن را 100 قرار دهید، تراکنش‌هایی که مقدار توکن در آن‌ها 100 یا بیشتر است، در گزارش "تراکنش‌های بزرگ" لیست می‌شوند.

TRANSFER_COUNT_THRESHOLD: عددی که نشان می‌دهد چند انتقال (چه ورودی و چه خروجی) برای یک آدرس، آن را به عنوان یک "آدرس پرفعالیت" مشخص می‌کند. به عنوان مثال، اگر آن را 4 قرار دهید، آدرس‌هایی که بیش از 4 انتقال داشته‌اند، در گزارش "آدرس‌های پرفعالیت" لیست می‌شوند.

3. اجرای اسکریپت
پس از انجام تنظیمات بالا:

ترمینال یا Command Prompt را باز کنید.

با استفاده از دستور cd به پوشه‌ای بروید که فایل پایتون را در آن ذخیره کرده‌اید.
مثال: cd C:\Users\YourUser\Documents\CryptoAnalyzer

دستور زیر را برای اجرای اسکریپت وارد کنید:

Bash

python your_script_name.py
(به جای your_script_name.py، نامی که فایل پایتون خود را با آن ذخیره کرده‌اید بنویسید، مثلاً python crypto_analyzer.py)

4. مشاهده گزارش
پس از اتمام اجرای اسکریپت، یک فایل اکسل با نام crypto_analysis_report_formatted.xlsx (یا نامی که برای OUTPUT_EXCEL_FILE تعیین کرده‌اید) در همان پوشه اسکریپت شما ایجاد می‌شود. این فایل شامل سه شیت مجزا خواهد بود:

موجودی_آدرس: شامل موجودی تفکیکی توکن‌ها برای TARGET_ADDRESS شما.

تراکنش‌های_بزرگ: لیستی از تراکنش‌هایی که مقدار توکن آن‌ها بالای TOKEN_AMOUNT_THRESHOLD بوده است.

آدرس‌های_پرفعالیت: لیستی از آدرس‌هایی که تعداد انتقال‌های آن‌ها از TRANSFER_COUNT_THRESHOLD بیشتر بوده است.

عیب‌یابی
FileNotFoundError: اگر اسکریپت نتوانست فایل CSV شما را پیدا کند، مطمئن شوید که FILE_PATH را به درستی تنظیم کرده‌اید (یا فایل CSV در کنار فایل پایتون قرار دارد).

خطا: ستون‌های ضروری زیر در فایل یافت نشدند:: نام ستون‌ها در فایل CSV شما با نام‌های مورد انتظار کد مطابقت ندارد. نام ستون‌ها را در فایل CSV خود بررسی کرده و در صورت لزوم، آنها را به نام‌های دقیق ذکر شده در بخش "آماده‌سازی فایل CSV تراکنش‌ها" تغییر دهید.

خطاهای مربوط به Amount یا Time (UTC): اطمینان حاصل کنید که این ستون‌ها حاوی داده‌های معتبر و قابل تبدیل به عدد یا تاریخ/زمان هستند.

