
---

````md
# Jualan Minum – Aplikasi Kasir & Stok Outlet (PHP + MySQL)

Aplikasi web sederhana untuk pencatatan **penjualan minuman per outlet**, input **bahan habis pakai**, laporan penjualan & bahan, analitik + grafik, ekspor CSV/PDF, resep produk (recipe), serta audit log (riwayat aktivitas) untuk keamanan.

Cocok untuk usaha dengan banyak outlet (misal 6 outlet dan bisa bertambah).

---

## ✨ Fitur Utama

### 👤 Role & Akses
- **Admin**
  - Melihat data **global semua outlet** dan per outlet
  - Kelola user (tambah/edit/reset password)
  - Kelola master bahan habis pakai
  - Input stok/bahan harian per outlet
  - Kelola resep produk (recipe)
  - Audit log (pantau aktivitas: create/delete transaksi, dll)
- **Petugas**
  - Hanya melihat data **outlet miliknya**
  - Input penjualan via kasir (klik gambar produk → input qty)

### 🧾 Kasir (Petugas/Admin)
- Tampilan kartu produk (gambar)
- Klik produk → popup qty → simpan transaksi
- Admin bisa pilih outlet (opsional untuk testing)

### 📦 Input Bahan Habis Pakai (Admin)
- Admin input bahan per outlet per tanggal (misal stok pagi)
- Mendukung angka desimal (contoh 2.5 Kg)

### 📊 Laporan & Analitik
- **Laporan penjualan**: harian / mingguan / bulanan / tahunan / rentang
- **Grafik laporan** (Chart.js) mengikuti filter
- **Top produk** (qty & rupiah)
- **Dashboard**: KPI hari ini & bulan ini + grafik 7 hari
- **Detail transaksi** (per jam / per kasir / per produk) sesuai modul yang dibuat

### 🧪 Resep Produk (Recipe)
- Admin bisa set “bahan apa saja dipakai” untuk tiap produk
- Isi `qty_per_unit` (contoh: Es Teh pakai gula 0.02 Kg per cup, dll)

### 🧾 Export
- Export laporan penjualan ke **CSV (Excel)** dan **PDF**
- Export laporan bahan ke **CSV/PDF** (jika modul export tersedia)

### 🔒 Audit Log
- Catat aktivitas penting: siapa melakukan apa, kapan, outlet mana, detail + IP
- Contoh: `sale_create`, `sale_delete`

---

## 🧰 Tech Stack
- PHP (native, tanpa framework)
- MySQL/MariaDB
- AdminLTE (tema UI)
- Chart.js (grafik)
- Bootstrap (dari AdminLTE dependency)

---

## ✅ Requirement

### Minimal
- PHP **8.1+** (disarankan 8.2+)
- MySQL/MariaDB **5.7+ / 10.x**
- Web server Apache/Nginx (Laragon/XAMPP/WAMP OK)
- Ekstensi PHP:
  - `mysqli`
  - `mbstring`
  - `openssl`
  - `json`

### Disarankan (Dev)
- Laragon (Windows) atau XAMPP
- phpMyAdmin untuk import DB

---

## 🚀 Instalasi (Laragon – Windows)

1. **Clone repo** ke folder web Laragon:
   ```bash
   cd C:\laragon\www
   git clone https://github.com/USERNAME/jualan-minum.git
````

2. **Buat database**

   * Buka phpMyAdmin: `http://localhost/phpmyadmin`
   * Buat DB baru misal: `jualan_minum`

3. **Import schema DB**

   * Import file SQL schema (sesuaikan nama file di repo kamu)
   * Jika belum ada file schema, import manual tabel sesuai query SQL yang disediakan selama pengembangan

4. **Konfigurasi koneksi DB**

   * Buka file konfigurasi DB di folder `app/` (misal `app/config.php` atau `app/db.php`)
   * Sesuaikan:

     * DB_HOST, DB_USER, DB_PASS, DB_NAME

   Contoh (ilustrasi):

   ```php
   define('DB_HOST', '127.0.0.1');
   define('DB_USER', 'root');
   define('DB_PASS', '');
   define('DB_NAME', 'jualan_minum');
   ```

