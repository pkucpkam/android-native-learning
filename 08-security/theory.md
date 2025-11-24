
# **I. PROGUARD / R8**

---

##  **1. Proguard là gì?**

Proguard là công cụ giúp:

* **obfuscation:** làm rối code (đổi tên biến, class, method → khó bị phân tích)
* **shrinking:** loại bỏ code không dùng
* **optimization:** tối ưu bytecode

→ Giảm kích thước app (.apk/.aab)
→ Tăng độ bảo mật

Từ Android Gradle Plugin 3.4+, **R8** được sử dụng thay thế Proguard.

---

##  **2. R8 là gì?**

R8 kết hợp cả 3 tính năng:

* shrinker
* optimizer
* obfuscator

Ưu điểm:

* Nhanh hơn Proguard
* Tạo file nhỏ hơn
* Tích hợp sẵn vào Android build system

---

##  **3. Cơ chế hoạt động Proguard/R8**

1. Nhận file .class
2. Loại bỏ code không dùng (shrinking)
3. Tối ưu bytecode
4. Obfuscate tên biến, method, class
5. Tạo ra file mapping.txt để map tên cũ – tên sau khi làm rối
6. Sinh ra APK/AAB cuối

---

##  **4. Cách bật Proguard/R8 trong Android**

Trong `app/build.gradle`:

```gradle
buildTypes {
    release {
        minifyEnabled true
        shrinkResources true
        proguardFiles getDefaultProguardFile(
            'proguard-android-optimize.txt'),
            'proguard-rules.pro'
    }
}
```

---

##  **5. File proguard-rules.pro**

Một số rule phổ biến:

### ➤ Giữ class model (GSON/Retrofit)

```pro
-keep class com.example.model.** { *; }
```

### ➤ Giữ annotation

```pro
-keepattributes *Annotation*
```

### ➤ Giữ class dùng reflection

```pro
-keep class kotlinx.** { *; }
```

### ➤ Giữ Room Database

```pro
-keep class androidx.room.** { *; }
-dontwarn androidx.room.**
```

---

##  **6. Khi nào cần viết Proguard rule?**

* Dùng thư viện reflection
* Dùng JSON mapping (Gson/Moshi)
* Dùng Room
* Framework DI (Dagger/Hilt/Koin)
* Serialization
* Code bị crash khi minifyEnabled = true

---

##  **7. Đọc file mapping.txt**

Sau khi obfuscate, R8 đổi tên class/method.
File `mapping.txt` giúp debug crash trong bản release.

Ví dụ crash log:

```
java.lang.NullPointerException: at a.b.c(Unknown Source)
```

mapping.txt:

```
com.example.ui.MainActivity -> a:
    void onCreate() -> b
    void loadData() -> c
```

→ ta biết hàm crash là `MainActivity.loadData()`.

---

# ─────────────────────────

# **II. NETWORK SECURITY CONFIG (HTTPS)**

---

Android từ API 28 trở lên chặn traffic HTTP không an toàn.

##  **1. Tại sao dùng HTTPS thay HTTP?**

HTTPS cung cấp:

* **Encryption:** mã hóa dữ liệu
* **Data integrity:** không bị sửa đổi
* **Authentication:** xác thực server
* Chống MITM (man-in-the-middle)

HTTP **không bảo mật** → dễ bị sniff traffic.

---

##  **2. Network Security Config là gì?**

Là XML cho phép cấu hình security rules cho network traffic.

Dùng để:

* Bật/tắt cleartext (HTTP)
* Thêm certificate custom (SSL pinning)
* Cho phép domain đặc biệt

---

##  **3. Cách tạo network_security_config.xml**

📁 res/xml/network_security_config.xml:

```xml
<network-security-config>
    <domain-config cleartextTrafficPermitted="false">
        <domain includeSubdomains="true">example.com</domain>
    </domain-config>
</network-security-config>
```

---

##  **4. Khai báo trong AndroidManifest**

```xml
<application
    android:networkSecurityConfig="@xml/network_security_config">
</application>
```

---

##  **5. Cho phép HTTP (tạm thời)**

```xml
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain>192.168.1.1</domain>
    </domain-config>
</network-security-config>
```

---

##  **6. SSL Pinning**

