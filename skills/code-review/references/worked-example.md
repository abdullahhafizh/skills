# Worked Example (Ilustrasi Sintetis, Bukan MR Nyata)

Dokumen ini menyediakan worked example end-to-end (potongan diff -> kartu Finding -> baris Summary -> keputusan) serta penanganan kondisi tepi (edge cases).

---

## 1. Ilustrasi Sintetis End-to-End

### A. Potongan Diff (`diff --git a/src/main/java/com/example/api/UserController.java b/src/main/java/com/example/api/UserController.java`)

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

### Pemetaan Baris Target Diff

| Baris Target | Isi Baris Kode | Kategori Pelanggaran / Observasi |
| :---: | :--- | :--- |
| `44` | `    private final UserService userService;` | Konteks eksisting (layer service) |
| `45` | `+    private final UserRepository userRepository;` | Injeksi repository langsung di controller (`Medium - Architecture`) |
| `46` | `+` | Baris kosong baru |
| `47` | `+    @GetMapping("/users/export")` | Endpoint HTTP baru |
| `48` | `+    public List<User> exportAllUsers() {` | JPA Entity `User` bocor di controller response (`Medium - Architecture`) |
| `49` | `+        return userRepository.findAll();` | Unpaginated list call tanpa `Pageable` (`High - Performance`) & AC tiket `PROJ-102` baru terpenuhi sebagian (`Blocking - AC Jira`) |
| `50` | `+    }` | Penutup blok method |
| `51` | `}` | Konteks eksisting |

### B. Kartu Finding yang Diterbitkan (Satu Severity per Kartu)

```markdown
**Blocking - AC Jira**
- **Path**: `src/main/java/com/example/api/UserController.java:49`
- **Rule**: `AC Jira PROJ-102`
- **Masalah**: Implementasi ekspor data pengguna baru memenuhi sebagian (partial) acceptance criteria wajib karena endpoint belum mendukung paginasi data.
- **Saran perbaikan**:
  Ubah query dan controller agar menerima `Pageable` dan mengembalikan slice data terpaginasi sesuai spesifikasi tiket `PROJ-102`.
```

```markdown
**High - Performance**
- **Path**: `src/main/java/com/example/api/UserController.java:49`
- **Rule**: `backend/springboot-criteria-specification.mdc`
- **Masalah**: Pemanggilan endpoint list mengembalikan seluruh baris data secara unpaginated (`findAll()`) tanpa parameter `Pageable`, berisiko memory spike dan OOM di production.
- **Saran perbaikan**:
  Gunakan parameter `Pageable` pada signature method dan delegasikan ke service/repository:
  ```java
  @GetMapping("/users/export")
  public ResponseEntity<ApiResponse<Page<UserResponseDto>>> exportAllUsers(Pageable pageable) {
      return ResponseEntity.ok(ApiResponse.ok(userService.getUsers(pageable)));
  }
  ```
```

```markdown
**Medium - Architecture**
- **Path**: `src/main/java/com/example/api/UserController.java:45`
- **Rule**: `backend/00-quality-gates.mdc`
- **Masalah**: `UserRepository` diinjeksi langsung ke dalam `UserController`, melanggar batasan layer arsitektur `controller -> service -> repository`.
- **Saran perbaikan**:
  Hapus dependensi langsung ke repository dari controller dan delegasikan seluruh pemanggilan data melalui `UserService`.
```

```markdown
**Medium - Architecture**
- **Path**: `src/main/java/com/example/api/UserController.java:48`
- **Rule**: `backend/springboot-api-response-i18n.mdc`
- **Masalah**: JPA Entity `User` bocor langsung pada return type controller HTTP tanpa isolasi transfer object (DTO).
- **Saran perbaikan**:
  Petakan entity `User` ke `UserResponseDto` via MapStruct mapper sebelum dikembalikan ke consumer API.
```

### C. Baris Ringkasan Temuan pada Summary

```markdown
| Severity | Kategori | Path (`file:line`) | Rule | Status Thread |
| :--- | :--- | :--- | :--- | :--- |
| `Blocking` | `AC Jira` | `src/main/java/com/example/api/UserController.java:49` | `AC Jira PROJ-102` | `Open` |
| `High` | `Performance` | `src/main/java/com/example/api/UserController.java:49` | `backend/springboot-criteria-specification.mdc` | `Open` |
| `Medium` | `Architecture` | `src/main/java/com/example/api/UserController.java:45` | `backend/00-quality-gates.mdc` | `Open` |
| `Medium` | `Architecture` | `src/main/java/com/example/api/UserController.java:48` | `backend/springboot-api-response-i18n.mdc` | `Open` |
```