5. **Jalankan web**

   * Start Laragon
   * Akses:

     ```
     http://localhost/jualan-minum/public
     ```

---

## 🔑 Akun Default

> (Disesuaikan dengan seed yang kamu pakai)

* Admin:

  * username: `admin`
  * password: `admin123`
* Petugas:

  * username: `petugas1`
  * password: `petugas123`

> Admin dapat reset password user dari menu **Manajemen User** (default reset: `123456`).

---

## 🗂️ Struktur Folder (Ringkas)

```
jualan-minum/
├─ app/
│  ├─ auth.php
│  ├─ helpers.php
│  ├─ audit.php
│  └─ ... konfigurasi DB
├─ layouts/
│  ├─ layout.php
│  └─ partials/ (menu, navbar, sidebar)
├─ public/
│  ├─ index.php
│  ├─ login.php
│  ├─ logout.php
│  ├─ dashboard.php
│  ├─ kasir.php
│  ├─ reports.php
│  ├─ reports_supplies.php
│  ├─ admin_supplies.php
│  ├─ admin_users.php
│  ├─ admin_consumables.php
│  ├─ admin_recipes.php
│  ├─ recipe_form.php
│  ├─ audit_logs.php
│  ├─ api_sale_add.php
│  ├─ api_sale_delete.php
│  ├─ export_sales.php
│  └─ assets/
│     ├─ adminlte/
│     └─ placeholder.png
└─ README.md
```

---

## 🧪 Setup Awal yang Wajib Dilakukan

1. Tambahkan outlet di tabel `outlets` (minimal 1 outlet aktif).
2. Pastikan user `petugas` punya `outlet_id` (wajib untuk petugas).
3. Pastikan produk ada di tabel `products` (aktif).
4. Isi resep produk di menu **Recipe** agar stok otomatis bisa dihitung (jika fitur stok otomatis sudah dipakai).

---

## 🧯 Troubleshooting

### 1) Error `Cannot redeclare h()`

* Pastikan semua include pakai `require_once`
* Jangan mendefinisikan fungsi yang sama di dua file berbeda

### 2) Error `Commands out of sync`

* Biasanya karena statement belum ditutup, atau `get_result()` dipanggil saat statement lain aktif
* Pastikan:

  * `$stmt->close()` dipanggil
  * tidak menjalankan query baru sebelum fetch selesai

### 3) Error `only_full_group_by`

* Gunakan `GROUP BY alias` + `ORDER BY MIN(field)` seperti di laporan yang sudah diperbaiki

### 4) AdminLTE assets 404

* Pastikan struktur folder `public/assets/adminlte/...` sesuai file yang dipakai di layout
* Pastikan `placeholder.png` ada di `public/assets/placeholder.png`

### 5) `$ is not defined`

* jQuery belum ter-load
* Pastikan load urutan:

  1. jQuery
  2. Bootstrap bundle
  3. AdminLTE

---

## 🔐 Catatan Keamanan (Minimal)

* Password disimpan dengan `password_hash()`
* Endpoint sensitif (hapus transaksi, manage user, supplies, recipes) dibatasi role `admin`
* Audit log mencatat aktivitas penting

> Untuk production, disarankan:

* Tambah CSRF token
* Aktifkan HTTPS
* Batasi akses folder dengan .htaccess / konfigurasi server

---

## 📌 Roadmap (Opsional)

* Stok otomatis berkurang berdasarkan resep
* Warning stok menipis (threshold per bahan per outlet)
* Export Excel `.xlsx` native (bukan CSV)
* Multi transaksi dalam 1 struk (cart)

---

## 🤝 Kontribusi

Pull request dipersilakan. Pastikan fitur baru tetap mengikuti:

* role-based access
* prepared statements (anti SQL injection)
* audit log untuk aksi kritikal

---

## 📄 Lisensi

Bebas dipakai untuk kebutuhan internal usaha. (Tambahkan lisensi sesuai kebutuhanmu)

```

---


