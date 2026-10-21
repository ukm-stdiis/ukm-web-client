# 📘 Panduan Kontribusi & Aturan Repositori Proyek UKM STDIIS

Dokumen ini berisi aturan wajib terkait penggunaan Git, GitHub, dan standar kerja tim selama proyek berlangsung (Okt 2026 - Mei 2027). **Semua anggota tim (Frontend & Backend) wajib membaca, memahami, dan mematuhi aturan ini tanpa terkecuali.**

---

## 1. 🏗️ Struktur Repositori
Proyek ini memisahkan antara frontend dan backend untuk kemudahan deployment dan skalabilitas:
- **Backend (Laravel API & Admin):** `ukm-web-api`
- **Frontend (Next.js & Tailwind):** `ukm-web-client`

**Dilarang keras** menggabungkan kode frontend dan backend dalam satu repositori!

---

## 2. 🌿 Strategi Branching
Kita menggunakan sistem 3 hierarki cabang utama. Dilarang melakukan *direct push* ke `main` atau `staging`.

- `main` 🔒 : **ZONA SUCI.** Hanya berisi kode production-ready yang akan di-deploy pada 10 Mei.
- `staging` 🔒 : **ZONA INTEGRASI.** Tempat bertemunya semua kode fitur yang sudah selesai dan siap ditesting (April).
- `feature/*` atau `bugfix/*` 🛠️ : **ZONA KERJA.** Cabang tempat kalian mengetik kode sehari-hari.

**Format Penamaan Branch Kerja:**
Gunakan format `tipe/nama-tugas` atau sertakan ID Trello/Jira jika ada.
- ✅ Benar: `feature/login-api`, `feature/UI-12-tabel-keuangan`, `bugfix/slider-error`
- ❌ Salah: `iqbal-branch`, `update-terbaru`, `fix-semua`

---

## 3. 🔄 Alur Kerja Git (Daily Workflow)
Gunakan alur ini setiap kali akan memulai pekerjaan:

1. **Selalu Update Lokalmu:**
   ```bash
   git checkout staging
   git pull origin staging
   ```
2. **Buat Branch Baru (dari staging yang sudah update):**
   ```bash
   git checkout -b feature/nama-fitur-kamu
   ```
3. **Kerjakan & Lakukan Commit Berkala (Jangan ditimbun!):**
   ```bash
   git add .
   git commit -m "feat: menambahkan komponen tabel keuangan"
   ```
4. **Push ke GitHub:**
   ```bash
   git push origin feature/nama-fitur-kamu
   ```
5. **Buka Pull Request (PR):** Buka PR di GitHub dari `feature/...` menuju `staging`.

---

## 4. 📝 Aturan Penulisan Commit (Conventional Commits)
Setiap pesan commit **wajib** menggunakan awalan berikut agar riwayat kode rapi dan mudah dilacak:
- `feat:` -> Untuk menambah fitur baru (Contoh: `feat: endpoint get list ukm`)
- `fix:` -> Untuk memperbaiki bug/error (Contoh: `fix: perbaiki padding navbar di mobile`)
- `ui:` -> Khusus untuk perubahan tampilan/slicing tanpa ubah logika (Contoh: `ui: ubah warna hero section`)
- `refactor:` -> Merapikan kode tanpa mengubah fungsi (Contoh: `refactor: pecah komponen galeri menjadi file terpisah`)
- `chore:` -> Perubahan konfigurasi/dependency (Contoh: `chore: install library axios`)

---

## 5. 🛡️ Aturan Pull Request (PR) & Code Review
Untuk mencegah kode error merusak aplikasi, aturan berikut diterapkan secara paksa (Enforced) di GitHub:

1. **Wajib 1 Approval:** PR tidak bisa di-merge sebelum disetujui (Approved) minimal oleh 1 orang lain (Lead FE/BE atau PM).
2. **Squash and Merge:** Saat melakukan merge PR, gunakan mode **Squash**. Ini akan menggabungkan belasan commit berantakanmu menjadi 1 baris commit yang rapi di `staging`.
3. **Selesaikan Conflict Sendiri:** Jika PR-mu *conflict* dengan `staging`, itu tanggung jawabmu untuk menarik (`pull`) kode terbaru dari `staging`, memperbaiki *conflict* secara lokal, dan *push* ulang. Jangan bebankan ke Reviewer.

---

## 6. 🚨 Aturan Keamanan JANGAN DILANGGAR
1. **JANGAN PERNAH PUSH `.env`!** File kredensial database dan API key tidak boleh masuk GitHub. Pastikan `.gitignore` sudah mendeteksi `.env`. Jika butuh menyamakan variabel, gunakan `.env.example`.
2. **Jangan Push Folder Node_modules / Vendor:** Biarkan `.gitignore` bekerja.

---

## 7. 📌 Aturan Tambahan Pendukung Proyek (Project Rules)

### A. Manajemen Tugas (Trello / Jira)
- Jangan mengerjakan fitur yang belum ada tiketnya di Trello/Jira.
- Pindahkan tiket ke kolom **"In Progress"** saat kamu mulai *coding*.
- Pindahkan tiket ke kolom **"In Review"** saat kamu membuka PR di GitHub. Sisipkan link PR di tiket tersebut.

### B. Standar Penulisan Kode (Linting/Formatting)
Agar gaya bahasa kode 6 orang tidak belang-belang:
- **Frontend (JS/Next):** Wajib install extension **Prettier** di VS Code dan aktifkan *Format on Save*.
- **Backend (Laravel):** Gunakan **Laravel Pint** untuk standarisasi format PHP.

### C. Definition of Done (DoD)
Sebuah tugas (task) belum dianggap "Selesai" (Done) hanya karena jalan di komputermu! Tugas dianggap Selesai JIKA:
1. Kode sudah di-push ke GitHub.
2. PR sudah di-Approve dan berhasil di-merge ke cabang `staging`.
3. Tidak ada error/warning di *console browser* atau *log server*.
4. Responsif di ukuran layar HP (khusus Frontend).

---
*Dibuat oleh: Iqbal Ramadani (Project Manager)*
