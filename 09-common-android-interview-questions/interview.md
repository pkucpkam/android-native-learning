
# 🇦🇩 **1. Android Basics**

### **1. Android là gì? Kiến trúc Android gồm những tầng nào?**

**Đáp án:**
Android là hệ điều hành mã nguồn mở dựa trên Linux dành cho thiết bị di động.
Kiến trúc gồm 4 tầng chính:

1. **Linux Kernel** – quản lý bộ nhớ, driver, bảo mật.
2. **Hardware Abstraction Layer (HAL)** – lớp giao tiếp với phần cứng.
3. **Android Runtime (ART) & Core Libraries** – chạy ứng dụng (ART thay thế Dalvik).
4. **Application Framework** – các service như Activity Manager, Window Manager.
5. **Applications** – layer chứa app (Facebook, Zalo, App người dùng viết).

---

### **2. Activity là gì? Chu kỳ vòng đời (lifecycle) của Activity?**

**Đáp án:**
Activity là một UI screen trong ứng dụng Android.
Lifecycle chính:

* `onCreate()`
* `onStart()`
* `onResume()`
* `onPause()`
* `onStop()`
* `onDestroy()`
* `onRestart()`

Dùng `ViewModel + onSaveInstanceState()` để xử lý xoay màn hình/dữ liệu tạm.

---

### **3. Fragment là gì? Khác Activity như thế nào?**

**Đáp án:**
Fragment là phần UI có thể tái sử dụng trong Activity.
**Khác biệt:**

* Fragment không độc lập → phải nằm trong Activity.
* Lifecycle của Fragment phức tạp hơn (thêm onAttach, onCreateView…).
* Hữu ích khi làm UI dạng tab, đa màn hình, responsive.

---

# 🇦🇩 **2. Java/Kotlin (Core)**

### **4. Sự khác nhau giữa Interface và Abstract Class?**

**Đáp án:**

| Interface                           | Abstract Class                             |
| ----------------------------------- | ------------------------------------------ |
| Không chứa state                    | Có thể chứa state, biến                    |
| Một class implement nhiều interface | Chỉ kế thừa 1 abstract class               |
| Hàm mặc định: default method        | Có thể có abstract và non-abstract methods |

---

### **5. Null safety trong Kotlin là gì?**

**Đáp án:**

* Kotlin hạn chế lỗi NullPointerException.
* Sử dụng `?`, `!!`, `?.`, `?:` để xử lý null.
  Ví dụ:

```kotlin
val name: String? = null
val result = name ?: "No name"
```

---

### **6. Data class là gì?**

**Đáp án:**
Class dùng để chứa dữ liệu, tự sinh `equals()`, `hashCode()`, `toString()`, `copy()`.

---

# 🇦🇩 **3. Android UI & Layout**

### **7. Các Layout thường dùng?**

**Đáp án:**

* LinearLayout
* RelativeLayout
* ConstraintLayout (tối ưu nhất, khuyến nghị hiện nay)
* FrameLayout
* RecyclerView với LayoutManager (Vertical, Horizontal, Grid)

---

### **8. RecyclerView hoạt động thế nào?**

**Đáp án:**
RecyclerView tái sử dụng View để tối ưu hiệu năng list lớn.
Thành phần:

* Adapter
* ViewHolder
* LayoutManager
* DiffUtil (tối ưu update list)

---

# 🇦🇩 **4. Android Components & Architecture**

### **9. ViewModel là gì? Tại sao dùng?**

**Đáp án:**
ViewModel lưu dữ liệu UI, **không bị destroy khi xoay màn hình**, giúp UI không phải load lại.
Sử dụng LiveData/StateFlow để quan sát dữ liệu.

---

### **10. LiveData khác StateFlow thế nào?**

**Đáp án:**

| LiveData                     | StateFlow                         |
| ---------------------------- | --------------------------------- |
| Lifecycle-aware              | Không lifecycle-aware             |
| Chỉ emit khi có observer     | Luôn emit giá trị hiện tại        |
| Dùng trong MVVM truyền thống | Dùng trong Kotlin Flow/Coroutines |

---

### **11. MVVM là gì?**

**Đáp án:**
Pattern tách logic khỏi UI.

* **Model**: dữ liệu
* **View**: UI (Activity/Fragment)
* **ViewModel**: xử lý logic, expose LiveData/Flow cho View.

---

# 🇦🇩 **5. Networking và Storage**

### **12. SharedPreferences dùng làm gì?**

**Đáp án:**
Lưu trữ key-value đơn giản: token, boolean, setting.

---

### **13. Room là gì? Vì sao nên dùng?**

**Đáp án:**
Room là ORM giúp thao tác SQLite dễ hơn.
Ưu điểm:

* Compile-time check SQL
* Coroutine support
* LiveData/Flow support

---

### **14. Retrofit hoạt động ra sao?**

**Đáp án:**
Retrofit là HTTP client:

* Dùng annotations (`@GET`, `@POST`)
* Hỗ trợ Gson/Moshi
* Kết hợp Coroutines/Flow/RxJava

Ví dụ:

```kotlin
@GET("users")
suspend fun getUsers(): List<User>
```

---

# 🇦🇩 **6. Coroutines**

### **15. Coroutine là gì?**

**Đáp án:**
Coroutine là lightweight thread giúp xử lý bất đồng bộ (async) hiệu quả.
Scope thường dùng:

* `viewModelScope`
* `lifecycleScope`

---

### **16. suspend function là gì?**

**Đáp án:**
Hàm có thể tạm dừng và tiếp tục mà không block thread.
Ví dụ:

```kotlin
suspend fun fetchData()
```

---

# 🇦🇩 **7. Android Advanced (Cơ bản cho Fresher)**

### **17. Dependency Injection là gì? Bạn biết Hilt/Dagger?**

**Đáp án:**
DI là kỹ thuật tách việc khởi tạo và sử dụng object.
Hilt/Dagger giúp:

* Giảm boilerplate
* Quản lý lifecycle của dependency
* Dễ test

---

### **18. Các loại Service trong Android**

**Đáp án:**

* Started Service
* Bound Service
* Foreground Service (hiện notification)

---

### **19. Proguard là gì?**

**Đáp án:**
Dùng để:

* Nén code
* Tối ưu
* Obfuscate – làm rối mã tránh reverse engineering

---

### **20. Cách tối ưu hiệu năng Android**

**Đáp án:**

* Dùng DiffUtil cho RecyclerView
* Tránh tạo object không cần thiết
* Sử dụng ConstraintLayout
* Load hình bằng Glide/Coil
* Chạy tác vụ nặng bằng Coroutines/Background thread

---

# 🇦🇩 BONUS: **Câu hỏi hành vi (Fresher)**

### **21. Tại sao bạn muốn làm Android Developer?**

→ Trả lời theo định hướng: yêu thích mobile, tự làm app cá nhân, thích UI/UX, muốn phát triển dài hạn.

### **22. Kinh nghiệm thực tế bạn đã có?**

→ Nêu project cá nhân, thực tập, app demo (CRUD, login, Retrofit, Room…).

### **23. Khi gặp bug khó bạn xử lý thế nào?**

→ Debug step-by-step, logcat, breakpoints, test từng function, tìm doc.

---

