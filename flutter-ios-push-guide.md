## 📝 Apple Push Notification Setup for Agora Chat (iOS)

> ⚠️ **This is a guide for setting up Apple Push Notifications with Agora Chat SDK.**  
> This guide supplements the current official documentation, which does not yet fully cover the Apple Push Notification (APNs) setup steps.

---

### 🚀 Purpose

This document guides developers through the process of configuring Apple Push Notification (APNs) with Agora Chat SDK, specifically for iOS platforms. The official guide currently lacks this section.

---

### 🔧 Setup Instructions

#### 1. Upload Certificate for APNs to Agora Console

- Go to [Agora Console](https://console.agora.io/)
- Navigate to **Project > Push Notifications > Apple Push**
- Upload your **APNs Key (.p8)** or **Certificate (.p12)**:
  - Fill in:
    - Certificate Name
    - Key ID
    - Team ID
    - Bundle ID
    - etc.
  - Select environment: **Production** / **Development**

![image](https://github.com/user-attachments/assets/e75804f8-2cf2-4528-b4d6-e50704b102f7)

#### 2. Configure APNs Certificate in SDK

```Dart
import 'package:agora_chat_sdk/agora_chat_sdk.dart';

ChatOptions options = ChatOptions(
  appKey: AgoraChatConfig.appKey,
  autoLogin: false,
  debugModel: true,
);

if (Platform.isIOS) {
  options.enableAPNs(AgoraChatConfig.apnsCertName); // e.g., 'apns_dev' or 'apns_prod'
}

ChatClient.getInstance.init(options);
```
> 📌 apnsCertName should match the certificate name uploaded in the Agora Console.

#### 3. Retrieve APNs Token via Firebase Messaging

```Dart
import 'package:firebase_messaging/firebase_messaging.dart';

String? token = await FirebaseMessaging.instance.getToken();

if (Platform.isIOS) {
  token = await FirebaseMessaging.instance.getAPNSToken();
}
```
> 🛠 Ensure you have initialized Firebase in your main() method and configured your GoogleService-Info.plist.

### 4. Send Token to Agora Chat Server

```Dart
import 'package:agora_chat_sdk/agora_chat_sdk.dart';

if (Platform.isIOS) {
  ChatClient.getInstance.pushManager.updateAPNsDeviceToken(token);
}
```
> 🔐 Make sure this token is updated when app starts and whenever onTokenRefresh is triggered.

---

### ✅ Notes & Tips

- This setup is for iOS only, Android will use FCM directly.
- You must upload a valid APNs certificate (or token) in the Agora Console before sending push notifications.
- For testing push: use a real device, simulators do not support APNs.
- apnsCertName must match the certificate name (without extension) you uploaded to Agora.

---

### 📣 Feedback

If you find this helpful or notice anything missing, please open a GitHub issue or contact `support@agora.io`.
