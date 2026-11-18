# Worked Example (Ilustrasi Sintetis, Bukan Review Nyata)

Dokumen ini menyediakan worked example perbaikan kode end-to-end sebelum diajukan ke bot `code-review`.

---

## 1. Kasus Kode Bermasalah (Sebelum Pre-check)

Pengembang hendak membuat fitur ekspor data pengguna pada Spring Boot backend dan Angular frontend.

### A. Potongan Diff Backend Awal (`UserController.java`)

```diff
@@ -44,2 +44,8 @@ public class UserController {
     private final UserService userService;
+    private final UserRepository userRepository;
+
+    @GetMapping("/users/export")
+    public List<User> exportAllUsers() {
+        return userRepository.findAll();
+    }
 }
```

### B. Hasil Audit Internal Pre-Check
Jika diff di atas diajukan ke `code-review`, akan timbul 4 temuan yang memicu `Request changes`:
1. `src/main/java/com/example/api/UserController.java:49` -> `High - Performance`: Pemanggilan `findAll()` tanpa `Pageable` (unbounded list).
2. `src/main/java/com/example/api/UserController.java:45` -> `Medium - Architecture`: Controller menginjeksi `UserRepository` langsung (pelanggaran layer).
3. `src/main/java/com/example/api/UserController.java:48` -> `Medium - Architecture`: Entity `User` bocor langsung ke response tanpa DTO.
4. `src/main/java/com/example/api/UserController.java:49` -> `Blocking - AC Jira`: AC wajib "Export data terpaginasi" hanya terpenuhi sebagian (`partial`).

---

## 2. Implementasi Remediasi Sebelum Push

Pengembang menerapkan perbaikan mandiri sesuai panduan `code-precheck`:

### A. Diff Backend Terkoreksi (`UserController.java`)

```diff
@@ -44,2 +44,8 @@ public class UserController {
     private final UserService userService;
+
+    @GetMapping("/users/export")
+    @PreAuthorize("hasPermission('USER', 'EXPORT')")
+    public ResponseEntity<ApiResponse<Page<UserResponseDto>>> exportUsers(Pageable pageable) {
+        return ResponseEntity.ok(ApiResponse.ok(userService.exportUsers(pageable)));
+    }
 }
```

### B. Verifikasi terhadap Quality Gates `code-review`

| Aturan yang Diverifikasi | Status Sebelum | Status Sesudah | Bukti Implementasi |
| :--- | :---: | :---: | :--- |
| **No Direct Repository in Controller** | `VIOLATION` | `PASS` | Dependensi didelegasikan utuh ke `userService.exportUsers(...)` |
| **No Leaked JPA Entity** | `VIOLATION` | `PASS` | Response dibungkus DTO `UserResponseDto` via `ApiResponse.ok(...)` |
| **Mandatory Pagination (`Pageable`)** | `VIOLATION` | `PASS` | Menerima parameter `Pageable` dan mengembalikan `Page<UserResponseDto>` |
| **Permission-Only AuthZ** | `MISSING` | `PASS` | Menggunakan `@PreAuthorize("hasPermission('USER', 'EXPORT')")` |
| **AC Jira Tiket PROJ-102** | `partial` | `met` | Kriteria ekspor pengguna terpaginasi terpenuhi penuh |
| **Unit Test Suite** | Belum ada | `PASS` | Test ditambahkan pada `UserControllerTest.java`, total test bertambah (+1) |

### C. Hasil Prediksi Bot Review
Setelah pre-check dan remediasi mandiri:
- `Blocking`: 0
- `High`: 0
- `Medium`: 0
- `Low / Note`: 0
- Keputusan Bot `code-review`: **Approve** (100% clean pass).
