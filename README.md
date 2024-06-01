

# API معاملات INVEX

## مقدمه

**Invex** یک پلتفرم پیشرو در معاملات که راه‌حل‌های نوآورانه‌ای برای بازارسازها و دیگر کاربران ارائه
می‌دهد. هدف ما قدرت‌بخشی به معامله‌گران با ابزارهای پیشرفته، زیرساخت‌های قوی، و خدمات بی‌نظیر مشتری است.

در Invex، مأموریت ما آسان‌سازی دسترسی به بازارهای مالی در عین حفظ رعایت سختگیرانه قوانین و نظارت‌های نظارتی است. ما
تلاش می‌کنیم عملکرد، قابلیت اطمینان و مقیاس‌پذیری بی‌نظیری برای ارزهای دیجیتال ارائه دهیم.

پلتفرم ما به طیف وسیعی از مشتریان، از جمله سرمایه‌گذاران نهادی، شرکت‌های معامله‌گری اختصاصی و معامله‌گران فردی که به
دنبال بهینه‌سازی بازده هستند، خدمت می‌کند.

آماده‌اید از قابلیت‌های قدرتمند Invex بهره‌مند شوید؟ همین امروز با ثبت‌نام در یک حساب و دسترسی به منابع گسترده ما شروع
کنید.

---

## احراز هویت و مجوز

برای استفاده از Invex، ابتدا باید اطلاعات تماس اولیه را ارائه دهید، با شرایط و ضوابط ما موافقت کنید و مراحل **تأیید هویت
سطح ۲** را تکمیل کنید، همچنین رمز دومرحله ای را فعال کنید تا بتوانید API را دریافت کنید.

برای دریافت API در پنل به قسمت **مدیریت API** رفته و ساخت API جدید را بزنید.

پس از تأیید، یک جفت کلید API
شامل یک کلید عمومی، یک کلید API برای استفاده در هدر (**X-API-Key-Invex**) و یک کلید شخصی دریافت خواهید کرد.

**توجه:** کلید API و کلید شخصی شما به عنوان یک شناسه امن عمل می‌کند که به شما اجازه می‌دهد درخواست‌ها را امضا کرده و
هویت خود را تأیید کنید. نگه داشتن این اطلاعات به صورت محرمانه بسیار مهم است؛ هرگز آن را با کسی خارج از افراد مجاز به
اشتراک نگذارید. اگر مشکوک به فعالیت غیرمجاز یا نقض امنیتی شدید، بلافاصله به تیم پشتیبانی ما اطلاع دهید یا کلید API خود
را از طریق داشبورد خود حذف کنید.

علاوه بر این، INVEX چندین تدابیر امنیتی برای محافظت در برابر فعالیت‌های مخرب پیاده‌سازی می‌کند:

- **پروتکل‌های رمزنگاری**: تمام ارتباطات بین سرورها و برنامه‌های مشتری از رمزنگاری استاندارد صنعت TLS v1.2+ استفاده
  می‌کند.
- **پیشگیری از حملات DDoS**: سیستم‌های ما تلاش برای استفاده مجدد از پیام‌های ارسال شده قبلی را شناسایی و مسدود می‌کنند.

##### چگونه داده‌های ورودی را امضا کنیم؟

برای هر endpoint با داده‌های ورودی، باید داده‌های ورودی خود را امضا کنید. برای این کار، باید یک فیلد `expire_at` در
پارامترهای query یا

بدنه(body) درخواست خود ارائه دهید. به نمونه موجود در درخواست‌های زیر نگاهی بیندازید. اما چگونه داده‌های خود را امضا
کنید؟

در اینجا یک نمونه کد در پایتون آمده است:

```python
import binascii
from cryptography.hazmat.primitives.serialization import load_der_private_key
from cryptography.hazmat.primitives.asymmetric import padding
from cryptography.hazmat.primitives import hashes


def sign_using_private_key():
    private_key = ...  # کلید مخفی API شما
    message = ...  # داده‌های ورودی کاربر به صورت JSON کد شده، شامل expire_at
    byte_private_key = binascii.unhexlify(private_key)
    rsa_private_key = load_der_private_key(byte_private_key, password=None)
    signature = rsa_private_key.sign(
        message.encode(),
        padding.PSS(
            mgf=padding.MGF1(hashes.SHA256()),
            salt_length=padding.PSS.MAX_LENGTH
        ),
        hashes.SHA256()
    )
    return signature.hex()


if __name__ == '__main__':
    print(sign_using_private_key())
```

از خروجی به عنوان امضا (signature) برای درخواست خود استفاده کنید.

#### دسترسی ها