Giúp ứng dụng kết nối đúng server bằng certificate định trước → chống giả mạo SSL.

```xml
<domain-config>
    <domain>example.com</domain>
    <pin-set expiration="2026-01-01">
        <pin digest="SHA-256">YOUR_CERTIFICATE_HASH==</pin>
    </pin-set>
</domain-config>
```

---

##  **7. Kiểm tra certificate hash**

Dùng:

* openssl
* hoặc Charles Proxy

---

# ─────────────────────────

# **III. STORING TOKENS SECURELY**

---

Không bao giờ lưu token (JWT, RefreshToken, AccessToken) bằng:

* SharedPreferences bình thường (plaintext)
* SQLite không mã hóa
* File ở external storage

Nếu điện thoại bị root → hacker đọc token dễ dàng.

---

##  **1. Cách lưu token an toàn trong Android**

### 🔐 **A. EncryptedSharedPreferences (Khuyên dùng)**

Dùng Jetpack Security Crypto.

```gradle
implementation "androidx.security:security-crypto:1.1.0-alpha06"
```

### Code tạo EncryptedSharedPreferences:

```kotlin
val masterKey = MasterKey.Builder(context)
    .setKeyScheme(MasterKey.KeyScheme.AES256_GCM)
    .build()

val encryptedPrefs = EncryptedSharedPreferences.create(
    context,
    "secure_prefs",
    masterKey,
    EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
    EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
)

// Save
encryptedPrefs.edit().putString("token", "abc123").apply()

// Read
val token = encryptedPrefs.getString("token", null)
```

Mã hóa AES256 toàn bộ data → hacker có root cũng khó đọc.

---

### 🔐 **B. Jetpack DataStore + Encryption**

Trước khi lưu vào DataStore, mã hóa token bằng KeyStore.

Lập trình:

* Tạo key AES
* Lưu key trong Android Keystore
* Encrypt token → lưu vào DataStore
* Decrypt khi cần

→ An toàn hơn SharedPreferences nhiều.

---

### 🔐 **C. Keystore**

Android Keystore cung cấp môi trường bảo mật để lưu key:

* Key AES/ RSA
* Key chỉ sử dụng bên trong keystore
* Hacker không thể extract key ngay cả khi root (khó hơn rất nhiều)

Dùng để:

* Mã hóa token
* Mã hóa dữ liệu nhạy cảm (password, private key)

---

##  **2. Không lưu token vào đâu?**

❌ SharedPreferences thường
❌ Room không mã hóa
❌ File trong internal storage dạng text
❌ External storage (rất nguy hiểm)

---

##  **3. Lưu Refresh Token & Access Token như thế nào?**

### **Access Token**

* Lưu trong bộ nhớ tạm (RAM)
* Không nên lưu vào disk
* Expire nhanh → ít rủi ro

### **Refresh Token**

* Lưu EncryptedSharedPreferences
* Mã hóa bằng AES + KeyStore
* Hoặc xài DataStore + crypto

---

##  **4. WebView & token**

❗ KHÔNG BAO GIỜ truyền token vào WebView qua URL như:

```
https://example.com?token=abcd
```

→ dễ bị steal token.

---

##  **5. Xử lý token expiration**

* Dùng Interceptor (Retrofit)
* Khi nhận HTTP 401 → dùng Refresh Token để lấy token mới
* Nếu refresh fail → logout

---

##  **6. Access Token trong header Authorization**

```kotlin
val request = original.newBuilder()
    .header("Authorization", "Bearer $token")
    .build()
```

---

# ─────────────────────────

# **IV. TÓM TẮT**

---

## Proguard/R8

* Làm rối code
* Loại bỏ code thừa
* Tối ưu bytecode
* Cần thêm rule khi dùng reflection / Gson / Room / Hilt
* File mapping dùng để debug crash release

---

## Network Security

* Luôn dùng HTTPS
* Network Security Config để quản lý SSL/HTTP
* Có thể bật hoặc tắt HTTP
* SSL pinning chống MITM

---

## Token Security

* Không bao giờ lưu token dạng plaintext
* Dùng EncryptedSharedPreferences hoặc DataStore + Keystore
* Access Token lưu trong RAM
* Refresh token cần mã hóa
* Cẩn thận khi dùng WebView

---
