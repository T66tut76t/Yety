# تعليمات بناء تطبيق Gaming VPN

## متطلبات البناء

### البرامج المطلوبة
1. **Android Studio** (الإصدار 4.0 أو أحدث)
2. **Android SDK** (API Level 28 أو أحدث)
3. **Java Development Kit (JDK)** 8 أو أحدث
4. **Git** لإدارة الإصدارات

### إعداد بيئة التطوير

#### 1. تثبيت Android Studio
```bash
# تحميل Android Studio من الموقع الرسمي
# https://developer.android.com/studio

# تثبيت SDK Tools المطلوبة
# - Android SDK Platform 28
# - Android SDK Build-Tools 30.0.3
# - Android Emulator
```

#### 2. إعداد متغيرات البيئة
```bash
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/tools/bin
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

## خطوات البناء

### 1. استيراد المشروع
```bash
# فك ضغط الملف
tar -xzf gaming_vpn_app_final.tar.gz

# فتح Android Studio
# File > Open > اختيار مجلد gaming_vpn_app
```

### 2. مزامنة المشروع
```bash
# في Android Studio
# Tools > Sync Project with Gradle Files
# انتظار انتهاء التحميل والمزامنة
```

### 3. إعداد التوقيع (للإصدار النهائي)
```bash
# إنشاء keystore جديد
keytool -genkey -v -keystore gaming-vpn-release-key.keystore -alias gaming-vpn -keyalg RSA -keysize 2048 -validity 10000

# إضافة معلومات التوقيع في app/build.gradle
android {
    signingConfigs {
        release {
            storeFile file('gaming-vpn-release-key.keystore')
            storePassword 'your_store_password'
            keyAlias 'gaming-vpn'
            keyPassword 'your_key_password'
        }
    }
}
```

### 4. بناء التطبيق

#### بناء إصدار التطوير (Debug)
```bash
# من سطر الأوامر
cd gaming_vpn_app
./gradlew assembleDebug

# أو من Android Studio
# Build > Build Bundle(s) / APK(s) > Build APK(s)
```

#### بناء إصدار الإنتاج (Release)
```bash
# من سطر الأوامر
./gradlew assembleRelease

# أو من Android Studio
# Build > Generate Signed Bundle / APK
```

### 5. تشغيل الاختبارات
```bash
# اختبارات الوحدة
./gradlew test

# اختبارات التكامل
./gradlew connectedAndroidTest
```

## ملفات APK الناتجة

### مواقع الملفات
```
app/build/outputs/apk/debug/app-debug.apk
app/build/outputs/apk/release/app-release.apk
```

### أحجام الملفات المتوقعة
- **إصدار التطوير:** ~25-30 MB
- **إصدار الإنتاج:** ~20-25 MB (مضغوط)

## التحسينات للإنتاج

### 1. تقليل حجم APK
```gradle
android {
    buildTypes {
        release {
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}
```

### 2. تحسين الأداء
```gradle
android {
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    
    kotlinOptions {
        jvmTarget = '1.8'
    }
}
```

### 3. إعدادات ProGuard
```proguard
# في ملف proguard-rules.pro
-keep class com.gamingvpn.app.** { *; }
-keep class com.google.mlkit.** { *; }
-dontwarn com.google.mlkit.**
```

## اختبار التطبيق

### 1. اختبار على المحاكي
```bash
# إنشاء محاكي جديد
avdmanager create avd -n Huawei_Y6_Prime_2019 -k "system-images;android-28;google_apis;x86"

# تشغيل المحاكي
emulator -avd Huawei_Y6_Prime_2019
```

### 2. اختبار على الجهاز الفعلي
```bash
# تفعيل وضع المطور على الجهاز
# Settings > About phone > Build number (اضغط 7 مرات)

# تفعيل USB Debugging
# Settings > Developer options > USB debugging

# تثبيت التطبيق
adb install app-debug.apk
```

### 3. اختبارات الأداء
```bash
# مراقبة استهلاك الذاكرة
adb shell dumpsys meminfo com.gamingvpn.app

# مراقبة استهلاك البطارية
adb shell dumpsys batterystats com.gamingvpn.app
```

## حل المشاكل الشائعة

### 1. خطأ في المزامنة
```bash
# حذف ملفات البناء
./gradlew clean

# إعادة المزامنة
./gradlew build
```

### 2. مشاكل الأذونات
```xml
<!-- التأكد من وجود الأذونات في AndroidManifest.xml -->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
```

### 3. مشاكل التوقيع
```bash
# التحقق من صحة keystore
keytool -list -v -keystore gaming-vpn-release-key.keystore
```

## نشر التطبيق

### 1. إعداد Google Play Console
- إنشاء حساب مطور
- رفع APK أو AAB
- إضافة وصف ولقطات شاشة

### 2. إعداد التوزيع البديل
- رفع على مواقع APK
- توزيع مباشر
- متاجر تطبيقات بديلة

## الصيانة والتحديثات

### 1. تحديث التبعيات
```gradle
// فحص التحديثات المتاحة
./gradlew dependencyUpdates

// تحديث Gradle Wrapper
./gradlew wrapper --gradle-version 7.4
```

### 2. إصلاح الأخطاء
```bash
# تشغيل أدوات التحليل
./gradlew lint

# إنشاء تقرير الأخطاء
./gradlew lintDebug
```

### 3. إضافة ميزات جديدة
- إنشاء branch جديد
- تطوير الميزة
- اختبار شامل
- دمج في الفرع الرئيسي

## الدعم والمساعدة

### الموارد المفيدة
- [دليل Android Developer](https://developer.android.com/guide)
- [وثائق Kotlin](https://kotlinlang.org/docs/)
- [مجتمع Android على Stack Overflow](https://stackoverflow.com/questions/tagged/android)

### الحصول على المساعدة
- مراجعة هذا الدليل أولاً
- البحث في المشاكل الشائعة
- طلب المساعدة من المجتمع
- الإبلاغ عن الأخطاء للفريق

---

**ملاحظة:** هذا الدليل يغطي الخطوات الأساسية لبناء التطبيق. للحصول على تفاصيل أكثر، راجع الوثائق الرسمية لـ Android Development.