دسترسی‌ها نقش مهمی در تعیین اقدامات مجاز شما در پلتفرم دارند. هر دسترسی مجموعه‌ای از مجوزها را به کلید API شما می‌دهد.
به عنوان مثال، دسترسی `trade` اجازه می‌دهد سفارش‌ها را ثبت و تغییر دهید، در حالی که دسترسی `read` دسترسی را به
عملیات‌های فقط خواندنی محدود می‌کند.

هنگام ایجاد یک کلید API، یک یا چند دسترسی را بر اساس استفاده خود مشخص کنید. جدول زیر دسترسی‌های معمول مورد استفاده را
خلاصه می‌کند:

| دسترسی | توضیحات                            | مجوزها              |
|--------|------------------------------------|---------------------|
| معامله | دسترسی ثبت و لغو سفارش‌ها          | ایجاد، حذف سفارش‌ها |
| خواندن | دسترسی فقط خواندنی به اطلاعات حساب | مشاهده تاریخچه حساب |
| برداشت | دسترسی برداشت از  حساب             | برداشت از حساب      |

---

## قوانین بازار

[نمونه درخواست](/swagger/#/TRADING_API/get_trading_rules_trading_v1_markets_get)

قبل از اجرای معاملات، قوانین بازار را که هر نماد را تنظیم می‌کنند، بررسی کنید. این مقررات اندازه‌های tick،
amount_step_size و حداقل/حداکثر مقادیر سفارش را تعیین می‌کنند. از  `/markets` برای مشاهده‌ی پیکربندی‌های فعلی
استفاده کنید.

پاسخ نمونه:

```json
{
  "symbol": "USDT_IRR",
  "tick_size": "10.000000000000000000",
  "amount_step_size": "0.010000000000000000",
  "minimum_limit_order_value": "1000000.000000000000000000",
  "maximum_limit_order_value": "10000000000.000000000000000000"
}
```

توجه داشته باشید که قوانین بازار ممکن است به دلیل عواملی مانند نوسانات، رویدادهای خبری یا مداخلات نظارتی به صورت دینامیک
تغییر کنند. همیشه قبل از اجرای معاملات، جدیدترین پیکربندی‌ها را بررسی کنید.

---

## سرویس معامله

شما می‌توانید سفارش‌ با قیمت مشخص و سفارش بازار را ثبت کنید و مقدار، سمت (خرید/فروش) و در صورت تمایل قیمت را مشخص کنید.

ثبت سفارش از طریق `/orders` صورت می‌گیرد که درخواست‌های POST حاوی payloadهای JSON را می‌پذیرد. پس از ارسال
موفقیت‌آمیز، پاسخی دریافت خواهید کرد که تأییدیه شناسه سفارش را ارائه می‌دهد.

سفارش‌های بازار بلافاصله اجرا می‌شوند و به شرایط بازار بستگی دارند. سفارش‌های قیمت مشخص، باز می‌مانند تا پر یا لغو شوند.

اجرای معامله از یک رویه استاندارد پیروی می‌کند:

1. مشتری یک سفارش ثبت می‌کند.
2. مچ‌انجین به دنبال طرف‌های مناسب می‌گردد.
3. طرفین بر روی شرایط تسویه موافقت می‌کنند.
4. معامله ثبت می‌شود.
5. وجوه به طور متناسب منتقل می‌شود.

---

## کانال‌های پشتیبانی

اگر با مشکلاتی روبرو شدید یا نیاز به کمک داشتید، دریغ نکنید با کارکنان دوستانه ما تماس بگیرید. ما راه‌های متعددی برای
ارتباط ارائه می‌دهیم:

- ایمیل: [support@invex.ir](mailto:support@invex.ir)
- گفتگوی زنده: در ساعات کاری در دسترس است
- تلفن: +1 (XXX)-XXX-XXXX (شنبه – چهارشنبه، 9 صبح – 5 بعد از ظهر)

ما به پاسخ‌های سریع و رفع مشکلات کارآمد افتخار می‌کنیم. در صورت تجربه تاخیر طولانی یا نتایج غیررضایت‌بخش، مسائل را به
تیم رهبری ارشد ما ارجاع دهید.

---

## سوالات متداول

سوالات متداول مرتبط با عملکرد و قابلیت‌های Invex:

**چه جفت ارزهایی پشتیبانی می‌شوند؟**

در حال حاضر، Invex از جفت ارزهای مختلفی پشتیبانی می‌کند. برای مشاهده لیست آن‌ها، نقطه انتهایی **قوانین معامله** را
فراخوانی کنید.

**قیمت‌ها چند وقت یک بار به روز می‌شوند؟**

قیمت‌ها هر ثانیه به‌روزرسانی می‌شوند تا نمایشی دقیق از حرکات بازار ارائه دهند.

**آیا هزینه‌ای برای معاملات وجود دارد؟**

بله، Invex کارمزدی بین 0.0% تا 0.5% بسته به سطح معاملاتی حساب، دریافت می‌کند.

