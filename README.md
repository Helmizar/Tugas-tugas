---

## Analisis STRIDE: Alur Publik, Tautan & Audit

Bagian ini mencakup ancaman pada alur Penerima Eksternal tanpa login, pembuatan dan pencabutan tautan berbagi 24 jam, pengunduhan lewat Backend API, serta integritas Audit Log. Skor risiko dihitung dengan rubrik latihan: **Kemungkinan (1-3) x Dampak (1-3)**. Skor 1-2 = Rendah, 3-4 = Sedang, 6-9 = Tinggi.

### Ringkasan Ancaman

| No | Ancaman | Kategori STRIDE | Elemen DFD | K x D | Skor | Level |
|----|---------|-----------------|------------|-------|------|-------|
| 1 | Penebakan token tautan oleh penerima tidak sah | Spoofing | Backend API | 2 x 3 | 6 | Tinggi |
| 2 | Kebocoran tautan lewat saluran luar dan header Referer | Information Disclosure | Data flow *Halaman opsi unduh file* | 3 x 2 | 6 | Tinggi |
| 3 | Manipulasi parameter permintaan untuk mengunduh file lain | Tampering | Data flow *Mengambil file* | 2 x 3 | 6 | Tinggi |
| 4 | Tautan tetap bisa dipakai setelah dicabut atau kedaluwarsa | Elevation of Privilege | Backend API | 2 x 2 | 4 | Sedang |
| 5 | Pengunduhan oleh penerima anonim tidak terlacak | Repudiation | Backend API | 2 x 2 | 4 | Sedang |
| 6 | Audit log diubah atau dihapus | Tampering | Database Metadata | 2 x 3 | 6 | Tinggi |

### Mitigasi Utama

| No | Mitigasi |
|----|----------|
| 1 | Token CSPRNG minimal 128 bit, rate limiting per IP, respons seragam untuk token tidak valid |
| 2 | HTTPS dengan HSTS, `Referrer-Policy: no-referrer`, token tidak ditulis utuh di log, opsi PIN atau batas unduhan |
| 3 | Token dipetakan ke satu file ID di server, otorisasi divalidasi pada setiap request unduh |
| 4 | Status dan masa berlaku dicek di server pada setiap request, `Cache-Control: no-store`, pencabutan transaksional |
| 5 | Field log wajib (ID tautan, waktu server, IP, hasil), log ditulis dalam transaksi yang sama dengan pemberian akses |
| 6 | Tabel log append-only (Backend API hanya INSERT), Admin hak baca saja, hash chaining atau salinan WORM |

### Asumsi

- **A3-1:** Tautan berupa URL berisi token dan diakses lewat HTTPS.
- **A3-2:** Masa berlaku dan status pencabutan disimpan di Database Metadata dan diperiksa oleh Backend API.
- **A3-3:** Hanya Backend API yang memegang kredensial ke Object Storage.
- **A3-4:** Tidak disebutkan adanya rate limiting, sehingga diasumsikan belum ada.
- **A3-5:** Audit log disimpan di database yang sama dengan metadata, sesuai deskripsi kasus.

Rekomendasi mitigasi di atas belum berarti kontrol telah diimplementasikan. Skenario, dampak, dan mitigasi lengkap dapat dilihat pada panel ancaman setiap elemen di Threat Dragon.