### D. Keputusan Akhir MR

```markdown
## Summary Review MR

- **Keputusan**: **Request changes**
- **Mode**: `Review`
- **Tiket Jira**: `PROJ-102`
- **SHA direview**: `a1b2c3d4e5f67890123456789abcdef012345678`

### Evaluasi Acceptance Criteria (AC) Jira

| Butir AC | Status (`met` / `partial` / `missing` / `unknown`) | Evidence (`file:line` / Catatan) |
| :--- | :--- | :--- |
| Export data pengguna terpaginasi | `partial` | `src/main/java/com/example/api/UserController.java:49` (Paginasi belum diimplementasikan) |

### Ringkasan Temuan

| Severity | Kategori | Path (`file:line`) | Rule | Status Thread |
| :--- | :--- | :--- | :--- | :--- |
| `Blocking` | `AC Jira` | `src/main/java/com/example/api/UserController.java:49` | `AC Jira PROJ-102` | `Open` |
| `High` | `Performance` | `src/main/java/com/example/api/UserController.java:49` | `backend/springboot-criteria-specification.mdc` | `Open` |
| `Medium` | `Architecture` | `src/main/java/com/example/api/UserController.java:45` | `backend/00-quality-gates.mdc` | `Open` |
| `Medium` | `Architecture` | `src/main/java/com/example/api/UserController.java:48` | `backend/springboot-api-response-i18n.mdc` | `Open` |

### Status Unit Test
- **Hasil eksekusi**: `Tidak dijalankan (opsi unit test tidak aktif)`
- **Jumlah test vs branch target**: `Tidak tersedia`
- **Catatan verifikasi**: `Unit test belum diverifikasi`
```

---

## 2. Penanganan Kasus Tepi (Edge Cases & Fallbacks)

### A. Beberapa Kunci Tiket Jira Ditemukan
- **Kondisi**: Branch memuat tiket `PROJ-101`, judul MR memuat `PROJ-102`, dan deskripsi memuat `PROJ-103`.
- **Tindakan**: Prioritaskan tiket dari branch name sebagai tiket primer. Cantumkan semua tiket di field Summary:
  `- **Tiket Jira**: PROJ-101 (terkait: PROJ-102, PROJ-103)`.
- **Evaluasi AC**: Evaluasi gabungan seluruh butir AC dari tiket terkait tanpa menduplikasi kriteria yang sama.

### B. Merge Request Dua Stack (Fullstack Backend + Frontend)
- **Kondisi**: MR memuat perubahan Spring Boot (`*.java`) sekaligus Angular (`*.ts`).
- **Tindakan**: Muat kedua referensi: `references/backend-rules.md` DAN `references/frontend-rules.md`.
- **Pembeda Rule**: Di kartu temuan dan Summary, wajib sertakan namespace pembeda secara eksplisit, misalnya `backend/00-quality-gates.mdc` untuk Java dan `frontend/00-quality-gates.mdc` untuk TypeScript.

### C. File Rename atau Diff Besar Tanpa Patch
- **Kondisi**: GitLab diff menampilkan hunk kosong (`patch` omitted/truncated) pada file hasil rename atau diff melebihi ambang batas ukuran.
- **Tindakan**: Jangan anggap implementasi hilang. Ambil cuplikan konten file pada commit `HEAD` (`git show HEAD:<path>`), periksa pemenuhan AC dan quality gates pada isi file terkini, dan hanya flag issue jika baris hasil modifikasi melanggar rule.

### D. Nilai `N/A` pada Field `Last Reviewed SHA` Lama
- **Kondisi**: MR Summary terdahulu menggunakan format usang dengan tulisan `- **Last Reviewed SHA**: N/A (Review perdana)`.
- **Tindakan**: Anggap nilai tersebut tidak ada / tidak valid. Jalankan mode `Review` biasa pada seluruh diff MR, dan tuliskan SHA commit `HEAD` aktual ke field `- **SHA direview**:` pada Summary pass saat ini.
