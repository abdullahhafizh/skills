# Checklist & Edge Cases (Standar MR Pre-check)

Dokumen ini memuat daftar periksa mandiri dan penanganan kondisi batas (edge cases) sebelum mengajukan merge request atau meminta bot review.

---

## 1. Daftar Periksa Kesiapan MR (Pre-Review Checklist)

### A. Tiket & Acceptance Criteria (AC) Jira
- [ ] Kunci tiket Jira (misal `PROJ-123`) tercantum jelas di branch name (`feature/PROJ-123-export-user`), judul MR, atau deskripsi MR.
- [ ] Jika mengerjakan beberapa tiket sekaligus, format penamaan di deskripsi: `PROJ-123 (terkait: PROJ-124, PROJ-125)`.
- [ ] Seluruh butir AC dari tiket primer dan terkait terpenuhi dengan bukti `file:line` konkret (status `met`).
- [ ] Tidak ada AC yang tertinggal atau baru terpenuhi sebagian (`partial`/`missing`) yang dapat memicu finding `Blocking`.

### B. Backend Quality Gates (Spring Boot / Java)
- [ ] **No Raw CRUD Queries**: Tidak menggunakan `@Query(nativeQuery = true)` atau raw SQL/JPQL untuk CRUD kecuali ada preseden identik di modul yang sama dan Criteria/Specification mustahil.
- [ ] **No N+1 Queries**: Association list/detail menggunakan `@EntityGraph`, `JOIN FETCH`, `@BatchSize`, atau DTO projection.
- [ ] **Paginasi Wajib**: Semua endpoint pengembalian koleksi data/list menerima `Pageable` dan mengembalikan `Page<T>` / `Slice<T>` terpaginasi.
- [ ] **Layer & DTO Isolation**: Tidak menginjeksi `*Repository` langsung di dalam `@Controller` / `@RestController` (wajib lewat `*Service`). Tidak membocorkan JPA Entity langsung ke HTTP response (wajib petakan ke DTO/`ApiResponse`).
- [ ] **Permission-Only AuthZ**: Otorisasi menggunakan `@PreAuthorize("hasPermission(...)")`. Tidak ada pengecekan nama role langsung (`hasRole` atau switch string role).
- [ ] **Zero Hardcode**: Tidak ada string URL, status code, pesan, atau permission yang di-hardcode (wajib pakai konstanta/enum/i18n/config).
- [ ] **Zero Secrets & PII**: Tidak ada token, password, atau data PII mentah yang tercatat di log atau bundle client.

### C. Frontend Quality Gates (Angular 20 / NgRx)
- [ ] **NgRx Boundary**: Pemanggilan HTTP API hanya dilakukan di dalam NgRx effects atau infrastructure service. Tidak ada injeksi `HttpClient` atau direct HTTP service di dalam component atau page.
- [ ] **State Mutation**: Reducer dan adapter mematuhi immutable updates; tidak ada in-place mutation pada state object atau array.
- [ ] **Performance & Change Detection**: Komponen list menggunakan `ChangeDetectionStrategy.OnPush`. Template HTML bebas dari pemanggilan method berat.
- [ ] **Zero Memory Leaks**: Subscription dibersihkan menggunakan `takeUntilDestroyed()`, async pipe, atau teardown lifecycle.
- [ ] **Client Security**: Tidak ada penggunaan `innerHTML` atau bypass sanitizer tanpa sanitizer resmi Angular.
- [ ] **Zero Hardcode**: UI copy dan status label menggunakan dictionary i18n atau konstanta.

### D. Unit Testing & Regresi
- [ ] Suite unit test lokal lulus 100% tanpa kegagalan (`0 failures`).
- [ ] Jumlah total unit test tidak berkurang dibandingkan commit target branch (`origin/main` / `origin/target`).
- [ ] Jika menambahkan fitur baru, sertakan unit test baru yang membuktikan fungsionalitas dan mencegah penurunan test count.

---

## 2. Penanganan Kasus Batas (Edge Cases)

### A. Diff Besar atau File Rename Tanpa Patch Hunk
- **Kondisi**: GitLab memotong patch pada file hasil rename atau file dengan diff melebihi ambang batas ukuran (`patch` omitted/truncated).
- **Aksi Mandiri**: Periksa file utuh pada commit lokal `HEAD` (`git show HEAD:<path>`). Pastikan struktur file yang direname tetap mematuhi seluruh quality gate backend/frontend agar bot reviewer tidak salah mengidentifikasi sebagai implementasi yang hilang.

### B. File Non-Stack (CI/CD, Docker, Dokumentasi)
- **Kondisi**: MR memodifikasi file infra/CI/CD seperti `.gitlab-ci.yml`, `docker-compose*.yml`, Kubernetes manifests, atau `.md`.
- **Aksi Mandiri**: File-file ini dikecualikan dari aturan stack backend (`backend-rules.md`). Namun, pastikan tetap lolos audit teknis pilar umum: zero secrets, tidak ada kredensial hardcoded, dan tidak merusak kompatibilitas pipeline.

### C. MR Rereview Pasca-Rebase / Force-Push
- **Kondisi**: Branch telah di-rebase ke target branch sehingga commit SHA review terdahulu tidak lagi ada di history linier.
- **Aksi Mandiri**: Jangan panik jika bot jatuh ke fallback diff `origin/target-branch...HEAD`. Pastikan setiap komentar temuan terdahulu telah dibalas di thread GitLab dan perbaikannya telah ter-commit pada `HEAD` sehingga thread dapat ditutup sebagai `"Sudah sesuai"`.
