# GitLab MR Comment & Summary Templates

Use these exact structures when emitting line findings, rereview thread replies, and the MR summary. Format the prose inside each card in clear Bahasa Indonesia while keeping technical terms, identifiers, and severity labels exact.

## 1. Line Finding Card (Review & New Rereview Findings)

Attach only to changed lines in the diff:

```markdown
**[Blocking | High | Medium | Low / Note] - [Security | Performance | AC Jira | Architecture | Regression | Testing | Zero Hardcode]**
- **Path**: `path/to/file.ext:line`
- **Rule**: `<backend/rule-file.mdc | frontend/rule-file.mdc>` (or `Quality Gate` / `AC Jira <TICKET-KEY>`)
- **Masalah**: <Satu kalimat spesifik menjelaskan pelanggaran pada baris yang berubah dan dampaknya.>
- **Saran perbaikan**:
  <Langkah konkret atau cuplikan kode pengganti yang langsung bisa diterapkan author.>
```

## 2. Rereview Resolved Thread Reply Card (`"Sudah sesuai"`)

When a previous finding thread is fixed in commits since the last reviewed SHA (or verified via the `HEAD` snippet after an author reply), reply in the same thread and resolve it:

```markdown
**Sudah sesuai** - `<judul singkat temuan awal>`
- **Path**: `path/to/file.ext:line`
- **Alasan**: <Satu kalimat bukti bahwa perbaikan pada commit/HEAD sudah memenuhi rule atau AC yang sebelumnya dilanggar.>
*(Action: Resolve thread)*
```

## 3. MR Review Summary Template

Always emit this summary after evaluating all changed files and Jira Acceptance Criteria:

```markdown
## Summary Review MR

- **Keputusan**: **[Approve | Request changes]**
- **Mode**: `[Review | Rereview | Review (Ulangi SHA + kirim komentar)]`
- **Tiket Jira**: `<KEY atau "<KEY-utama> (terkait: <KEY>, <KEY>)" atau "Tidak ditemukan di branch/judul/deskripsi">`
- **SHA direview**: `<HEAD SHA yang dievaluasi pada pass ini>`

### Evaluasi Acceptance Criteria (AC) Jira

| Butir AC | Status (`met` / `partial` / `missing` / `unknown`) | Evidence (`file:line` / Catatan) |
| :--- | :--- | :--- |
| <Butir AC 1> | `met` | `path/to/file.ext:line` |
| Tidak ada AC di konteks | `unknown` | Catatan: tidak memblokir keputusan |

### Ringkasan Temuan

| Severity | Kategori | Path (`file:line`) | Rule | Status Thread |
| :--- | :--- | :--- | :--- | :--- |
| `Blocking` | `Security` | `path/to/file.ext:line` | `backend/00-quality-gates.mdc` | `Open` |

### Status Unit Test
- **Hasil eksekusi**: `<Lulus / Gagal / Tidak dijalankan (opsi unit test tidak aktif)>`
- **Jumlah test vs branch target**: `<Tetap / Bertambah (+N) / Berkurang (-N) / Tidak tersedia>`
- **Catatan verifikasi**: `<"Unit test belum diverifikasi" jika Tidak dijalankan, selain itu "-">`
```
