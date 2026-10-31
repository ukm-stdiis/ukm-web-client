# 🛠 Panduan Kontribusi & Arsitektur Web ORMAWA STDIIS

Selamat datang di repositori frontend ORMAWA STDIIS. Proyek ini menggabungkan platform untuk 16+ UKM ke dalam satu basis kode terpusat (Next.js + Directus). Untuk mencegah _technical debt_ dan kerusakan sistem saat produksi, repositori ini menerapkan aturan kualitas (_quality gates_) yang ketat.

Silakan baca dan pahami seluruh dokumen ini sebelum Anda menulis atau mengirim kode.

---

## 1. Arsitektur Tunggal (Bun)

Kita menerapkan pendekatan tunggal untuk memaksimalkan kecepatan pengembangan lokal dan stabilitas server produksi:

- **Lokal & Produksi (Developer & Production):** Wajib menggunakan **Bun** murni sebagai _package manager_ (`bun install`, `bun add`). Dilarang keras menggunakan `npm`, `yarn`, atau `pnpm` agar tidak terjadi bentrok _lockfile_.

---

## 2. Persiapan Lingkungan Lokal (Setup)

Langkah ini wajib dilakukan oleh setiap anggota tim setelah melakukan _clone_ repositori.

1. Buka terminal di direktori proyek (gunakan WSL Ubuntu).
2. Jalankan perintah instalasi:
   ```bash
   bun install
   ```
3. _Catatan:_ Perintah di atas akan secara otomatis memasang seluruh dependensi proyek sekaligus mengaktifkan **Husky** (Git Hooks) di lokal Anda. Tanpa langkah ini, Anda tidak akan bisa melakukan sinkronisasi kode ke GitHub.

---

## 3. Standar Pesan Commit (Strict Commit Convention)

Kami tidak menerima pesan _commit_ yang tidak jelas seperti `"update"`, `"fix bug"`, atau `"beres"`. Husky akan **membatalkan** _commit_ Anda di lokal jika tidak mengikuti awalan berikut.

**Format Wajib:** `tipe: deskripsi singkat (maksimal 70 karakter)`

**Daftar Tipe yang Diizinkan:**

- `feat:` -> Penambahan fitur baru backend/frontend (Contoh: `feat: integrasi endpoint get list ukm`)
- `fix:` -> Perbaikan bug/error (Contoh: `fix: atasi memory leak pada komponen galeri`)
- `ui:` -> Khusus perubahan tampilan/styling Figma (Contoh: `ui: sesuaikan padding navbar untuk viewport mobile`)
- `refactor:` -> Merestrukturisasi kode tanpa mengubah fungsionalitas (Contoh: `refactor: pisahkan fungsi fetch ke utils terpisah`)
- `chore:` -> Pembaruan konfigurasi, dependensi, atau setup (Contoh: `chore: update versi eslint`)

---

## 4. Alur Kerja Git (Git Workflow) & Pull Request

Cabang `main` dan `development` dikunci oleh sistem (_Protected Branches_). Tidak ada _push_ langsung yang diizinkan, sekecil apa pun perubahannya.

1. **Buat Cabang (Branch):** Buat cabang baru dari `development` dengan format `<nama>-<tugas>`.
   _(Contoh: `naufal-ui-hero` atau `rafli-api-auth`)_
2. **Commit:** Lakukan _commit_ bertahap sesuai format di atas.
3. **Push & PR:** Lakukan `git push` dan buka **Pull Request (PR)** di GitHub.
4. **Review:** Kode tidak bisa digabungkan (_merge_) sebelum mendapat _Approval_ dari Project Manager/Lead Code dan lolos pengecekan CI/CD.

---

## 5. Quality Gates (Penjaga Kualitas Kode)

Sistem tidak menerima alasan _"di laptop saya jalan"_. Kode Anda akan diuji secara otomatis oleh dua lapis pertahanan:

### Lapis 1: Husky & lint-staged (Lokal)

Setiap kali Anda menekan `git commit`, Husky akan mencegatnya dan menjalankan:

1. **Pengecekan Pesan Commit:** Memastikan Anda menggunakan `feat:`, `fix:`, dll.
2. **Auto-Format:** Merapikan indentasi menggunakan Prettier.
3. **Validasi ESLint & TypeScript:** Mencegah deklarasi tipe data `any` dan kode yang tidak terpakai (_unused variables_).
   _Jika gagal, commit akan dibatalkan otomatis._

### Lapis 2: GitHub Actions (Server CI)

Saat Anda membuat Pull Request, server GitHub akan memverifikasi ulang seluruh proyek dari nol (menginstal dependensi dengan Bun, menjalankan linter, dan `tsc --noEmit`).
_Jika muncul tanda ❌ (Merah), tombol "Merge" akan diblokir. Anda wajib melihat log error, memperbaikinya di lokal, lalu commit & push ulang._

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
