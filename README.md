# 🛠 Panduan Kontribusi & Arsitektur Web ORMAWA STDIIS

Selamat datang di repositori frontend ORMAWA STDIIS. Proyek ini menggabungkan platform untuk 16+ UKM ke dalam satu basis kode terpusat (Next.js + Directus). Untuk mencegah *technical debt* dan kerusakan sistem saat produksi, repositori ini menerapkan aturan kualitas (*quality gates*) yang ketat.

Silakan baca dan pahami seluruh dokumen ini sebelum Anda menulis atau mengirim kode.

---

## 1. Arsitektur Tunggal (Bun)

Kita menerapkan pendekatan tunggal untuk menyeimbangkan kecepatan pengembangan lokal dan stabilitas server produksi:
- **Lokal & Produksi (Developer & Production):** Wajib menggunakan **Bun** murni sebagai *package manager* (`bun install`, `bun add`). Dilarang keras menggunakan `npm`, `yarn`, atau `pnpm` agar tidak terjadi bentrok *lockfile*.

---

## 2. Persiapan Lingkungan Lokal (Setup)

Langkah ini wajib dilakukan oleh setiap anggota tim setelah melakukan *clone* repositori.

1. Buka terminal di direktori proyek (gunakan WSL Ubuntu).
2. Jalankan perintah instalasi:
   ```bash
   bun install
   ```
3. *Catatan:* Perintah di atas akan secara otomatis memasang seluruh dependensi proyek sekaligus mengaktifkan **Husky** (Git Hooks) di lokal Anda. Tanpa langkah ini, Anda tidak akan bisa melakukan sinkronisasi kode ke GitHub.

---

## 3. Standar Pesan Commit (Strict Commit Convention)

Kami tidak menerima pesan *commit* yang tidak jelas seperti `"update"`, `"fix bug"`, atau `"beres"`. Husky akan **membatalkan** *commit* Anda di lokal jika tidak mengikuti awalan berikut.

**Format Wajib:** `tipe: deskripsi singkat (maksimal 70 karakter)`

**Daftar Tipe yang Diizinkan:**
*   `feat:` -> Penambahan fitur baru backend/frontend (Contoh: `feat: integrasi endpoint get list ukm`)
*   `fix:` -> Perbaikan bug/error (Contoh: `fix: atasi memory leak pada komponen galeri`)
*   `ui:` -> Khusus perubahan tampilan/styling Figma (Contoh: `ui: sesuaikan padding navbar untuk viewport mobile`)
*   `refactor:` -> Merestrukturisasi kode tanpa mengubah fungsionalitas (Contoh: `refactor: pisahkan fungsi fetch ke utils terpisah`)
*   `chore:` -> Pembaruan konfigurasi, dependensi, atau setup (Contoh: `chore: update versi eslint`)

---

## 4. Alur Kerja Git (Git Workflow) & Pull Request

Cabang `main` dan `staging` dikunci oleh sistem (*Protected Branches*). Tidak ada *push* langsung yang diizinkan, sekecil apa pun perubahannya.

### 1. Arsitektur Cabang Utama

*   **`staging` (Lingkungan Pengembangan/Integrasi)**
    *   Ini adalah **Default Branch** repositori.
    *   Semua fitur, perbaikan, dan pembaruan dari setiap pengembang **wajib** bermuara di cabang ini.
    *   Cabang ini tidak akan dipublikasikan ke pengguna akhir, melainkan diuji secara internal.
*   **`main` (Lingkungan Produksi)**
    *   Cabang ini **terkunci rapat**. Hanya berisi kode stabil yang siap ditarik oleh Docker server kampus.
    *   **Dilarang keras** menargetkan Pull Request (PR) harian ke cabang ini.

---

### 2. Alur Kerja Harian Developer (SOP Pengembang)

Setiap anggota tim wajib mengikuti urutan perintah ini setiap kali memulai dan menyelesaikan tugas baru.

#### Langkah 1: Sinkronisasi Fondasi Lokal
Selalu mulai pekerjaan dari cabang `staging` yang paling mutakhir. Jangan menimpa riwayat dengan *merge commit*.
```bash
git checkout staging
git pull --rebase origin staging
```

#### Langkah 2: Buat Cabang Kerja (Branching)
Tidak ada yang boleh menulis kode langsung di `staging`. Buat cabang baru khusus untuk tugas spesifik Anda dengan format `<nama>-<tugas>`.
```bash
# Contoh: naufal-ui-navbar atau rafli-api-ukm
git checkout -b <nama-cabang>
```

