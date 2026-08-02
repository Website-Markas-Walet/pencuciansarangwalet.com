---
site: pencuciansarangwalet.com
deploy: { host: unknown, cf_account: null, branch: main, deploy_hook: unknown }
url_patterns: { artikel: "/{slug}/", kota: "/kota/jasa-cuci-sarang-walet-di-{kota-slug}-{provinsi-slug}/", page: "/{slug}/", desain: "/desain/desain-rumah-bangun-walet-ukuran-{NxN}-meter-terbaik-untuk-bisnis-anda/" }
chrome: { header_lines: "163-238", footer_lines: "700-861", byte_identical: "variants:7" }
analytics: { gtm: "GTM-NPLJCRC", ga4_property_id: "", tiktok: "CJA4IDBC77U5K7SPBH0G", fb_verify: "rsw8pfzx25q4w4u4hdoo5g3ncjkeri", adsense: "" }
brand: { primary: "#840800", font: "Montserrat", logo: "/wp-content/uploads/2023/03/cropped-new-crop-logo-pencucian-walet-1024x260.png", favicon: "/wp-content/uploads/2023/03/cropped-icon-logo-pencucian-walet-min-32x32.png" }
ecosystem_menu: absent
contact: { wa: "6287725260196", email: "sales@markaswalet.com" }
media: { local_mb: 354, hotlink: "blogspot:774, localhost:51, gravatar:1" }
content: { articles: 9, kota_unique: 487, kota_admin_level: kabupaten }
cleanup: ["502 file CSS per-halaman Elementor (cruft)", "121/487 halaman kota tanpa <h1>", "51 img http://localhost RUSAK", "media lokal 351MB belum dioptimasi (tanpa WebP/AVIF)", "link internal campur absolute/relative", "sisa staging: admin-ajax.php -> subpath /pencuciansarangwalet/", "chrome tidak seragam (nav 387/509, GTM 457/509, 7 varian header)"]
blockers: ["deploy host tak terkonfirmasi (repo tanpa config; probe live diblokir network policy)", "push ke GitHub ditolak 403 (session ini read-only; butuh write-access: GitHub App contents:write / deploy key)", "ga4_property_id & adsense kosong (konfirmasi pemilik)"]
---

# CMS Readiness — pencuciansarangwalet.com

Ringkasan machine-readable di frontmatter. Detail & artefak siap-konsumsi ada di `_cms/`:
`_cms/README.md`, `_cms/site.config.json`, `_cms/layout.template.html`,
`_cms/content/articles.json`, `_cms/content/kota.dataset.json`, `_cms/media-inventory.md`.

## Catatan penting

- **Identitas:** static HTML export WordPress 6.2 (Astra + Elementor + Page Generator Pro + Header-Footer-Elementor + WPForms). Tidak ada backend hidup — `/wp-json/` file statis mati. Integrasi = **tulis file → commit repo → deploy**, bukan panggil API.
- **Deploy (BLOCKER):** repo tidak menyimpan config deploy apa pun (tanpa `.nojekyll`/`CNAME`/config Cloudflare/`functions/`/workflows/README). Probe header situs live gagal karena network policy memblok domain (gateway 403). Host hanya diketahui pemilik akun. Branch produksi `main`. Cek: GitHub Settings→Pages, atau DNS (`*.pages.dev`=Cloudflare vs `*.github.io`/`A 185.199.108.x`=GitHub Pages).
- **Write-access (BLOCKER):** session ini punya read (repo ter-clone) tapi push ditolak 403 (policy). Rekomendasi: GitHub App fine-grained `contents:write` atau deploy key SSH. Ini membuka push sekaligus jalur commit CMS.
- **Chrome:** header/footer **verbatim** sudah diekstrak ke `_cms/layout.template.html` dengan slot `<!-- CONTENT -->`. **Tidak byte-identik: 7 varian header** antar halaman (kanonik diambil dari homepage). Tidak ada perubahan pada file situs.
- **Konten:** 9 artikel (metadata siap; body masih terbungkus Elementor → perlu ekstraksi, konfirmasi format HTML bersih vs Markdown), 487 kota (**dataset**, bukan 487 halaman manual; 121 tanpa H1), 5 halaman page, 5 halaman `desain` programmatic by ukuran bangunan.
- **Media:** 354 MB lokal (351 MB gambar belum dioptimasi) + **826 hotlink eksternal** (774 blogspot, **51 localhost RUSAK**, 1 gravatar) → rehome ke R2.
- **Ecosystem menu:** belum ada link antar-brand jaringan → kandidat kontrol pusat.
