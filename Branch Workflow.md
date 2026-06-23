# Branch Workflow

## RoadMap
    
### 1. أنواع الفروع الأساسية (Branch Types) في مشاريع Flutter

- **`main` / `master`:** الفرع الرئيسي والنهائي. يحتوي دائماً على الكود المستقر والقابل للنشر فوراً (Production-ready). لا يتم التعديل عليه مباشرة أبداً.
- **`develop`:** الفرع الأساسي للتطوير اليومي. تُدمج فيه كل الميزات الجديدة، ومنه تخرج نسخ التجريب (Staging/Beta).
- **Feature Branches (`feature/`)**:
    - يُفتح لإنشاء ميزة جديدة (مثال: `feature/login-screen` أو `feature/state-management`).
    - يُتفرع من `develop` ويعود إلى `develop`.
- **Bugfix Branches (`bugfix/`)**:
    - لإصلاح الأخطاء المكتشفة أثناء التستنج في فرع `develop`.
    - يُتفرع من `develop` ويعود إليه.
- **Hotfix Branches (`hotfix/`)**:
    - **حرج جداً:** لإصلاح مشكلة كارثية ظهرت في تطبيق الـ Production (المتجر) مباشرة.
    - يُتفرع من `main` مباشرة، وبعد الإصلاح يُدمج في `main` و `develop` بالتوازي.
- **Release Branches (`release/`)**:
    - التحضير لرفع النسخة للمتجر (App Store / Play Store). يتم فيه تحديث رقم الإصدار (`pubspec.yaml`) وتهيئة الـ Icons والـ Changelog.

### 2. أشهر استراتيجيات سير العمل (Branching Strategies)

- **Git Flow:** الاستراتيجية الكلاسيكية والأكثر استخداماً في تطبيقات الموبايل الكبيرة (تعتمد على الفروع المذكورة أعلاه: Main, Develop, Feature, Release, Hotfix).
- **GitHub Flow:** استراتيجية بسيطة وسريعة (فرع `main` وفروع ميزات قصيرة الأجل فقط)، ممتازة للمشاريع الصغيرة أو الـ SaaS.
- **Trunk-Based Development:** الدمج المستمر والسريع مباشرة في الفرع الرئيسي (مع استخدام **Feature Flags** في كود الفلاتر لإخفاء الميزات غير المكتملة عن المستخدمين).

### 3. دورة حياة الـ Feature كـ Flutter Developer (الخطوات العملية)

- **التحديث قبل البدء:** التأكد من سحب آخر التعديلات (`git checkout develop` ثم `git pull`).
- **إنشاء الفرع:** تسمية احترافية (`git checkout -b feature/auth-bloc`).
- **التطوير المحلي:** كتابة الأكواد (UI, Controllers/Blocs, Models).
- **إدارة ملفات فلاتر الخاصة (`.gitignore`):** التأكد من عدم رفع الملفات المولدة تلقائياً (مثل `.dart_tool/`, `build/`, `.env`, وملفات الـ IDE كـ `.idea/` أو `.vscode/`).
- **الـ Commits الذكية:** تقسيم الشغل لرسائل واضحة (مثل: `feat: add login UI`, `feat: integrate login API with Retrofit`).
- **الرفع (Push):** رفع الفرع إلى GitHub (`git push origin feature/auth-bloc`).

### 4. ممارسات GitHub وفرق العمل (Pull Requests & Collaboration)

- **فتح طلب الدمج (Pull Request - PR):** من فرع الـ `feature` إلى فرع الـ `develop`.
- **وصف الـ PR (Templates):** كتابة ما تم إنجازه، وإرفاق صور أو فيديوهات (Screenshots/Screen recordings) لشاشات فلاتر قبل وبعد التعديل.
- **مراجعة الكود (Code Review):** قيام المطورين الآخرين بمراجعة الـ Clean Code، والـ Architecture المستخدم (مثل Clean Architecture أو MVC)، والتأكد من عدم وجود تسريب للـ Memory (Memory Leaks).
- **حل النزاعات (Resolving Conflicts):**
    - غالباً تحدث في فلاتر داخل ملف `pubspec.lock` عند تعديل الـ Packages، أو في ملفات الـ native مثل `podfile.lock` (iOS) أو `build.gradle` (Android).
    - يتم حلها محلياً عن طريق عمل `git merge develop` داخل فرعك، وحل التضارب ثم عمل Push.

### 5. الأتمتة وفلاتر (CI/CD integration with Branches)

- **GitHub Actions:** ربط الفروع بأدوات الأتمتة.
- **عند الـ Push لـ `feature/*`:** تشغيل الـ `flutter analyze` للتأكد من جودة الكود، وتشغيل الـ Unit Tests (`flutter test`).
- **عند الدمج في `develop`:** بناء نسخة أندرويد (APK/AAB) ونسخة iOS (IPA) ورفعها تلقائياً لمنصات الفحص مثل Firebase App Distribution أو TestFlight.
- **عند الدمج في `main`:** عمل Release رسمي ورفعه للمتاجر (Google Play / App Store) باستخدام أدوات مثل **Fastlane**.

### 6. أوامر جيت متقدمة لا غنى عنها لمطور الفلاتر (Advanced Git Ops)

- **`git stash`:** لحفظ تعديلاتك الحالية في الفلاتر مؤقتاً جانباً لو احتجت الانتقال لفرع آخر بسرعة لإصلاح بغ (Bug) بدون عمل commit لكود غير مكتمل.
- **`git cherry-pick`:** لأخذ Commit معين (إصلاح بغ مثلاً) من فرع ونقله لفرع آخر تماماً بدون دمج الفروع كاملة.
- **`git rebase`:** لإعادة ترتيب الفروع وجعل تاريخ الـ Commits خطياً ونظيفاً (يُفضل استخدامه بحذر في الفروع المحلية فقط).
- **`git clean -fdx`:** أمر قوي جداً لمسح كل الملفات غير المتبع تتبعها (Untracked)، وممتاز لحل المشاكل الغريبة في كاش الفلاتر (يُستخدم كبديل خارق لـ `flutter clean`).
