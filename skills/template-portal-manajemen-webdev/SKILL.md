---
name: template-portal-manajemen-webdev
description: "Use when updating the WebDev portal template."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [windows, linux, macos]
metadata:
  hermes:
    tags: [website, project-management, portal, cloudflare-workers, cloudflare-pages, deployment, milestone, webdev]
---

# Template Portal Manajemen WebDev

## When to Use

Gunakan skill ini saat menelaah, mengubah, menguji, menginisiasi, atau men-deploy template portal manajemen website ini maupun proyek turunannya.

Panduan untuk bekerja pada template **Portal Manajemen Pengembangan Website** atau proyek yang diturunkan darinya. Template memisahkan portal internal pengelolaan proyek dari situs publik yang sedang dibangun. Gunakan konteks dan keputusan aktual proyek; jangan menganggap nilai placeholder, ZIP, atau direktori kerja yang mirip sebagai keadaan proyek yang benar.

## Sebelum mengubah apa pun

1. Pastikan direktori proyek yang dimaksud pengguna. Bila sumbernya ZIP, baca isinya tanpa mengekstrak cache/runtime yang tidak diperlukan. Abaikan `.wrangler/`, `node_modules/`, dan artefak build saat membaca kode.
2. Baca `CLAUDE.md`/aturan proyek yang aktif, `README.md`, lalu dokumen terkait di `BAHAN/01_Manajemen_Proyek/`. Periksa `git status` sebelum perubahan dan jangan menimpa perubahan pengguna.
3. Bedakan isi template dari keputusan proyek nyata. Hosting, stack, ruang lingkup, rilis, dan perubahan yang mengikat diputuskan pemilik proyek; catat keputusan baru di `Keputusan_dan_Risiko.md` dan tandai keputusan lama sebagai digantikan, bukan menghapus jejaknya.
4. Antarmuka dan dokumentasi proyek berbahasa Indonesia baku. Jangan menaruh token, kata sandi, atau kunci privat di repo maupun `BAHAN/`.

## Arsitektur yang harus dipertahankan

- `BAHAN/` adalah sumber kebenaran dokumen proyek (struktur 00–08). `portal/public/deliverable/` hanya cermin untuk dirender portal.
- `portal/` adalah portal manajemen: HTML/CSS/JS statis dan Worker Cloudflare, dikirim langsung dengan Wrangler; portal tidak memerlukan repositori Git.
- `situs/` adalah produk website. Isinya bebas diganti dengan stack yang disepakati; skrip deploy memakai `BUILD_CMD`/`BUILD_DIR` dan `TARGET=pages|ssh`.
- Navigasi portal hanya didefinisikan di `layout.js` (`NAV`). Identitas merek ada di `konfig.js`; data status/milestone ada di `progres.json`; konfigurasi jalur hosting dan URL ada di `hosting.json`; peta dokumen ada di `deliverable.js` (`DOKUMEN`). Semua berkas itu berada di bawah `portal/public/`.
- Worker di `portal/src/index.js` menangani `/api/saya` dan `/api/status-situs`; rute lain disajikan lewat binding `ASSETS`. Pemeriksaan status hanya boleh menerima ID lingkungan yang terdaftar di `hosting.json`—jangan mengubahnya menjadi proksi URL bebas. Portal internal harus dilindungi Cloudflare Access setelah deployment; pembatasan menu di frontend bukan pengganti gerbang Access.
- Checklist interaktif browser tersimpan di localStorage; status milestone yang mengikat diperbarui di `progres.json`, bukan dianggap selesai hanya karena checklist lokal dicentang.

## Alur kerja proyek

Ikuti tahap berurutan dan jangan menandai milestone selesai tanpa bukti:

| Tahap | Fokus dan bukti utama |
|---|---|
| M1 Fondasi | visi/audiens/ukuran sukses disetujui; arsitektur, stack, hosting, nama dan slug diputuskan; kerangka situs dan pratinjau berjalan |
| M2 Konten & halaman | peta situs disepakati; halaman inti, konten/aset, sistem desain, dan responsivitas selesai |
| M3 Fitur & integrasi | fitur, formulir/pencarian/integrasi, alur pengelolaan konten, keamanan dan privasi diperiksa |
| M4 Uji & optimasi | kasus fungsional, kinerja, aksesibilitas, SEO dan UAT dicatat; target Lighthouse awal halaman utama ≥90 bila disepakati |
| M5 Rilis & serah terima | prasyarat/UAT/domain/HTTPS terpenuhi; deploy produksi, tag rilis, cadangan bila perlu, dokumentasi, tutorial dan akses diserahkan |

