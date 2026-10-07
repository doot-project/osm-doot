# 🚀 GTPS OnSuperMain (OSM) Fast Download & Anti-Bug Setup

Repositori ini dirancang khusus untuk hosting file cache Growtopia Private Server (GTPS) seperti `items.dat`, textures (`game/`, `interface/`), dan `audio/` di GitHub dengan koneksi **Fast Download** dan **Bebas Bug Download (Anti-Loop / Anti-Error)**.

---

## ⚠️ Penyebab Utama Bug Download di GTPS & Solusinya

| Masalah yang Sering Terjadi | Penyebabnya | Solusi di Repo Ini |
| :--- | :--- | :--- |
| **Download Data Gagal / Stuck** | Menggunakan `raw.githubusercontent.com` langsung (GitHub memblokir IP karena rate-limit HTTP 429/403). | Menggunakan **jsDelivr CDN** (`cdn.jsdelivr.net`) yang memiliki server CDN global gratis dan tanpa rate limit. |
| **Looping "Updating Items Data"** | Nilai hash `items.dat` yang dikirim server tidak sama dengan file yang ada di CDN. | Gunakan tool `tools/run_hash_calculator.bat` untuk menghitung hash yang 100% akurat. |
| **Error 404 Not Found** | Lupa menambahkan tanda garis miring (`/`) di akhir CDN Path. | Format path dipastikan berakhiran `/` (contoh: `gh/USER/REPO@main/cache/`). |
| **File Corrupt Saat Diupload** | Git di Windows secara otomatis mengubah baris akhir (line-ending CRLF/LF) file biner seperti `.dat` dan `.rttex`. | Disertakan file `.gitattributes` agar file biner tidak pernah dirusak oleh Git. |

---

## 📁 Struktur Folder Repository GitHub

Pastikan struktur repository Anda di GitHub persis seperti ini:

```text
osm/
├── .gitattributes             <-- Wajib ada (mencegah file items.dat & rttex corrupt)
├── README.md
├── cache/
│   ├── items.dat              <-- File items.dat GTPS Anda letakkan di sini
│   ├── audio/                 <-- File audio/musik custom (.wav, .ogg, .mp3)
│   ├── game/                  <-- File texture sprites/items (.rttex)
│   └── interface/             <-- File texture GUI/interface (.rttex)
├── server_code/
│   ├── OnSuperMain_Example.cpp
│   ├── OnSuperMain_Example.js
│   └── cloudflare_worker.js
└── tools/
    ├── hash_calculator.js
    └── run_hash_calculator.bat
```

---

## 🛠️ Langkah-Langkah Pemasangan

### 1. Masukkan File `items.dat`
Salin file `items.dat` dari GTPS Anda ke dalam folder:
`osm/cache/items.dat`

*(Jika Anda punya custom texture `.rttex`, masukkan ke dalam folder `cache/game/` atau `cache/interface/`)*

### 2. Hitung Hash items.dat
Jalankan file:
`tools/run_hash_calculator.bat`
*(Atau ketik perintah `node tools/hash_calculator.js` di terminal)*

Output akan menampilkan angka hash, contoh:
```text
[+] Items.dat Hash (Signed): 1435564073
```
Catat angka ini untuk dimasukkan ke kode server GTPS Anda.

### 3. Upload ke GitHub
1. Buat repository baru di [GitHub](https://github.com/new), misalnya dengan nama: `gtps-cache` (pilih Public).
2. Upload seluruh isi folder `osm/` ke repository tersebut.
3. Pastikan branch default Anda adalah `main` (atau `master`).

---

## ⚡ Konfigurasi OnSuperMain di Server GTPS

### Format CDN (jsDelivr)
- **CDN Host**: `cdn.jsdelivr.net`
- **CDN Path**: `gh/<USERNAME_GITHUB>/<NAMA_REPO>@<BRANCH>/cache/`
  *(Ganti `<USERNAME_GITHUB>`, `<NAMA_REPO>`, dan `<BRANCH>` sesuai repositori Anda)*
  *(Contoh: `gh/developergt/gtps-cache@main/cache/`)*
- **Wajib**: Pastikan selalu diakhiri dengan tanda slash `/`!

---

### Contoh Kode C++ (Variant List)
```cpp
VariantList varList;
varList[0] = "OnSuperMainStartAcceptLogon";
varList[1] = 1435564073;                                     // Masukkan hasil dari hash_calculator
varList[2] = "cdn.jsdelivr.net";                             // Host CDN
varList[3] = "gh/USERNAME/REPO@main/cache/";                 // Path CDN (Wajib akhiran slash '/')
varList[4] = "cc.cz.madkite.freedom org.aqua.gg";            // Token hash
varList[5] = "";

// Kirim ke player
SendVariantList(peer, varList, 0, 1);
```

---

### Contoh Kode Node.js
```javascript
peer.sendVariant([
    "OnSuperMainStartAcceptLogon",
    1435564073,                             // Hash items.dat
    "cdn.jsdelivr.net",                     // Host CDN
    "gh/USERNAME/REPO@main/cache/",         // Path CDN
    "cc.cz.madkite.freedom org.aqua.gg"     // Token
]);
```

---

## 🔍 Cara Tes Link di Browser
Sebelum membuka server untuk pemain, buka link ini di browser Anda:
```text
https://cdn.jsdelivr.net/gh/USERNAME/REPO@main/cache/items.dat
```
- Jika file langsung terdownload di browser: **BERHASIL! Setup Anda sudah benar dan siap digunakan.**
- Jika muncul error 404: Periksa kembali ejaan Username, Nama Repo, atau Branch Anda.

---

## 🔄 Cara Update `items.dat` Tanpa Delay Cache
Karena jsDelivr menyimpan cache file agar download cepat, saat Anda mengupdate `items.dat` baru di GitHub, bersihkan cachenya dengan cara:
Buka URL purge di browser:
```text
https://purge.jsdelivr.net/gh/USERNAME/REPO@main/cache/items.dat
```
Setelah dibuka, cache akan langsung diperbarui dalam hitungan detik!