#### Langkah 3: Eksekusi Kode & Commit
Lakukan pekerjaan Anda. Saat menyimpan perubahan, Husky akan memvalidasi linter dan sintaks TypeScript secara otomatis. Gunakan format standar mutlak:
*   `feat: <pesan>` -> Menambah fitur baru
*   `fix: <pesan>` -> Memperbaiki bug/error
*   `ui: <pesan>` -> Perubahan tampilan antarmuka
*   `refactor: <pesan>` -> Merestrukturisasi kode tanpa mengubah fungsi
*   `chore: <pesan>` -> Perubahan konfigurasi/dependency

```bash
git add .
git commit -m "feat: tambahkan endpoint tabel kas ukm"
```

#### Langkah 4: Dorong (Push) Cabang ke Server
Unggah cabang kerja Anda ke repositori GitHub.
```bash
git push -u origin <nama-cabang>
```

#### Langkah 5: Buka Pull Request (PR)
1. Buka repositori di GitHub.
2. Klik tombol **Compare & pull request** pada cabang yang baru Anda dorong.
3. ⚠️ **KRUSIAL:** Pastikan **Base branch (target)** adalah `staging`, bukan `main`.
4. Isi deskripsi PR dengan jelas.
5. Tunggu *Code Review* dari Project Manager dan pastikan GitHub Actions (CI/CD) menunjukkan status *Passed* (Hijau).

---

### 3. Alur Rilis Produksi (Khusus Project Manager)

Proses ini hanya dieksekusi oleh Project Manager saat aplikasi di `staging` sudah teruji, bebas *bug*, dan siap diluncurkan.

1. Buka tab Pull Requests di GitHub.
2. Buat *New pull request*.
3. Atur target: **Base branch = `main`** dan **Compare branch = `staging`**.
4. Beri judul "Release PR: Pembaruan Modul UKM [Tanggal]".
5. *Merge* PR tersebut. Server kampus akan memproses pembaruan ini ke lingkungan produksi.

---

### 4. Penegakan Aturan (Zero Tolerance)

Sistem akan langsung menolak dan menutup (*close*) Pull Request jika:
1. Menargetkan `main` sebagai dasar cabang (*base branch*).
2. Format pesan *commit* melanggar konvensi (tidak diawali `feat:`, `fix:`, dll).
3. Gagal melewati pengecekan tipe data TypeScript atau Linter di tahap CI/CD server.

---

## 5. Quality Gates (Penjaga Kualitas Kode)

Sistem tidak menerima alasan *"di laptop saya jalan"*. Kode Anda akan diuji secara otomatis oleh dua lapis pertahanan:

### Lapis 1: Husky & lint-staged (Lokal)
Setiap kali Anda menekan `git commit`, Husky akan mencegatnya dan menjalankan:
1. **Pengecekan Pesan Commit:** Memastikan Anda menggunakan `feat:`, `fix:`, dll.
2. **Auto-Format:** Merapikan indentasi menggunakan Prettier.
3. **Validasi ESLint & TypeScript:** Mencegah deklarasi tipe data `any` dan kode yang tidak terpakai (*unused variables*).
*Jika gagal, commit akan dibatalkan otomatis.*

### Lapis 2: GitHub Actions (Server CI)
Saat Anda membuat Pull Request, server GitHub akan memverifikasi ulang seluruh proyek dari nol (menginstal dependensi dengan Bun, menjalankan linter, dan `tsc --noEmit`). 
*Jika muncul tanda ❌ (Merah), tombol "Merge" akan diblokir. Anda wajib melihat log error, memperbaikinya di lokal, lalu commit & push ulang.*

---

## 6. Penyelesaian Masalah (Troubleshooting)

**Masalah 1: "Command bunx not found" atau hook gagal dieksekusi**
Buka terminal WSL Anda dan jalankan perintah ini untuk memberikan izin eksekusi pada skrip bash:
```bash
chmod +x .husky/pre-commit
chmod +x .husky/commit-msg
```

**Masalah 2: Commit saya ditolak oleh Husky karena TypeScript Error, padahal filenya tidak saya ubah?**
Ini terjadi karena `tsc --noEmit` memeriksa seluruh proyek. Jika ada tipe data yang rusak di file lain (yang berdampak pada file Anda), Anda harus memperbaikinya atau mendiskusikannya dengan tim.

**Masalah 3: Saya salah ketik pesan commit dan terblokir.**
Cukup jalankan ulang `git commit -m "tipe: pesan yang benar"` dengan format yang disyaratkan. Jangan pernah menggunakan parameter `--no-verify`.