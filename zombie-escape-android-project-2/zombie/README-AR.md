# بناء ملف AAB للعبة (من الموبايل بدون تثبيت برامج)

⚠️ المشروع ده **ما اتبناش كـ AAB على جهاز حقيقي**. اتجرّب فقط: تشغيل اللعبة في متصفح، وتوليد مشروع أندرويد بـ Capacitor، والتأكد إن أوامر الـ workflow بتلاقي أهدافها. فيه كل الملفات وخطوات البناء السحابي، لكن ملف AAB بيتعمل لما تشغّل البناء على GitHub. لو البناء فشل ابعتلي نص الخطأ.

## الملفات
- `index.html` : اللعبة كاملة (الموديل والـ textures جواه).
- `package.json` + `capacitor.config.json` : إعدادات Capacitor (غيّر `appId` و`appName` قبل النشر، الـ appId لازم يكون فريد على Google Play).
- `github-workflows/build-aab.yml` : بناء AAB وAPK تجريبي.
- `github-workflows/make-keystore.yml` : عمل مفتاح التوقيع (مرة واحدة).

## الخطوات (كلها من متصفح الموبايل)
1. اعمل حساب على github.com، وأنشئ **Repository** جديد وخليه **Private**.
2. فك ضغط الـ ZIP من تطبيق الملفات في الموبايل.
3. في الـ Repo: Add file ← Upload files ← ارفع `index.html` و`package.json` و`capacitor.config.json`، ثم Commit.
4. Add file ← **Create new file**، اكتب الاسم بالظبط: `.github/workflows/build-aab.yml` والصق محتوى `github-workflows/build-aab.yml`، ثم Commit.
5. كرر نفس الخطوة للملف: `.github/workflows/make-keystore.yml`.
6. تبويب **Actions** ← **Make upload keystore** ← Run workflow. بعد ما يخلص نزّل artifact اسمه `keystore-KEEP-PRIVATE` وافتح `secrets.txt`.
7. Settings ← Secrets and variables ← Actions ← New repository secret، وأضف 4 أسرار بنفس الأسماء والقيم: `KEYSTORE_BASE64` و`KEY_ALIAS` و`KEYSTORE_PASSWORD` و`KEY_PASSWORD`.
   - احتفظ بنسخة من `upload.jks` و`secrets.txt` في مكان آمن، ولا ترفعهم على أي Repo عام.
8. Actions ← **Build AAB and APK** ← Run workflow. لما يخلص حمّل:
   - `apk-debug` : ملف APK تجريبي تثبّته على موبايلك وتجرّب اللعبة.
   - `aab` : ملف AAB الموقّع للرفع على Google Play (لو ظهر `app-release-signed.aab` يبقى التوقيع اشتغل).
9. في Google Play Console: أنشئ تطبيق، وارفع الـ AAB في Internal testing الأول، وعبّي بيانات المتجر وسياسة الخصوصية.

## ملاحظات
- كل مرة تبني، رقم النسخة (versionCode) بيزيد تلقائياً.
- المكتبات (three.js وغيرها) بتتنزل وقت البناء وتتحط جوه التطبيق، فاللعبة بتشتغل من غير نت.
- لازم تتأكد من متطلبات target API الحالية في Play Console، وCapacitor 8 هو اللي بيحدد النسخة.
- لازم حساب مطوّر على Google Play (رسوم لمرة واحدة).

## الإعلانات (AdMob)
- المشروع فيه مكتبة `@capacitor-community/admob` وكود الإعلانات في اللعبة: إعلان قبل دخول المرحلة، وإعلان بعد الخسارة، وإعلان مكافأة لمضاعفة الفلوس بعد الفوز.
- لحد ما تحط معرّفاتك الحقيقية، البناء بيستخدم **معرّفات الاختبار الرسمية من Google**، فالإعلانات اللي بتظهر تجريبية.
- لما يكون عندك حساب AdMob: من Settings ← Secrets and variables ← Actions ← تبويب **Variables** أضف: `ADMOB_APP_ID` و`ADMOB_INTERSTITIAL` و`ADMOB_REWARDED`، وبعدها شغّل البناء من جديد.
- على الويب (المعاينة) بيظهر إعلان تجريبي بديل مدته ثواني، مش إعلان حقيقي.
- قبل النشر: حط سياسة خصوصية، وراجع موافقة المستخدمين في أوروبا (UMP)، وعبّي إقرار الإعلانات في Google Play. أنا ما اختبرتش الإعلانات على جهاز حقيقي.

## تعديلات اتعملت في الإصدار ده
- إصلاح خطأ شيدر في الخط الخارجي (outline) للشخصية المتحركة، كان بيطلع `objectNormal undeclared`.
- إضافة نموذج الموافقة UMP قبل أول إعلان (مطلوب في أوروبا)، وبيتخطى الإعلان لو المستخدم رفض.
- `initializeForTesting` بقى بيشتغل فقط لو معرّفات الإعلانات تجريبية.
- تنضيف الـ listeners بعد كل إعلان.
- ملفات جديدة: `privacy-policy.html` (عدّل التاريخ والإيميل وارفعها على GitHub Pages) و`STORE-LISTING.md` (نصوص المتجر والاستبيانات).
