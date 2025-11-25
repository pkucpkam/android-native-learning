# Android Fundamentals 

## 1. Activity là gì? + Vòng đời Activity

### 🧠 Khái niệm
**Activity** là một thành phần quan trọng trong Android, đại diện cho một màn hình giao diện mà người dùng tương tác trực tiếp. Mỗi Activity thường gắn với một layout UI và chịu trách nhiệm xử lý logic cho màn hình đó (nhận input, gọi API, điều hướng sang màn khác…).

Mỗi Activity thường gắn với một layout (XML) và xử lý logic của màn hình:

- Nhận input người dùng  
- Điều hướng sang màn khác  
- Gọi API  
- Quản lý vòng đời UI  

---

## 🔄 Vòng đời Activity & Ý nghĩa

| Callback | Ý nghĩa | Khi dùng |
|---------|---------|----------|
| **onCreate()** | Activity được tạo lần đầu | setContentView, khởi tạo View, nhận Intent |
| **onStart()** | Activity sắp hiển thị | Chuẩn bị UI |
| **onResume()** | Activity ở foreground, user tương tác được | Resume animation, load nhẹ |
| **onPause()** | Activity bị che một phần | Pause animation, lưu tạm |
| **onStop()** | Activity không còn visible | Giải phóng tài nguyên nặng (GPS/camera) |
| **onDestroy()** | Activity bị hủy | Clean resources |
| **onRestart()** | Activity quay lại từ trạng thái stop | Khởi động lại logic nhẹ |

---

## 🧾 Code mẫu minh họa Lifecycle Activity

```java
public class MainActivity extends AppCompatActivity {

    private static final String TAG = "LifecycleDemo";
    private int counter = 0;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Log.d(TAG, "onCreate() called");

        if (savedInstanceState != null) {
            counter = savedInstanceState.getInt("counter", 0);
        }

        Button btnNext = findViewById(R.id.btnNext);
        btnNext.setOnClickListener(v -> {
            startActivity(new Intent(MainActivity.this, SecondActivity.class));
        });
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG, "onStart()");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG, "onResume()");
        counter++;
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG, "onPause()");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.d(TAG, "onStop()");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "onDestroy()");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.d(TAG, "onRestart()");
    }
}
```

---

# 2. Fragment – Khái niệm & sự khác nhau với Activity

## 🧠 Fragment là gì?
Fragment là một phần giao diện (UI component) có vòng đời riêng nhưng **không thể tồn tại độc lập**, phải được chứa trong Activity.

---

## 🔍 Khác nhau giữa Activity & Fragment

| Activity | Fragment |
|---------|----------|
| Độc lập | Phụ thuộc Activity |
| Khai báo trong manifest | Không khai báo manifest |
| Một màn hình đầy đủ | Một phần UI |
| Điều hướng trực tiếp | Điều hướng qua FragmentManager/NavHost |

---

## 📌 Khi nào dùng Fragment?

- Khi dùng bottom navigation, tab, viewpager – mỗi tab là một Fragment.
- Khi một Activity cần chia thành nhiều phần UI có thể tái sử dụng.
- Giao diện tablet 2-pane  
- Single Activity Architecture (phổ biến hiện nay)

---

## 📘 Ví dụ:
- MainActivity chứa bottom navigation → mỗi tab là một Fragment  
- DetailFragment hiển thị chi tiết sản phẩm  

---

# 3. Intent – Explicit vs Implicit

## 🧠 Intent là gì?

Intent là một đối tượng dùng để mô tả một “ý định” thực hiện hành động: mở Activity, gửi Broadcast, start Service… Nó có thể mang kèm dữ liệu qua extras.

Là “ý định” để:

- Mở Activity  
- Start Service  
- Gửi Broadcast  
- Chọn ảnh, mở web, gọi điện…  

---

## 🎯 Explicit Intent
👉 Chỉ rõ Activity/Service cần mở

```kotlin
val intent = Intent(this, DetailActivity::class.java)
startActivity(intent)
```

---

## 🎯 Implicit Intent
👉 Không chỉ rõ component, chỉ mô tả hành động

```kotlin
val intent = Intent(Intent.ACTION_VIEW)
intent.data = Uri.parse("https://google.com")
startActivity(intent)
```


#   1. **Mở Activity khác**

### Java:

```java
Intent intent = new Intent(MainActivity.this, DetailActivity.class);
startActivity(intent);
```

---

#   2. **Gửi dữ liệu sang Activity khác**

### Java:

```java
Intent intent = new Intent(MainActivity.this, DetailActivity.class);
intent.putExtra("username", "Nguyen Van A");
intent.putExtra("age", 20);
startActivity(intent);
```

### Nhận dữ liệu ở `DetailActivity`:

```java
String name = getIntent().getStringExtra("username");
int age = getIntent().getIntExtra("age", 0);
```

---

#   3. **Mở ứng dụng gọi điện**

```java
Intent intent = new Intent(Intent.ACTION_DIAL);
intent.setData(Uri.parse("tel:0123456789"));
startActivity(intent);
```

---

#   4. **Mở trình duyệt web**

```java
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.setData(Uri.parse("https://google.com"));
startActivity(intent);
```

