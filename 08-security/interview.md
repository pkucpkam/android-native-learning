
# 🟢 **MỨC DỄ (FRESHER)**

---

## **1. Proguard và R8 là gì? Dùng để làm gì?**

**Trả lời:**
Proguard và R8 là công cụ dùng để:

* **Làm rối mã nguồn (obfuscation)** → khiến hacker khó reverse-engineer
* **Loại bỏ code không dùng (shrinking)**
* **Tối ưu bytecode (optimization)**
* **Giảm kích thước APK/AAB**

Hiện nay:

* **R8** là tool mặc định thay thế Proguard (nhanh hơn, hiệu quả hơn).

---

## **2. Vì sao cần làm rối code (obfuscate)?**

**Trả lời:**
Ứng dụng Android có thể bị giải nén và đọc code bằng tools như JADX, Apktool.
Làm rối code giúp:

* Ẩn logic quan trọng
* Bảo vệ thuật toán nội bộ
* Làm khó hacker khi tìm giá trị nhạy cảm
* Giảm nguy cơ reverse engineering

---

## **3. Proguard được bật ở đâu trong Android?**

**Trả lời:**
Trong file `app/build.gradle`:

```gradle
buildTypes {
    release {
        minifyEnabled true
        shrinkResources true
        proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'),
                      'proguard-rules.pro'
    }
}
```

---

## **4. File mapping.txt là gì?**

**Trả lời:**
mapping.txt lưu **bảng ánh xạ giữa tên class/method trước và sau khi obfuscate**.
File này giúp debug bản release khi stacktrace bị làm rối.

---

## **5. HTTPS là gì? Khác với HTTP như thế nào?**

**Trả lời:**
HTTPS = HTTP + TLS/SSL
Khác biệt:

* **HTTP**: plain text → ai cũng đọc được → **không an toàn**
* **HTTPS**: dữ liệu được **mã hóa**, **bảo toàn dữ liệu**, **xác thực server**

Chống:

* MITM (Man-in-the-middle)
* Sniffing
* Fake server

---

## **6. Network Security Config dùng để làm gì?**

**Trả lời:**
Network Security Config kiểm soát:

* Cho phép hoặc chặn HTTP (cleartext)
* Xác định domain an toàn
* SSL pinning
* Thêm chứng chỉ tự ký (self-signed certificate)

Định nghĩa trong file XML tại `res/xml/network_security_config.xml`.

---

## **7. Lưu token trong SharedPreferences có an toàn không?**

**Trả lời:**
**Không an toàn**.
SharedPreferences lưu token dạng plain text → hacker (nhất là trên thiết bị root) dễ lấy được.
→ Không nên lưu token, password ở dạng này.

---

# 🟡 **MỨC TRUNG BÌNH (JUNIOR)**

---

## **8. Cách lưu token an toàn nhất trong Android?**

**Trả lời:**
Cách an toàn nhất:

1. **EncryptedSharedPreferences** (Jetpack Security)
2. Mã hóa bằng AES với key lưu trong **Android Keystore**
3. Hoặc **Encrypted DataStore**
4. Access Token → **chỉ lưu trong RAM**, không lưu vào disk
5. Refresh Token → lưu trong encrypted storage

---

## **9. Android Keystore dùng để làm gì?**

**Trả lời:**
Android Keystore dùng để:

* Lưu trữ key (AES/RSA) an toàn trong phần cứng thiết bị
* Key không thể extract bằng root
* Dùng để encrypt/decrypt token hoặc dữ liệu nhạy cảm

Keystore là “két sắt” bảo mật của Android.

---

## **10. EncryptedSharedPreferences hoạt động như thế nào?**

**Trả lời:**
Nó sử dụng:

* AES-256-GCM để mã hóa giá trị
* AES-256-SIV để mã hóa key
* MasterKey được lưu trong Android Keystore

→ Dữ liệu trong SharedPreferences hoàn toàn mã hóa ở dạng byte-level.

---

## **11. Tại sao không nên lưu token vào SQLite?**

**Trả lời:**
SQLite trong Android không mã hóa theo mặc định.
Hacker có thể đọc database file (`.db`) → token bị lộ.

Nếu cần lưu, phải mã hóa dữ liệu trước hoặc dùng SQLCipher.

---

## **12. Bạn hiểu gì về SSL Pinning?**

**Trả lời:**
SSL Pinning đảm bảo ứng dụng **chỉ kết nối với server có certificate đúng** (đã pin).
Dù hacker tạo certificate giả → app sẽ từ chối kết nối.
Giúp chống MITM mạnh mẽ nhất.

---

## **13. JSON Web Token (JWT) gồm những phần nào?**

**Trả lời:**
JWT được chia làm 3 phần:

1. **Header** → kiểu token, thuật toán mã hóa
2. **Payload** → chứa data (userId, exp, roles…)
3. **Signature** → xác thực tính toàn vẹn

JWT không mã hóa payload mà chỉ encode Base64 → dễ đọc nếu không bảo vệ.

