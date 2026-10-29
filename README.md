# 📘 Panduan Kontribusi & Aturan Repositori Proyek UKM STDIIS

Dokumen ini berisi aturan **wajib dan mengikat** terkait penggunaan Git, GitHub, dan standar kerja tim selama proyek berlangsung (Okt 2026 - Mei 2027). Sistem di repositori ini telah dikonfigurasi untuk menolak (*reject*) secara otomatis setiap pelanggaran terhadap aturan di bawah ini.

---

## 1. 🏗️ Struktur Repositori & Hak Akses
Proyek ini memisahkan frontend dan backend untuk kemudahan *deployment* dan skalabilitas:
- **Backend (Directus & Bun):** `ukm-web-api`
- **Frontend (Next.js):** `ukm-web-client`

**Matriks Hak Akses (Role-Based Repository):**
- **Admin/Owner:** Ardan (QA) & Iqbal (PM) - Memiliki hak penuh konfigurasi repositori.
- **Maintainer:** Naufal (Lead FE) & Rafli (Lead BE) - Memiliki hak untuk menyetujui (Approve) dan melakukan Merge Pull Request.
- **Developer (Write):** Farhan, Emil, Rashif - Hanya memiliki hak untuk membuat branch fitur, push kode, dan membuat Pull Request. Dilarang melakukan merge.

---

## 2. 🌿 Strategi Branching & Proteksi Sistem
Kita menggunakan sistem 3 hierarki cabang utama. Sistem GitHub telah dikunci (**Branch Protection Enabled**); tidak ada seorang pun yang bisa melakukan *direct push* ke `main` atau `staging`.

- `main` 🔒 : **ZONA SUCI.** Hanya berisi kode production-ready yang akan di-deploy pada 10 Mei.
- `staging` 🔒 : **ZONA INTEGRASI.** Tempat bertemunya semua kode fitur yang sudah selesai dan siap ditesting (April).
- `feature/*` atau `bugfix/*` 🛠️ : **ZONA KERJA.** Cabang tempat kalian mengetik kode sehari-hari.

**Format Penamaan Branch Kerja (Wajib):**
Gunakan format `tipe/nama-tugas`. Jika ada nomor tiket Trello/Jira, wajib disertakan!
- ✅ Benar: `feature/T-12-login-api`, `feature/tabel-keuangan`, `bugfix/slider-error`
- ❌ Salah: `iqbal-branch`, `update-terbaru`, `fix-semua`

---

## 3. 🔄 Alur Kerja Harian (Daily Workflow)
Gunakan alur ini setiap kali akan memulai pekerjaan:

1. **Selalu Update Lokalmu:**
   ```bash
   git checkout staging
   git pull origin staging
   ```
2. **Buat Branch Baru:**
   ```bash
   git checkout -b feature/nama-fitur-kamu
   ```
3. **Kerjakan & Lakukan Commit Berkala:**
   ```bash
   git add .
   git commit -m "feat: menambahkan komponen tabel keuangan"
   ```
4. **Push ke GitHub & Buka Pull Request (PR):**
   ```bash
   git push origin feature/nama-fitur-kamu
   ```

---

## 4. 📝 Aturan Penulisan Commit (Conventional Commits)
Setiap pesan commit **wajib** menggunakan awalan berikut. Mesin akan menolak commit yang tidak sesuai format:
- `feat:` -> Untuk menambah fitur baru (Contoh: `feat: endpoint get list ukm`)
- `fix:` -> Untuk memperbaiki bug/error (Contoh: `fix: perbaiki padding navbar di mobile`)
- `ui:` -> Khusus untuk perubahan tampilan (Contoh: `ui: ubah warna hero section`)
- `refactor:` -> Merapikan kode tanpa mengubah fungsi
- `chore:` -> Perubahan konfigurasi/dependency

---

## 5. 🛡️ Aturan Pull Request (PR) & Code Review
Aturan berikut ditegakkan secara paksa (*Enforced*) oleh GitHub:

1. **Wajib 1 Approval dari Lead:** PR di Frontend (`ukm-web-client`) wajib di-approve oleh Naufal. PR di Backend (`ukm-web-api`) wajib di-approve oleh Rafli.
2. **PM Tidak Melakukan Code Review:** PM (Iqbal) dan QA (Ardan) hanya akan menguji fungsionalitas di cabang `staging`, bukan membaca baris kodemu.
3. **Squash and Merge:** Saat me-merge PR ke `staging`, Lead wajib menggunakan mode **Squash**.
4. **Selesaikan Conflict Sendiri:** Jika PR-mu *conflict* dengan `staging`, itu tanggung jawabmu (pembuat PR) untuk menarik (`pull`) kode terbaru dari `staging`, memperbaiki *conflict* secara lokal, dan *push* ulang. 

---

## 6. ⚙️ Otomatisasi & Sinkronisasi (Aturan Khusus)

### A. Frontend (Next.js) - Standarisasi Kode Paksa
- Repositori telah dilengkapi dengan **Husky** dan **lint-staged**.
- Setiap kali kalian menjalankan `git commit`, sistem secara otomatis akan menjalankan **Prettier** untuk merapikan spasi, tab, dan format kode kalian.
- Jika kode mengandung error fatal (*linting error*), commit akan dibatalkan otomatis oleh sistem. Jangan mencoba mem-bypass sistem ini!

### B. Backend (Directus & Bun) - Sinkronisasi Skema Database
- **TIDAK ADA PERUBAHAN DATABASE TANPA SNAPSHOT!** Jika kalian mengubah tabel, relasi, atau RBAC melalui *dashboard* Directus di lokal, perubahan itu tidak akan masuk ke Git.
- Kalian wajib menjalankan perintah ini sebelum commit agar teman setim kalian mendapatkan update tabel database terbaru:
  ```bash
  npx directus schema snapshot
  ```
- File `snapshot.yaml` yang dihasilkan wajib di-commit.
- Saat teman lain melakukan `git pull`, mereka wajib menjalankan `npx directus schema apply` untuk menerapkan perubahan database tersebut ke laptop mereka.

---

## 7. 🚨 Aturan Keamanan JANGAN DILANGGAR
1. **JANGAN PERNAH PUSH `.env`!** Kredensial database tidak boleh masuk GitHub. Gunakan file `.env.example` untuk membagikan *template* variabel ke tim.
2. **Karantina Docker & WSL 2:** Semua *development* Backend (Postgres, Directus) wajib dijalankan di dalam kontainer Docker melalui WSL 2 Ubuntu. Dilarang meng-install Postgres langsung di Windows (*bare-metal*) agar environtment lokal kalian identik 100% dengan *production server*.

---

## 8. 📌 Manajemen Tugas & Definition of Done (DoD)

### Manajemen Tiket (Trello)
- Dilarang mengerjakan fitur yang belum ada tiketnya di Trello.
- Geser tiket ke **"In Progress"** saat mulai *coding*.
- Geser tiket ke **"In Review"** dan lampirkan link PR GitHub saat PR dibuka.

### Definition of Done (DoD)
Sebuah tugas (task) **BELUM SELESAI** jika hanya jalan di komputermu! Tugas dianggap Selesai (Done) JIKA:
1. Kode sudah di-push ke GitHub dan melewati otomatisasi pengecekan (Husky).
2. PR sudah di-Approve oleh Lead dan berhasil di-merge ke cabang `staging`.
3. (Khusus BE) Skema Directus sudah di-snapshot jika ada perubahan struktur tabel.
4. Tidak ada error di *console browser* atau *log server*.
5. (Khusus FE) Tampilan tidak berantakan saat dites di ukuran layar HP (*Responsive*).
