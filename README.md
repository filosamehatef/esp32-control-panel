# SFA Control Panel (Firebase + ESP32)

## وصف المشروع
يهدف هذا المشروع إلى التحكم في لوحة تحكم SFA باستخدام ESP32 كجهاز مادي وواجهة ويب تعتمد على Firebase.

### الميزات
- التحكم في المحركات (Motor 1 و Motor 2).
- عرض مستوى المياه في الخزان.
- أوضاع تشغيل مختلفة:
  - التحكم اليدوي (Manual Mode).
  - التحكم التلقائي (Automatic Mode).
- دعم وضع التحكم عبر الجوال أو اللوحة.

## المكونات المطلوبة
1. ESP32.
2. لوحة تحكم (Control Panel) تحتوي على:
   - محركات.
   - مستشعرات مستوى المياه.
   - مكبر صوت طوارئ.
3. اتصال Wi-Fi.
4. Firebase Realtime Database.

## تشغيل المشروع

### 1. إعداد Firebase
1. قم بإنشاء مشروع جديد في Firebase.
2. تفعيل قاعدة البيانات في الوقت الحقيقي (Realtime Database).
3. نسخ **API Key** و **Database URL** واستبدالهما في ملف `firmwarecode.ino` وملف `index.html`.

### 2. إعداد ESP32
1. تحميل الكود الموجود في `firmwarecode.ino` إلى ESP32.
2. التأكد من تثبيت المكتبات التالية في Arduino IDE:
   - WiFi.h
   - WebServer.h
   - HTTPClient.h
   - ArduinoJson.h
3. استبدال بيانات Wi-Fi (SSID وكلمة المرور) في الكود.

### 3. تشغيل واجهة المستخدم
1. افتح ملف `index.html` في متصفح ويب.
2. تأكد من أن ESP32 متصل بـ Firebase.

## كيفية الاستخدام
- افتح واجهة المستخدم.
- اختر وضع التحكم (Mobile أو Panel).
- استخدم الأزرار لتشغيل/إيقاف المحركات أو تغيير الأوضاع.

## ملاحظات
- تأكد من أن ESP32 وواجهة المستخدم لديهما اتصال إنترنت مستقر.
- قم بتحديث البرامج الثابتة لـ ESP32 إذا لزم الأمر.

```shell
# تثبيت المكتبات
arduino-cli lib install WiFi
arduino-cli lib install WebServer
arduino-cli lib install HTTPClient
arduino-cli lib install ArduinoJson
```
