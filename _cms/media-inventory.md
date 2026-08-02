# Media Inventory — pencuciansarangwalet.com

Untuk item brief #7 (inventarisasi media + tandai hotlink eksternal untuk rehome ke R2).

## 1. Media lokal (di dalam repo)

| Lokasi | Isi | Jumlah | Ukuran | Status |
|---|---|---|---|---|
| `wp-content/uploads/2023/` | Gambar asli (PNG 306, JPG/JPEG 49) | **354 file** | **351 MB** | ⚠️ Berat, belum dioptimasi, tanpa WebP/AVIF |
| `wp-content/uploads/elementor/` | CSS per-halaman generated Elementor | **502 file** | 2.2 MB | 🗑️ Cruft — bisa dibuang saat rebuild |
| `wp-content/uploads/essential-addons-elementor/` | Aset plugin | beberapa | kecil | Cruft |
| **Total `uploads/`** | | **861 file** | **354 MB** | |

Semua media direferensikan lewat path **di-hardcode di HTML** (root-relative), jadi rehome/optimasi media = juga menyentuh HTML.

## 2. Hotlink eksternal (WAJIB rehome ke R2 / cdn.markaswalet.id)

Total **826 `<img>` menunjuk domain eksternal**:

| Host | Jumlah | Catatan |
|---|---|---|
| `1.bp.blogspot.com` | **774** | CDN Blogger — pola sama seperti situs lain di jaringan; rapuh, bisa hilang sewaktu-waktu |
| `http://localhost` | **51** | 🔴 **RUSAK** — sisa staging, gambar mati (broken image di produksi) |
| `0.gravatar.com` | 1 | avatar |

### Rekomendasi
1. **Prioritas 1 — 51 gambar `http://localhost`:** sudah rusak sekarang. Putuskan: ganti aset atau hapus referensinya.
2. **Prioritas 2 — 774 blogspot:** unduh → upload ke R2 (`cdn.markaswalet.id`) → rewrite `src`. Risiko tinggi karena di luar kendali kita.
3. **Optimasi 354 gambar lokal:** konversi ke WebP/AVIF saat pindah ke R2 (351 MB → bisa turun drastis).
4. **Buang 502 CSS Elementor** saat rebuild via template bersih.

## 3. Untuk CMS
- Sediakan langkah **media-rehome**: crawl `src` eksternal → simpan ke R2 → map URL lama→baru → rewrite HTML.
- Setelah rehome, tidak boleh ada `img src` yang keluar dari `cdn.markaswalet.id` atau domain sendiri.
