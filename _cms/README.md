# Persiapan Integrasi CMS Terpusat — pencuciansarangwalet.com

Balasan brief dari session situs ini untuk `cms.markaswalet.id`.
Arsitektur final diterima: Next.js + Supabase (Vercel), media di Cloudflare R2 (`cdn.markaswalet.id`), scope aditif (CMS generate/edit KONTEN lalu commit ke repo; navbar/menu/footer & URL tidak diubah).

Artefak siap konsumsi di folder `_cms/`:
- `site.config.json` — analytics, brand, menu, kontak, url_patterns, content_types, known_issues
- `layout.template.html` — chrome (header+footer verbatim) dengan slot `<!-- CONTENT -->` + placeholder SEO
- `content/articles.json` — 9 artikel (metadata; body perlu ekstraksi terpisah)
- `content/kota.dataset.json` — dataset 487 kota (bukan 487 halaman manual)
- `media-inventory.md` — media lokal + hotlink eksternal untuk rehome R2

---

## ✅ BLOCKER #1 — Mekanisme deploy → **BELUM BISA DIPASTIKAN DARI SINI (butuh pemilik akun)**

**Fakta yang sudah dipastikan:**
- Repo **tidak berisi config deploy apa pun**: tanpa `.nojekyll`, `CNAME`, `_headers`/`_redirects`/`wrangler.toml`, `functions/`, `.github/workflows`, `README`. Isi repo murni file statis di root.
- Probe header HTTP situs live **gagal**: network policy session ini memblok akses ke `pencuciansarangwalet.com` (gateway 403 CONNECT), jadi host tidak bisa di-fingerprint dari sini.

**Kesimpulan:** target deploy dikonfigurasi **di luar repo**. Hanya pemilik akun yang tahu. Branch produksi: `main`.

**Decision-tree 30 detik untuk pemilik akun (pilih satu):**
1. **GitHub → Settings → Pages** aktif (source = `main`, root)? → **GitHub Pages**. Aksi: tambah `.nojekyll` (folder `wp-content` diawali huruf biasa, tapi amankan dari Jekyll).
2. **Cek DNS domain:**
   - `CNAME → *.pages.dev` → **Cloudflare Pages** (sebutkan akun #1/#2 + Deploy Hook URL).
   - `CNAME → *.github.io` atau `A → 185.199.108.x` → **GitHub Pages**.
3. Bukan keduanya → kemungkinan **server pull manual** (cron `git pull`) → beri tahu host + cara trigger.

➡️ **Yang aku butuh dari pemilik:** jawab "host mana + branch + (kalau Cloudflare) akun & Deploy Hook".

## ✅ BLOCKER #2 — Akses tulis untuk CMS

Repo di GitHub, jadi mekanisme commit CMS:
- **Rekomendasi:** **GitHub App** fine-grained (permission `contents:write`) di-install ke repo ini — auditable, mudah dicabut, tidak terikat 1 orang.
- Alternatif: **deploy key SSH** per-repo (read/write).
- **Hindari** PAT personal broad-scope.
- Jika deploy ternyata Cloudflare Pages: CMS cukup commit ke `main`; deploy jalan via **Deploy Hook** (kirim URL-nya, bukan token, lewat channel aman).

⚠️ **Kirim mekanismenya saja — JANGAN commit/kirim token rahasia ke repo atau chat.**

---

## Item 3 — Chrome jadi template ✅
`layout.template.html` = `<head>` global (favicon, fonts, Astra/Elementor CSS, **analytics: GTM-NPLJCRC + TikTok + FB verify**) + **header verbatim (index.html:163-238)** + slot `<!-- CONTENT -->` + **footer verbatim (index.html:700-861)**. Placeholder SEO per-halaman: `{{TITLE}}`, `{{META_DESCRIPTION}}`, `{{CANONICAL_URL}}`, `{{OG_TYPE}}`, `{{OG_IMAGE}}`.
⚠️ Chrome situs ini **tidak seragam** (nav 387/509, GTM 457/509). Template ini menetapkan versi **kanonik** dari homepage.

## Item 4 — URL patterns ✅
Lihat `site.config.json → url_patterns`. Inti: **folder + `/index.html`, trailing slash WAJIB**. Keanehan: link campur absolute/relative; sisa staging `admin-ajax.php` → `/pencuciansarangwalet/`.

## Item 5 — Config situs ✅
Lihat `site.config.json`. Analytics, brand (`#840800`, Montserrat/Lato/Roboto, logo, favicon), menu 5-item, kontak (WA `6287725260196`, email `sales@markaswalet.com`).

## Item 6 — Ekspor konten ✅ (sebagian)
- **Artikel:** `content/articles.json` — 9 artikel, metadata lengkap (title, slug, date, cover, meta desc, category, author). **Body HTML = TODO:** masih terbungkus markup Elementor berat; perlu langkah ekstraksi khusus (usul: render body → bersihkan div Elementor → simpan sebagai HTML/Markdown bersih). Konfirmasi format body yang CMS harapkan.
- **Kota:** `content/kota.dataset.json` — **1 dataset 487 kota** (kota, provinsi, slug, url), bukan 487 halaman manual. 121 halaman ditandai `missing_h1`.

## Item 7 — Media ✅
Lihat `media-inventory.md`. **826 hotlink eksternal** (774 blogspot, **51 localhost RUSAK**, 1 gravatar) → rehome R2. Media lokal 354 file / 351 MB belum dioptimasi.

## Item 8 — Jangan ubah URL/slug ✅
Skema URL dipertahankan. Belum ada perubahan slug, jadi belum perlu peta redirect 301. Jika CMS mengubah slug, aku siapkan 301.

---

## Ringkasan keterbatasan yang harus diterima CMS
1. **Tulis-via-Git, bukan API** — tidak ada backend hidup.
2. **Chrome belum tersentralisasi & tidak seragam** — pakai `layout.template.html` sebagai single-source.
3. **Konten & tampilan menyatu (Elementor)** — body artikel perlu ekstraksi/pembersihan.
4. **487 kota = generator (template+dataset), bukan editor per-halaman.**
5. **URL trailing-slash wajib; SEO artifacts (sitemap/title/meta) harus diregenerasi tiap publish.**
6. **Hutang teknis:** 826 hotlink (51 rusak), 121 kota tanpa H1, 502 CSS cruft, media belum dioptimasi, link campur abs/rel, sisa staging subpath.