---

## **14. Làm sao để chống MITM attack?**

**Trả lời:**

* Dùng HTTPS
* Bật `cleartextTrafficPermitted=false`
* SSL Pinning
* Không để lộ CA certificate
* Không truyền token qua HTTP
* Dùng HSTS (server)

---

## **15. Vì sao token không nên lưu vào Intent hoặc Bundle?**

**Trả lời:**
Intent và Bundle có thể bị đọc qua:

* Logcat
* Dump system
* IPC leak
  → Không an toàn cho dữ liệu nhạy cảm.

---

# 🔴 **MỨC KHÓ (MIDDLE – NÂNG CAO)**

*(Các câu này thường dùng để "lọc" Junior lên Middle)*

---

## **16. Sự khác nhau giữa Proguard và R8 ở mức kỹ thuật?**

**Trả lời:**

* R8 thay thế Proguard trong build pipeline
* R8 hoạt động **trực tiếp trên bytecode**, kết hợp shrink + optimize + obfuscate
* R8 chạy nhanh hơn Proguard và tạo ra file nhỏ hơn
* Không cần toolchain riêng như Proguard

R8 cũng hỗ trợ rule Proguard, nên backward compatible.

---

## **17. Tại sao HTTP cleartext bị block từ Android 9 (API 28)?**

**Trả lời:**
Google tăng cường bảo mật, vì HTTP:

* Dễ bị sniff
* Không mã hóa
* Dễ bị MITM
* Dễ bị chỉnh sửa dữ liệu

Vì vậy `cleartextTrafficPermitted` mặc định = **false** từ API 28.

---

## **18. Access Token và Refresh Token nên xử lý khác nhau thế nào?**

**Trả lời:**
**Access Token**

* Expire ngắn
* Dùng để gọi API
* Không nên lưu vào disk → lưu RAM hoặc EncryptedSharedPreferences

**Refresh Token**

* Expire dài hơn
* Dùng để tạo Access Token mới
* PHẢI lưu bằng EncryptedSharedPreferences hoặc DataStore + Keystore

Luôn dùng Interceptor để tự refresh token khi nhận HTTP 401.

---

## **19. Nếu hacker có quyền root, họ có thể lấy token không?**

**Trả lời:**
Có thể, nhưng rất khó nếu app dùng:

* EncryptedSharedPreferences
* Key lưu trong Android Keystore
* Không lưu Access Token trong disk

Keystore bảo vệ key trong phần cứng → hacker không thể lấy key để decrypt token.

---

## **20. Những sai lầm bảo mật phổ biến của developer Android?**

**Trả lời:**

* Lưu token trong SharedPreferences thường
* Truyền token qua URL hoặc Intent
* Dùng HTTP thay vì HTTPS
* Không bật Proguard/R8
* Không khai báo Network Security Config
* Cho phép debug build ở release (android:debuggable="true")
* Hardcode API key trong code
* Không SSL Pinning
* Lưu password hoặc JWT vào SQLite không mã hóa

---

## **21. Hardcode API Key trong code có an toàn không? Vì sao?**

**Trả lời:**
**Không an toàn.**
Hacker giải nén APK → dùng JADX → đọc code, strings, constants.
API keys/mật khẩu bị lộ ngay lập tức.

Cách bảo vệ:

* Move key lên server
* Dùng NDK (JNI) để che dấu logic (tốt hơn nhưng vẫn crack được)
* R8 để obfuscate

---

## **22. Làm sao để bảo vệ WebView khỏi bị ăn cắp token?**

**Trả lời:**
WebView đặc biệt nguy hiểm nếu xử lý sai.

Cách bảo vệ:

* Không truyền token trong URL
* Không enable JavaScriptInterface mà không security
* Dùng HTTPS
* Chặn file:// access
* Không lưu cookie lâu dài
* Dùng WebViewClient để kiểm soát request

---

## **23. Tại sao không nên lưu password của user trong thiết bị?**

**Trả lời:**
Vì:

* Bị leak → mất tài khoản user
* Hacker root máy → đọc file
* Lưu password vi phạm chuẩn bảo mật OWASP

Giải pháp:

* Chỉ lưu access token
* Không bao giờ lưu plain password
* Lưu hash nếu cần, nhưng hashing server side.

---

## **24. SQL Injection có xảy ra trong Android không?**

**Trả lời:**
Có, nếu dùng SQLite mà ghép chuỗi query thủ công:

```sql
"SELECT * FROM user WHERE name='" + name + "'"
```

Cách tránh:

* Dùng Room
* Dùng query param (`?`)
* Không dùng string concatenation

---

## **25. Làm sao bảo vệ app khỏi reverse engineering?**

**Trả lời:**

* Enable R8/Proguard
* Obfuscate code mạnh (mapping)
* Loại bỏ debug info
* Dùng NDK cho logic nhạy cảm
* Verify integrity (SafetyNet hoặc Play Integrity API)
* Không hardcode token/key

---