Laporan tahap berada di `BAHAN/02_Milestone-1_Fondasi/` sampai `08_Laporan_Akhir/`. Isi bukti aktual, jangan mengarang hasil, skor, persetujuan, atau deploy.

## Aturan perubahan dan operasi berisiko

- Edit dokumen di `BAHAN/`, bukan cermin `portal/public/deliverable/`. Tambah dokumen dengan memperbarui berkas di `BAHAN/` dan `DOKUMEN`; tambahkan entri `NAV` hanya bila perlu menu tersendiri.
- `scripts/inisiasi.py` mengganti placeholder secara luas pada berkas teks. Jalankan tanpa `--terapkan` dahulu, periksa daftar berkas/token dan identitas, lalu minta/pastikan izin sebelum menerapkan. Jalankan sebelum deployment perdana karena nama Worker/Pages diturunkan dari slug.
- `portal/scripts/sync-bahan.sh` membangun ulang cermin dan menghapus isi lama `deliverable/`. Baca guard dan status sumber/cermin sebelum menjalankannya; jangan gunakan `PAKSA=1` kecuali pemilik menyetujui kehilangan perubahan yang terdaftar secara spesifik. Jangan menghapus atau mengganti berkas pengguna tanpa konfirmasi.
- Deployment hanya atas permintaan eksplisit pemilik. Sebelum menjalankan, periksa konfigurasi, target, perubahan Git, build command, dan tujuan aktual. `deploy.sh` dapat mengeksekusi perintah build dan menulis `.deploy.log`; `deploy-ssh.sh` dapat mengunggah/menimpa file, memakai `rsync --delete`, menjalankan `SSH_CLEAN=1` yang menghapus isi jalur server, serta mengeksekusi `REMOTE_POST_CMD`. Tinjau semuanya dan konfirmasikan efek destruktif/produksi sebelum eksekusi.
- Simpan konfigurasi mesin/rahasia di `situs/deploy.config.sh` (di-ignore Git), `.env`/`.dev.vars` yang tidak dikomit, atau secret manager Cloudflare sesuai kebutuhan. Jangan membaca atau mencetak nilai rahasia untuk sekadar memeriksa keberadaannya.
- Untuk perintah/konfigurasi Wrangler yang perlu diubah, muat skill `wrangler` dan gunakan dokumentasi/schema Cloudflare terkini; jangan mengandalkan ingatan pada versi/flag.

## Pengembangan dan verifikasi

- Portal harus diuji lewat `npx wrangler dev -c portal/wrangler.jsonc --port 8788`; server statis `file://` tidak menyediakan API Worker. Kerangka situs dapat dilayani dengan `python -m http.server 8790 --directory situs/public` atau dev server stack aktual.
- Jangan memulai daemon/server jangka panjang tanpa permintaan. Bila server lokal memang diminta, nyatakan port dan cara menghentikannya, lalu verifikasi halaman dan endpoint yang relevan.
- Setelah perubahan kode, jalankan pemeriksaan yang relevan (parse JSON/JSONC dengan parser sesuai, sintaks JS/Python/shell, tes/build yang tersedia) dan lakukan smoke test lokal bila aman. Laporkan hanya hasil yang benar-benar keluar dari perintah.
- Trajektori Git dibuat dengan `python portal/scripts/bangun-trajektori.py` dari riwayat `situs/`. Commit dapat memakai trailer `Milestone: M2` (M1–M5) agar klasifikasi otomatis; skrip mewarisi milestone sebelumnya bila trailer/pemetaan hash tidak ada. Bangun ulang sebelum memperbarui portal setelah perubahan riwayat.

Lihat `references/workflow-reference.md` untuk perintah, struktur deliverable, konfigurasi hosting, dan rincian keselamatan skrip.