---

#   5. **Mở app nhắn tin SMS**

```java
Intent intent = new Intent(Intent.ACTION_SENDTO);
intent.setData(Uri.parse("smsto:0123456789"));
intent.putExtra("sms_body", "Hello bạn ơi!");
startActivity(intent);
```

---

#   6. **Gửi email (Intent chooser)**

```java
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("message/rfc822");
intent.putExtra(Intent.EXTRA_EMAIL, new String[]{"abc@gmail.com"});
intent.putExtra(Intent.EXTRA_SUBJECT, "Tiêu đề email");
intent.putExtra(Intent.EXTRA_TEXT, "Nội dung email");
startActivity(Intent.createChooser(intent, "Chọn ứng dụng email"));
```

---

#   7. **Chọn ảnh từ Gallery**

```java
Intent intent = new Intent(Intent.ACTION_PICK);
intent.setType("image/*");
startActivityForResult(intent, 100);
```

---

#   8. **Chụp ảnh bằng Camera**

```java
Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
startActivityForResult(intent, 101);
```

---

#   9. **Gửi dữ liệu theo Object (Serializable)**

### Model:

```java
public class User implements Serializable {
    String name;
    int age;
}
```

### Gửi object:

```java
User user = new User();
user.name = "Linh";
user.age = 22;

Intent intent = new Intent(MainActivity.this, DetailActivity.class);
intent.putExtra("user", user);
startActivity(intent);
```

### Nhận object:

```java
User user = (User) getIntent().getSerializableExtra("user");
```

---

#   10. **Lấy kết quả trả về từ Activity (startActivityForResult)**

### Gửi yêu cầu:

```java
Intent intent = new Intent(MainActivity.this, InputActivity.class);
startActivityForResult(intent, 200);
```

### Nhận kết quả:

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == 200 && resultCode == RESULT_OK) {
        String result = data.getStringExtra("result");
        Log.d("RESULT", result);
    }
}
```

### Trả dữ liệu về:

```java
Intent result = new Intent();
result.putExtra("result", "Dữ liệu trả về");
setResult(RESULT_OK, result);
finish();
```

---

# 4. Service – Foreground service – Background limitations

## 🧠 Service là gì?

Service là một component chạy ngầm, không có UI, dùng để xử lý các tác vụ lâu hoặc cần tiếp tục chạy khi user không mở app, ví dụ: phát nhạc, download file, sync dữ liệu.

Component chạy **ngầm**, không có UI.  

Dùng cho: chơi nhạc, xử lý nền, upload/download.

---

## 🔥 Foreground Service
- Chạy ưu tiên cao  
- Bắt buộc có notification  
- Dùng cho: Music app, GPS tracking

---

## 🚫 Hạn chế từ Android O+
- Không được chạy background service tự do  
- Phải dùng:  
  - Foreground service  
  - WorkManager  
  - JobScheduler  

---

# 5. BroadcastReceiver & ContentProvider

## 📡 BroadcastReceiver

BroadcastReceiver là component dùng để “lắng nghe” các sự kiện (broadcast) từ hệ thống hoặc app khác, ví dụ: máy khởi động xong (BOOT_COMPLETED), thay đổi kết nối mạng, nhận SMS,… Khi nhận được broadcast, nó chạy một đoạn code ngắn để xử lý.


Lắng nghe sự kiện hệ thống:

- Network on/off  
- Boot completed  
- Nhận SMS  
- Battery low  

Đoạn code:

```kotlin
class NetworkReceiver: BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        Log.d("Receiver", "Network changed")
    }
}
```

---

## 🗄 ContentProvider

ContentProvider là component dùng để chia sẻ dữ liệu có cấu trúc giữa các phần trong app hoặc giữa app khác nhau, thông qua URI. Dữ liệu thường là database, file…


Dùng chia sẻ dữ liệu giữa các app qua URI:

Ví dụ:
- Danh bạ (ContactsProvider)  
- Ảnh (MediaStore)  

---

# 6. AndroidManifest.xml & Permission

## 📘 Vai trò của Manifest
- Khai báo Activity/Service/Receiver  
- Khai báo Permission  
- Intent-filter  
- Tên package  
- Cấu hình app  

---

## 🔐 Permission quan trọng

| Permission | Chức năng |
|-----------|-----------|
| INTERNET | Gọi API |
| CAMERA | Dùng camera |
| ACCESS_FINE_LOCATION | GPS chính xác |
| READ_EXTERNAL_STORAGE | Đọc file |
| RECORD_AUDIO | Ghi âm |

👉 Từ Android 6+ cần **runtime permission**.

---

# 🎯 Bộ câu hỏi ôn tập thêm


1. Khi nào onPause() được gọi?  
2. Sự khác nhau giữa Fragment lifecycle & Activity lifecycle?  
3. Diff giữa Explicit & Implicit Intent?  
4. Khi nào dùng Service thay vì Thread?  
5. ContentProvider dùng để làm gì?  
6. Vì sao background service bị hạn chế từ Android O?  
7. Vì sao cần runtime permission?  
8. Điểm khác biệt giữa static broadcast & dynamic broadcast?  

---
