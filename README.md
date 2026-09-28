# KOPI — Uji Organoleptik Kopi Fermentasi

Website penilaian untuk panelis pada penelitian/invention **kopi fermentasi**. Panelis (siswa dan guru) menilai 3 varian kopi berdasarkan aroma, warna, dan rasa, lalu jawabannya otomatis tersimpan ke Google Spreadsheet. Hasil penilaian dirancang untuk dianalisis dengan **uji Friedman**.

**Demo:** https://egabelva.github.io/Kopi/

## Fitur

- Form identitas panelis: **Nama** dan **Status** (Siswa / Guru / Lainnya dengan isian bebas)
- Tabel penilaian 3 sampel × 3 kriteria dalam satu halaman
- Semua isian **wajib diisi** sebelum bisa dikirim
- Data langsung masuk ke Google Spreadsheet
- Tampilan bertema **cyberpunk** (background gelap, neon biru elektrik dan pink, grid neon, bingkai bersudut tajam)
- Responsif untuk HP dan komputer

## Sampel dan Kriteria

**Sampel** (kopi dengan lama fermentasi berbeda):

- 24 Jam
- 48 Jam
- 72 Jam

**Skala penilaian 1–5:**

| Skor | Aroma | Warna | Rasa |
|------|-------|-------|------|
| 1 | Sangat khas dan kuat | Sangat menarik | Sangat asam |
| 2 | Khas dan kuat | Menarik | Asam |
| 3 | Netral | Netral | Netral |
| 4 | Tidak kuat | Tidak menarik | Tidak asam |
| 5 | Sangat tidak kuat | Sangat tidak menarik | Sangat tidak asam |

## Teknologi

- HTML, CSS, dan JavaScript (satu file, tanpa library tambahan)
- Font dari Google Fonts (Orbitron dan Rajdhani)
- Google Apps Script (Web App) sebagai penghubung ke Google Spreadsheet
- GitHub Pages sebagai hosting

## Struktur Proyek

```text
Kopi/
├── index.html   # Website (tampilan, form, dan skrip pengirim data)
├── Code.gs      # Kode Google Apps Script (disalin ke editor Apps Script)
└── README.md
```

## Alur Kerja

```text
Panelis mengisi form di website (GitHub Pages)
        │
        ▼
JavaScript mengirim data (POST) ke URL Web App Apps Script
        │
        ▼
Apps Script menulis data ke sheet "Data" di Spreadsheet "Kopi"
```

Setiap pengiriman menghasilkan **satu tabel tersendiri** di sheet "Data": baris pertama berisi nama, status, dan waktu; lalu header (Sampel, Aroma, Warna, Rasa); lalu 3 baris nilai untuk tiap sampel. Tabel berikutnya otomatis diletakkan di sebelah kanan dengan jarak 1 kolom.

## Cara Setup

### 1. Siapkan Google Spreadsheet

1. Buat Google Spreadsheet baru dengan nama **Kopi**.
2. Ganti nama tab sheet (bukan named range) menjadi persis **Data**.

### 2. Pasang Google Apps Script

1. Buka **Extensions → Apps Script** dari spreadsheet tersebut.
2. Hapus kode bawaan, lalu tempel seluruh isi `Code.gs`.
3. Klik **Deploy → New deployment**, lalu pilih:
   - Type: **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
4. Klik **Authorize access** dan izinkan akses (jika muncul peringatan "Google hasn't verified this app", pilih **Advanced → Go to ... (unsafe) → Allow**).
5. Salin URL Web App yang diakhiri `/exec`.

> Untuk memperbarui kode di kemudian hari, gunakan **Deploy → Manage deployments**, edit deployment yang ada, lalu pilih **New version** agar URL tidak berubah.

### 3. Hubungkan website ke Apps Script

Buka `index.html`, cari baris berikut, lalu ganti isinya dengan URL Web App dari langkah sebelumnya:

```js
const APPS_SCRIPT_URL = "PASTE_URL_APPS_SCRIPT_DISINI";
```

### 4. Deploy ke GitHub Pages

1. Push semua file ke repository GitHub.
2. Buka **Settings → Pages**.
3. Pilih **Deploy from a branch**, branch `main`, folder `/ (root)`, lalu **Save**.
4. Tunggu 1–2 menit, website akan aktif di `https://<username>.github.io/<nama-repo>/`.

## Catatan Teknis

- **Pengiriman memakai mode `no-cors`.** Website tidak bisa membaca balasan dari Apps Script, sehingga pesan "berhasil" muncul setelah data terkirim tanpa error jaringan. Jika data tidak muncul di spreadsheet, cek menu **Executions** di Apps Script untuk melihat error-nya.
- **Panelis boleh mengirim berkali-kali.** Tidak ada pembatasan pengiriman; memuat ulang halaman akan menampilkan form kosong lagi.
- **URL Apps Script terlihat di kode sumber website.** Ini keterbatasan hosting statis seperti GitHub Pages, jadi siapa pun yang membuka kode sumber bisa melihatnya.
- **Untuk analisis uji Friedman**, data per panelis di sheet "Data" berbentuk tabel terpisah. Perhitungan Friedman membutuhkan satu matriks gabungan (baris = panelis, kolom = sampel, satu matriks per kriteria), jadi data perlu disusun ulang terlebih dahulu sebelum dihitung.
