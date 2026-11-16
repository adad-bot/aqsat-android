# نسخهٔ اندروید «اقساط من»

> اگر برنامه‌نویس نیستی، اول [راهنمای-نصب.md](راهنمای-نصب.md) را بخوان؛ همان‌جا قدم‌به‌قدم تا نصب روی گوشی توضیح داده شده است. این فایل برای کسی است که با Gradle و Android آشناست.

پوستهٔ بومی اندروید که همان رابط وب `../index.html` را در `WebView` و کاملاً آفلاین اجرا می‌کند. داده‌ها در حافظهٔ محلی خود اپ ذخیره می‌شوند و اپ هیچ مجوز اینترنتی ندارد.

- `minSdk 24` (اندروید ۷ و بالاتر)
- `targetSdk 34`
- بدون هیچ کتابخانهٔ بیرونی (بدون AndroidX/Compose) — بیلد سریع و سبک

## ساخت APK

### راه ۱ — بدون نصب هیچ ابزاری: GitHub Actions

پوشهٔ `android` را در یک ریپوی GitHub بگذار (root ریپو همان پوشه باشد) و push کن. فایل `.github/workflows/build-apk.yml` خودش APK می‌سازد:

1. در GitHub وارد تب **Actions** شو
2. ورک‌فلوی **Build APK** ← آخرین اجرا
3. در پایین صفحه، artifact با نام **aqsat-debug-apk** را دانلود کن؛ داخلش `app-debug.apk` است.

این روش وقتی به کار می‌آید که دانلود Android SDK از شبکهٔ خودت مسدود باشد (Google دانلودهای SDK را برای بعضی IPها ۴۰۴ می‌کند).

### راه ۲ — Android Studio

پوشهٔ `android` را با `Open` باز کن، صبر کن Gradle sync تمام شود، بعد:

`Build ▸ Build Bundle(s) / APK(s) ▸ Build APK(s)`

خروجی: `app/build/outputs/apk/debug/app-debug.apk`

### راه ۳ — خط فرمان با SDK محلی

```bash
gradle assembleDebug
```

(یا `./gradlew assembleDebug` اگر wrapper را ساخته باشی)

## نصب روی گوشی

فایل APK را به گوشی منتقل کن و باز کن. اندروید اجازهٔ «نصب از منابع ناشناس» می‌خواهد؛ برای فایل‌منیجر یا مرورگری که APK را باز می‌کند فعالش کن.

## امضا و انتشار در استور

APK فعلی با کلید `debug` امضا می‌شود؛ برای استفادهٔ شخصی کافی است، اما کافه‌بازار/مایکت/گوگل‌پلی نسخهٔ release با کلید خودت را می‌خواهند:

```bash
keytool -genkey -v -keystore aqsat.jks -keyalg RSA -keysize 2048 -validity 10000 -alias aqsat
```

بعد در `app/build.gradle.kts` یک `signingConfigs` بساز و `release` را با آن امضا کن، سپس:

```bash
gradle assembleRelease
```

## نکته‌ها

- بروزرسانی رابط کاربری: فایل `app/src/main/assets/index.html` را با نسخهٔ جدید `../index.html` جایگزین کن و دوباره بیلد بگیر.
- پاک‌کردن داده‌های اپ (از تنظیمات اندروید) یعنی پاک‌شدن همهٔ اقساط؛ از داخل اپ پشتیبان `JSON` بگیر.
- اپ مجوز `INTERNET` ندارد؛ پس هیچ داده‌ای بیرون نمی‌رود.

## ساختار

```
android/
├─ settings.gradle.kts
├─ build.gradle.kts
├─ app/
│  ├─ build.gradle.kts
│  └─ src/main/
│     ├─ AndroidManifest.xml
│     ├─ java/ir/aqsat/app/MainActivity.java
│     ├─ assets/index.html      ← همان اپ وب
│     └─ res/                   ← آیکن، تم، رشته‌ها
└─ .github/workflows/build-apk.yml
```
