## 条码扫描器

基于 [zxing](https://github.com/zxing/zxing)，核心库未修改，仅处理 Android 部分。

- 缩小权限
  * 移除网络权限及相关功能
  * 移除通讯录权限及相关功能
  * 移除存储卡权限并添加相关功能
- 支持无相机使用，从文件扫描和保存图片到文件
- 完全保留通过 intent 编码及请求解码功能
- 移除帮助

### 权限说明

- 相机。非必需，用于从摄像头采集条形码
- 振动。成功扫描后可选的振动提醒
- 控制和查看 WLAN 网络。直接连接扫描到的 WLAN 二维码

### 用法

```diff
diff --git a/mobile/build.gradle b/mobile/build.gradle
index 77f30dd2..4898c4af 100644
--- a/mobile/build.gradle
+++ b/mobile/build.gradle
@@ -90,7 +90,7 @@ dependencies {
     implementation 'androidx.browser:browser:1.4.0'
     implementation 'androidx.constraintlayout:constraintlayout:2.1.3'
     implementation 'androidx.lifecycle:lifecycle-runtime-ktx:2.4.1'
+    implementation 'com.github.ssrlive:zxing-android:v4.9.3'
     implementation 'com.takisoft.preferencex:preferencex-simplemenu:1.1.0'
     implementation 'com.twofortyfouram:android-plugin-api-for-locale:1.0.4'
     implementation 'me.zhanghai.android.fastscroll:library:1.1.8'
diff --git a/settings.gradle b/settings.gradle
index ad74650a..0e664b40 100644
--- a/settings.gradle
+++ b/settings.gradle
@@ -20,6 +20,7 @@ dependencyResolutionManagement {
         google()
         mavenCentral()
         jcenter()
+        maven { url 'https://jitpack.io' }
     }
 }
 include ':mobile', ':core', ':plugin'
```

```kotlin
    private const val REQUEST_SCAN_QRCODE = 4

    try {
        val intent = Intent("com.google.zxing.client.android.SCAN")
        intent.putExtra("SCAN_MODE", "QR_CODE_MODE")
        startActivityForResult(intent, REQUEST_SCAN_QRCODE)
    } catch (_: ActivityNotFoundException) {
        (activity as MainActivity).launchUrl(getString(R.string.faq_url))
    }

    REQUEST_SCAN_QRCODE -> {
        val contents = data?.getStringExtra("SCAN_RESULT")
        val uri = Uri.parse(contents)
        startActivity(Intent(context, UrlImportActivity::class.java).setData(uri))
    }
```

### 构建

* OpenJDK 1.8+
* Android SDK
  - Android SDK Build-Tools 30.0.0
  - Android SDK Platform 30
  - Android SDK Tools

```bash
# 在 Windows 下，要設置 JAVA_HOME 環境變量。
set JAVA_HOME="C:\Program Files\Android\Android Studio\jre"

git clone https://github.com/ssrlive/zxing-android.git
cd zxing-android
# 建议编辑 app/build.gradle ，修改 applicationId 以避免冲突

./gradlew aR

ls app/build/outputs/aar/BarcodeScanner-release.aar

```
