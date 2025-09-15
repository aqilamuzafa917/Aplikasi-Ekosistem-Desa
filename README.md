<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"></a></p>

# Sistem Informasi Desa - Backend API

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Backend Laravel untuk layanan Sistem Informasi Desa: pengajuan surat administrasi, artikel, pengaduan, profil desa, data APBDesa, peta, dan indeks IDM. Mendukung rute publik dan panel admin berbasis token (Sanctum).

## Daftar Isi
- [Fitur Utama](#fitur-utama)
- [Arsitektur & Teknologi](#arsitektur--teknologi)
- [Endpoint Utama](#endpoint-utama)
- [Autentikasi Admin (Sanctum)](#autentikasi-admin-sanctum)
- [Instalasi](#instalasi)
- [Konfigurasi Lingkungan](#konfigurasi-lingkungan)
- [Migrasi, Seed, dan Data Contoh](#migrasi-seed-dan-data-contoh)
- [Menjalankan Aplikasi](#menjalankan-aplikasi)
- [Contoh Request](#contoh-request)
- [Pengujian](#pengujian)
- [Kontribusi](#kontribusi)
- [Lisensi](#lisensi)

## Fitur Utama

- Pengajuan berbagai jenis surat (Kematian, Pindah, Kelahiran, Usaha, dst.)
- Pelacakan status pengajuan berdasarkan NIK
- Manajemen pengajuan surat oleh admin desa
- Pembuatan dokumen PDF otomatis untuk surat yang disetujui
- Modul publik: Artikel, Profil Desa, APBDesa, Peta/POI, IDM, Chatbot

## Arsitektur & Teknologi

- Framework: Laravel 12, PHP ^8.2
- Autentikasi Admin: Laravel Sanctum
- PDF: barryvdh/laravel-dompdf
- Basis data: SQLite (default), MySQL/PostgreSQL didukung
- Testing: Pest/PHPUnit
- Frontend: lihat repo [sistem-informasi-desa-fr](https://github.com/IqbalInsanKurnia/sistem-informasi-desa-fr)

## Endpoint Utama

Catatan prefix: semua rute berada di bawah prefix dasar Laravel `api`. Contoh: `GET /api/publik/profil-desa`.

- Publik (tanpa autentikasi):
  - Profil Desa: `GET /api/publik/profil-desa`, `GET /api/publik/profil-desa/{id}`
  - Surat: `POST /api/publik/surat`, `GET /api/publik/surat/{nik}`, `GET /api/publik/surat-latest/{nik}`
  - Surat PDF publik: `GET /api/publik/surat/{nik}/{id}/pdf`
  - Artikel publik: `GET /api/publik/artikel`, `GET /api/publik/artikel-latest`, `GET /api/publik/artikel/{id}`, `POST /api/publik/artikel`
  - Pengaduan publik: `POST /api/publik/pengaduan`
  - APBDesa publik: `GET /api/publik/apbdesa`, `GET /api/publik/apbdesa/multi-tahun`, `GET /api/publik/apbdesa/statistik`, `GET /api/publik/apb-desa/pdf/{tahun?}`
  - Peta/POI publik: `GET /api/publik/map`, `GET /api/publik/map/poi/all`
  - IDM publik: `GET /api/publik/idm`, `GET /api/publik/idm/{tahun}`, `GET /api/publik/idm-stats`
  - Lainnya: `POST /api/publik/chatbot/send`, `POST /api/publik/cek-nik-tanggal-lahir`, `GET /api/publik/penduduk/{nik}`

- Admin (wajib Bearer token Sanctum):
  - Auth: `POST /api/register`, `POST /api/login`, `POST /api/logout`, `GET /api/user`, `GET /api/users`, `POST /api/users/{id}/revoke`, `POST /api/users/{id}/reactivate`
  - Surat: `GET /api/surat`, `GET /api/surat/{id}`, `POST /api/surat`, `PUT /api/surat/{id}`, `PATCH /api/surat/{id}/status`, `DELETE /api/surat/{id}`, `PATCH /api/surat/{id}/restore`, `GET /api/surat/sampah`, `GET /api/surat/stats`, `GET /api/surat/{id}/pdf`
  - Artikel: `GET /api/artikel`, `POST /api/artikel`, `GET /api/artikel/{id}`, `PUT /api/artikel/{id}`, `PATCH /api/artikel/{id}/status`, `DELETE /api/artikel/{id}`, `GET /api/artikel/stats`
  - Pengaduan: `GET /api/pengaduan`, `GET /api/pengaduan/{pengaduan}`, `PATCH /api/pengaduan/{pengaduan}/status`, `DELETE /api/pengaduan/{pengaduan}`, `GET /api/pengaduan/stats`
  - Profil Desa: `GET /api/profil-desa`, `POST /api/profil-desa`, `GET /api/profil-desa/{id}`, `PATCH /api/profil-desa/{id}`, `DELETE /api/profil-desa/{nama_desa}`
  - Penduduk: `GET /api/penduduk`, `POST /api/penduduk`, `PUT /api/penduduk/{nik}`, `DELETE /api/penduduk/{nik}`, `GET /api/penduduk/cari`, `GET /api/penduduk/stats`
  - APBDesa: pendapatan/belanja CRUD, ringkasan `GET /api/apbdesa`
  - POI/Map: `POST /api/map/poi`, `PUT /api/map/poi/{potensi}`, `DELETE /api/map/poi/{potensi}`
  - IDM: variabel/indikator CRUD, `POST /api/idm/{tahun}/recalculate`

Detail lengkap lihat `routes/api.php`.

## Autentikasi Admin (Sanctum)

1) Registrasi (setup awal admin): `POST /api/register`

2) Login: `POST /api/login` → respons berisi token Sanctum

3) Gunakan header: `Authorization: Bearer <token>` untuk semua rute admin

## Instalasi

1. Clone repositori:
   ```bash
   git clone https://github.com/aqilamuzafa917/Sistem-Informasi-Desa-Backend.git
   cd Sistem-Informasi-Desa-Backend
   ```

2. Instal dependensi PHP:
   ```bash
   composer install
   ```

3. Salin file konfigurasi:
   - Windows (PowerShell):
     ```powershell
     copy .env.example .env
     ```
   - macOS/Linux:
     ```bash
     cp .env.example .env
     ```

4. Konfigurasikan database di file `.env`
   - Default proyek menyiapkan SQLite otomatis di `database/database.sqlite` via skrip Composer.
   - Untuk SQLite: pastikan baris berikut aktif:
     ```env
     DB_CONNECTION=sqlite
     DB_DATABASE=./database/database.sqlite
     ```
   - Untuk MySQL/PostgreSQL: sesuaikan kredensial `DB_*`.

5. Generate key dan migrasi database:
   ```bash
   php artisan key:generate
   php artisan migrate
   ```

## Konfigurasi Lingkungan

Variabel penting pada `.env` (contoh umum):
- App: `APP_NAME`, `APP_ENV`, `APP_KEY`, `APP_URL`, `APP_TIMEZONE=Asia/Jakarta`
- Sanctum/Cors (bila ada frontend terpisah): `SANCTUM_STATEFUL_DOMAINS`, `SESSION_DOMAIN`, `CORS_ALLOWED_ORIGINS`
- Mail (opsional): `MAIL_MAILER`, `MAIL_HOST`, `MAIL_PORT`, `MAIL_USERNAME`, `MAIL_PASSWORD`
- Storage: `FILESYSTEM_DISK=public`

### Konfigurasi Desa yang Fleksibel (`config/desa.php`)

File `config/desa.php` menyimpan identitas dan preferensi tampilan desa (nama, alamat, kontak, website, logo, titik pusat peta, dsb). Nilai-nilai ini:

- Dapat diubah dinamis via endpoint admin tanpa perlu deploy ulang.
- Dipakai lintas modul (mis. PDF surat, profil desa, chatbot system prompt, peta) sehingga perubahan langsung tercermin di seluruh aplikasi.

Endpoint terkait:

- Publik: `GET /api/publik/desa-config`
- Admin: `GET /api/desa-config`, `PUT /api/desa-config`

Contoh payload `PUT /api/desa-config` (field opsional boleh dihilangkan):

```json
{
  "kode": "BTJR-TMR",
  "nama_kabupaten": "Kabupaten Bandung Barat",
  "nama_kecamatan": "Batujajar",
  "nama_desa": "Batujajar Timur",
  "alamat_desa": "Jl. Raya Batujajar No.191 ...",
  "kode_pos": "40561",
  "nama_provinsi": "Jawa Barat",
  "jabatan_kepala": "Kepala Desa",
  "nama_kepala_desa": "Pak Rafi",
  "jabatan_ttd": "Kepala Desa",
  "nama_pejabat_ttd": "Pak Rafi",
  "nip_pejabat_ttd": "19XXXXXXXXXXXXXX",
  "sosial_media": "https://www.instagram.com/kecamatanbatujajarkbb",
  "website_desa": "http://localhost:5173",
  "email_desa": "info@batujajartimur.desa.id",
  "telepon_desa": "(022) 68XXXXX",
  "logo_desa": "https://cdn.digitaldesa.com/uploads/profil/300_bandungbarat.png",
  "center_map": [-6.912986707035502, 107.5105222441776]
}
```

Catatan teknis fleksibilitas:

- Controller `DesaConfigController@updateConfig` memvalidasi dan menyimpan perubahan langsung ke file `config/desa.php`. Perubahan berlaku segera setelah request berhasil.
- Bidang seperti `nip_pejabat_ttd`, `sosial_media`, `website_desa`, `email_desa`, `telepon_desa` bersifat opsional (nullable).
- `center_map` adalah array `[lat, lng]` untuk memusatkan peta modul publik.

### Chatbot Desa: Function Calling berbasis Gemini

Chatbot publik (`POST /api/publik/chatbot/send`) menggunakan Gemini API dengan fitur function calling untuk mengakses data real-time dari sistem. System prompt chatbot akan otomatis menyisipkan konteks dari `config/desa.php` (nama desa, kontak, website, sosmed) sehingga respons selaras identitas desa.

Konfigurasi `.env`:

- `GEMINI_API_KEY=...` (wajib)
- `GEMINI_API_KEY_BACKUP=...` (opsional; fallback otomatis bila key utama gagal)

Fungsi yang dapat dipanggil AI (didefinisikan di `App\Http\Controllers\ChatbotController::$availableFunctions` dan dieksekusi melalui `executeFunctionCall`):

- `get_surat_by_nik(nik: string)` → 3 surat terbaru pemohon
- `get_artikel_list(kategori?: string)` → daftar artikel terbaru + rangkuman
- `get_artikel_by_id(id: string)` → detail artikel
- `get_laporan_apbdesa(tahun?: number)` → ringkasan APBDes tahun tertentu/terbaru
- `get_statistik_penduduk()` → statistik demografi ringkas
- `get_idm_data(tahun?: number)` → skor dan status IDM

Cara menambah fungsi baru untuk chatbot:

1) Tambahkan definisi di array `$availableFunctions` (nama, deskripsi, skema parameter).
2) Tambahkan handler di `executeFunctionCall($functionName, $args)` untuk memanggil controller/metode yang tepat dan kembalikan `JsonResponse`.
3) Jika perlu, perbarui system prompt di `getSystemInstruction()` agar AI tahu kapan harus memanggil fungsi baru tersebut.

Alur eksekusi ringkas:

- Chatbot memanggil Gemini dengan daftar fungsi yang tersedia.
- Jika model meminta function call, backend mengeksekusi fungsi lokal, lalu memanggil Gemini lagi dengan `functionResponse` agar AI merangkum hasilnya menjadi jawaban akhir.
- Semua percakapan dan error penting dicatat pada `chatbot_logs` melalui `ChatbotLog` model; admin dapat melihat statistik via endpoint admin.

## Migrasi, Seed, dan Data Contoh

1. Jalankan migrasi:
   ```bash
   php artisan migrate
   ```
2. Seed data contoh utama:
   ```bash
   php artisan db:seed
   # atau jalankan per seeder:
   php artisan db:seed --class=UserSeeder
   php artisan db:seed --class=PendudukSeeder
   php artisan db:seed --class=ArtikelSeeder
   php artisan db:seed --class=PengaduanSeeder
   php artisan db:seed --class=ProfilDesaSeeder
   php artisan db:seed --class=TotalApbDesaSeeder
   php artisan db:seed --class=RealisasiPendapatanSeeder
   php artisan db:seed --class=RealisasiBelanjaSeeder
   php artisan db:seed --class=VariabelIdmSeeder
   php artisan db:seed --class=IndikatorIdmSeeder
   ```

Catatan: Validasi NIK mengacu pada tabel `penduduks`. Gunakan seeder untuk mendapatkan NIK valid saat mencoba endpoint publik surat.

## Menjalankan Aplikasi

- Opsi sederhana: `php artisan serve`
- Mode pengembangan terpadu (server + queue + Vite, butuh Node.js):
  ```bash
  composer run dev
  ```

## Contoh Request

### 1) Membuat Pengajuan Surat Baru (Publik)

Endpoint: `POST /api/publik/surat`

Field umum yang wajib diisi:
- `nik_pemohon` (16 digit, terdaftar di `penduduks`)
- `jenis_surat`
- `keperluan`
- `tanggal_request` (opsional, YYYY-MM-DD)
- `attachment_bukti_pendukung` (opsional; jpg/jpeg/png/pdf, maks 2MB)

Contoh payload berdasarkan jenis surat:

#### SK_KEMATIAN
```json
{
    "nik_pemohon": "3201xxxxxxxxxxxx",
    "jenis_surat": "SK_KEMATIAN",
    "keperluan": "Mengurus akta kematian dan klaim asuransi",
    "nik_penduduk_meninggal": "3201yyyyyyyyyyyy",
    "tanggal_kematian": "2024-03-10",
    "waktu_kematian": "15:30",
    "tempat_kematian": "Rumah Sakit ABC",
    "penyebab_kematian": "Sakit Jantung",
    "hubungan_pelapor_kematian": "Anak Kandung"
}
```

#### SK_PINDAH
```json
{
    "nik_pemohon": "3201xxxxxxxxxxxx",
    "jenis_surat": "SK_PINDAH",
    "keperluan": "Pindah domisili ke luar kota",
    "alamat_tujuan": "Jl. Merdeka No. 10",
    "rt_tujuan": "005",
    "rw_tujuan": "002",
    "kelurahan_desa_tujuan": "Sukmajaya",
    "kecamatan_tujuan": "Cimanggis",
    "kabupaten_kota_tujuan": "Kota Depok",
    "provinsi_tujuan": "Jawa Barat",
    "alasan_pindah": "Mengikuti suami",
    "klasifikasi_pindah": "Antar Kabupaten/Kota",
    "data_pengikut_pindah": [
        {"nik": "3201aaaaaaaaaaaa", "nama": "Nama Anak 1", "hubungan": "Anak"},
        {"nik": "3201bbbbbbbbbbbb", "nama": "Nama Anak 2", "hubungan": "Anak"}
    ]
}
```

#### SK_KELAHIRAN
```json
{
    "nik_pemohon": "3201ibuibuibubbb",
    "jenis_surat": "SK_KELAHIRAN",
    "keperluan": "Pembuatan Akta Kelahiran",
    "nama_bayi": "Budi Santoso",
    "tempat_dilahirkan": "Rumah Bersalin Sehat",
    "tempat_kelahiran": "Bogor",
    "tanggal_lahir_bayi": "2024-03-10",
    "waktu_lahir_bayi": "08:15",
    "jenis_kelamin_bayi": "Laki-laki",
    "jenis_kelahiran": "Tunggal",
    "anak_ke": 1,
    "penolong_kelahiran": "Bidan",
    "berat_bayi_kg": 3.1,
    "panjang_bayi_cm": 50.5,
    "nik_penduduk_ibu": "3201ibuibuibubbb",
    "nik_penduduk_ayah": "3201ayahayahayah",
    "nik_penduduk_pelapor_lahir": "3201ibuibuibubbb",
    "hubungan_pelapor_lahir": "Ibu Kandung"
}
```

#### SK_USAHA
```json
{
    "nik_pemohon": "3201usahawanxxxx",
    "jenis_surat": "SK_USAHA",
    "keperluan": "Pengajuan pinjaman KUR",
    "nama_usaha": "Warung Makan Sedap Mantap",
    "jenis_usaha": "Kuliner",
    "alamat_usaha": "Jl. Raya Desa No. 45",
    "status_bangunan_usaha": "Sewa",
    "perkiraan_modal_usaha": 15000000,
    "perkiraan_pendapatan_usaha": 5000000,
    "jumlah_tenaga_kerja": 2,
    "sejak_tanggal_usaha": "2022-01-15"
}
```

#### REKOM_KIP
```json
{
    "nik_pemohon": "3201ortuxxxxxxxx",
    "jenis_surat": "REKOM_KIP",
    "keperluan": "Pengajuan Kartu Indonesia Pintar",
    "penghasilan_perbulan_kepala_keluarga": 1500000,
    "pekerjaan_kepala_keluarga": "Buruh Harian Lepas",
    "nik_penduduk_siswa": "3201siswasiswaaa",
    "nama_sekolah": "SDN Desa Makmur 01",
    "nisn_siswa": "0012345678",
    "kelas_siswa": "5"
}
```

#### SKTM atau REKOM_KIS
```json
{
    "nik_pemohon": "3201kkkkkkkkkkkk",
    "jenis_surat": "SKTM",
    "keperluan": "Pengajuan Bantuan Sosial / KIS",
    "penghasilan_perbulan_kepala_keluarga": 800000,
    "pekerjaan_kepala_keluarga": "Petani"
}
```

#### SK_KEHILANGAN_KTP
```json
{
    "nik_pemohon": "3201hilangktpxxx",
    "jenis_surat": "SK_KEHILANGAN_KTP",
    "keperluan": "Mengurus penerbitan KTP baru",
    "nomor_ktp_hilang": "3201hilangktpxxx",
    "tanggal_perkiraan_hilang": "2024-03-08",
    "lokasi_perkiraan_hilang": "Pasar Desa",
    "kronologi_singkat": "KTP diperkirakan terjatuh saat berbelanja di pasar sekitar pukul 10 pagi.",
    "nomor_laporan_polisi": "LP/B/123/III/2024/SPKT/POLSEK",
    "tanggal_laporan_polisi": "2024-03-09"
}
```

### 2) Melihat Daftar Surat Berdasarkan NIK Pemohon (Publik)

Endpoint: `GET /api/publik/surat/{nik}`

Warga dapat memeriksa status pengajuan surat mereka dengan menyediakan NIK.

Contoh URL: `/api/publik/surat/3201xxxxxxxxxxxx`

### 3) Mengunduh PDF Surat (Publik)

Endpoint: `GET /api/publik/surat/{nik}/{id}/pdf`

Setelah surat disetujui, warga dapat mengunduh PDF menggunakan kombinasi NIK pemohon dan ID surat.

### 4) Login Admin dan Ambil Data Surat (Admin)

```bash
# Login
curl -X POST http://localhost:8000/api/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"password"}'

# Gunakan token dari respons untuk mengambil daftar surat
curl http://localhost:8000/api/surat \
  -H "Authorization: Bearer <TOKEN>"
```

## Pengujian

Jalankan seluruh test suite:
```bash
php artisan test
# atau
./vendor/bin/pest
```

## Persyaratan Sistem

- PHP ^8.2
- Laravel 12
- Composer
- Database: SQLite (default), MySQL/PostgreSQL opsional

## Kontribusi

Kontribusi sangat diterima! Silakan buka Issue atau Pull Request.

1. Fork repositori
2. Buat branch fitur (`git checkout -b feature/amazing-feature`)
3. Commit perubahan (`git commit -m 'Add some amazing feature'`)
4. Push ke branch (`git push origin feature/amazing-feature`)
5. Buka Pull Request

## Lisensi

Proyek ini dilisensikan di bawah Lisensi MIT - lihat file [LICENSE](LICENSE) untuk detail lebih lanjut.
