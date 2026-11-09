# تسابيح — مشروع Flutter

## التشغيل
1. ثبّت Flutter (flutter.dev) ثم من مجلد المشروع:
   ```
   flutter create . --org com.yourname --project-name tasabeeh
   flutter pub get
   flutter run
   ```
   (الأمر الأول يولّد مجلد android فقط ولا يمس ملفات lib)

## إعداد أندرويد (مطلوب للتذكيرات)
**android/app/src/main/AndroidManifest.xml** — قبل وسم `<application>`:
```xml
<uses-permission android:name="android.permission.POST_NOTIFICATIONS"/>
<uses-permission android:name="android.permission.VIBRATE"/>
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
```
وداخل `<application>`:
```xml
<receiver android:exported="false" android:name="com.dexterous.flutterlocalnotifications.ScheduledNotificationReceiver"/>
<receiver android:exported="false" android:name="com.dexterous.flutterlocalnotifications.ScheduledNotificationBootReceiver">
  <intent-filter>
    <action android:name="android.intent.action.BOOT_COMPLETED"/>
    <action android:name="android.intent.action.MY_PACKAGE_REPLACED"/>
  </intent-filter>
</receiver>
```
**android/app/build.gradle** — داخل `android { compileOptions { ... } }` أضف `coreLibraryDesugaringEnabled true`، وفي `dependencies`:
```
coreLibraryDesugaring 'com.android.tools:desugar_jdk_libs:2.0.4'
```

## النشر على Google Play
1. غيّر اسم الحزمة (applicationId) إلى معرّفك الخاص، وبدّل أيقونة التطبيق (حزمة flutter_launcher_icons).
2. أنشئ مفتاح توقيع: `keytool -genkey -v -keystore upload.jks -keyalg RSA -keysize 2048 -validity 10000 -alias upload` واربطه عبر `android/key.properties`.
3. `flutter build appbundle --release` ← الملف الناتج: `build/app/outputs/bundle/release/app-release.aab`
4. أنشئ حساب مطوّر (٢٥$ مرة واحدة)، وارفع الملف، وأضف لقطات الشاشة وسياسة الخصوصية (التطبيق لا يجمع بيانات).

---
## إضافة المصحف الكامل (مهم)
1. من tanzil.net/download اختر: Quran Text Type = **Uthmani**، Output = **Text (with aya numbers)**، ثم نزّل الملف.
2. سمّه `quran-uthmani.txt` وضعه في `assets/quran/`. التطبيق يزيل تكرار البسملة تلقائيًا ويعرض السور الـ١١٤.
3. شروط Tanzil: اذكر مصدر النص (Tanzil.net) داخل التطبيق ولا تعدّل النص.
4. بدون الملف تظهر ٦ سور قصيرة للتجربة فقط. راجعها على مصحف موثوق قبل النشر.

## الخط
أضف خط Amiri (مجاني) إلى assets/fonts وفعّل كتلة fonts في pubspec.yaml لتظهر الآيات بخط مصحف جميل.

## التذكير التلقائي بالأذكار
من الإعدادات فعّل تذكير الصباح (الافتراضي ٦:٠٠ ص) وتذكير المساء (الافتراضي ٥:٣٠ م) وغيّر الوقت بالضغط عليه. الضغط على الإشعار يفتح تبويب الأذكار على الوضع المناسب.
- يلزم تعديل AndroidManifest وbuild.gradle كما في قسم "إعداد أندرويد" أعلاه.
- أندرويد ١٣ فما فوق يسأل عن إذن الإشعارات عند أول تفعيل.
- قد يتأخر الإشعار بضع دقائق لتوفير البطارية. بعض الأجهزة (شاومي، هواوي، سامسونج) تحتاج استثناء التطبيق من توفير البطارية.

## أوقات الصلاة
- تُحسب بدون إنترنت بخوارزمية فلكية، مع ٩ طرق حساب (أم القرى، رابطة العالم الإسلامي، المصرية، كراتشي، ISNA، الكويت، قطر، الخليج، المغرب)، وخيار العصر (الجمهور أو الحنفي)، وضبط يدوي بالدقائق لكل صلاة.
- أضف في AndroidManifest (قبل وسم application):
```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
```
- إشعارات الصلاة تستخدم جدولة غير دقيقة لتوفير البطارية وقد تتأخر دقائق. لجعلها دقيقة بدّل `inexactAllowWhileIdle` إلى `exactAllowWhileIdle` في `Notifier.prayerAt` وأضف إذن `SCHEDULE_EXACT_ALARM` (وراجع سياسة Google Play لهذا الإذن).
- أم القرى: العشاء بعد المغرب بـ٩٠ دقيقة، وفي رمضان زِد ٣٠ دقيقة يدويًا من «ضبط الدقائق».
- تحقق من الأوقات بمقارنتها مع تقويم رسمي لمدينتك قبل النشر.
