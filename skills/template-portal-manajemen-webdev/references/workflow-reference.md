# Referensi operasional template

## Struktur dokumen BAHAN

- `00_Referensi/`: bahan, pemilik akun, tautan acuan (tanpa kata sandi/token).
- `01_Manajemen_Proyek/`: visi & tujuan, arahan arsitektur, dokumentasi teknis, keputusan/risiko, rencana proyek.
- `02_Milestone-1_Fondasi/` sampai `06_Milestone-5_Rilis/`: laporan tiap tahap dan rencana rilis.
- `07_Tutorial/`: tutorial pengelola konten, pengunjung, operator teknis.
- `08_Laporan_Akhir/`: laporan proyek dan serah terima.

Dokumen operasional disunting di `BAHAN/`. `sync-bahan.sh` menyinkronkan pohon itu ke `portal/public/deliverable/`. Deploy portal memanggil sinkronisasi terlebih dahulu.

## Menjalankan secara lokal

Dari akar proyek:

```bash
npx wrangler dev -c portal/wrangler.jsonc --port 8788
python -m http.server 8790 --directory situs/public
```

Yang pertama melayani portal beserta `/api/*`; yang kedua hanya sesuai untuk kerangka statis. Framework aktual mungkin memiliki perintah dev sendiri. Portal tidak berfungsi penuh melalui `file://` atau server statis biasa.

Jangan mulai server tanpa permintaan pengguna. Jangan menganggap server yang berjalan adalah produksi.

## Inisiasi template

Perintah contoh (ganti semua nilai dengan identitas yang telah disetujui):

```bash
python scripts/inisiasi.py --nama "Nama Website" --slug nama-website --domain contoh.or.id --pemilik "Nama Pemilik" --pengembang "Nama Pengembang"
```

Tanpa `--terapkan`, skrip hanya menampilkan berkas kandidat yang mengandung token. Periksa workspace, `git status`, dan daftar berkas sebelum memilih `--terapkan`. Slug tervalidasi berupa huruf kecil, angka, dan tanda hubung; nilai nama/pemilik/pengembang tidak boleh berisi tanda kutip ganda. Urutan penggantian kode: domain, slug, lalu token kapital. Setelah penerapan, periksa sisa placeholder dan sinkronisasi dokumen hanya setelah izin untuk operasi itu.

## Data portal dan jalur hosting

- `portal/public/assets/data/progres.json`: identitas, tujuan, statistik, jadwal, lima milestone/checklist, ketergantungan. Centang di UI disimpan lokal; perbarui `selesai` pada JSON untuk status proyek yang resmi.
- `portal/public/assets/data/hosting.json`: target aktif, URL lokal/pratinjau/produksi, proyek Pages, informasi server SSH, portal Worker, dan langkah kesiapan jalur hosting. Setiap perubahan URL/target juga perlu diselaraskan dengan `situs/deploy.config.sh`.
- `portal/public/assets/data/trajektori.json`: keluaran generator Git; jangan edit manual jika dapat dibangun ulang.
- `portal/public/assets/js/layout.js`: satu sumber navigasi (`NAV`). `konfig.js`: nama/slug/domain. `deliverable.js`: peta kode dokumen ke path dan label (`DOKUMEN`).

Worker status-situs menerima ID lingkungan, membaca URL dari `hosting.json`, lalu membuat GET dengan batas waktu 8 detik. Jangan membolehkan klien mengirim URL arbitrer.

## Deploy yang dikonfigurasi

Salin `situs/deploy.config.example.sh` ke `situs/deploy.config.sh` hanya setelah keputusan hosting dibuat; isi file lokal itu tidak boleh masuk Git. Konfigurasikan `TARGET=pages` atau `TARGET=ssh`, `BUILD_CMD`, dan `BUILD_DIR` sesuai stack.

Pages:

```bash
npx wrangler login
npx wrangler pages project create <slug> --production-branch main
bash situs/scripts/deploy.sh
```

SSH:

- Gunakan pengguna terbatas, bukan root; verifikasi host, port, key, dan `SSH_PATH`.
- `SSH_MODE=upload`: bila `rsync` tersedia, skrip memakai `rsync -az --delete`; ini menghapus file remote yang hilang dari build lokal. Tanpa rsync, upload tar biasanya tidak menghapus file remote lama; `SSH_CLEAN=1` menjalankan `rm -rf` pada isi path sebelum ekstraksi.
- `SSH_MODE=git`: server menjalankan fetch/checkout/pull dan opsional `REMOTE_BUILD_CMD`.
- Kedua mode dapat menjalankan `REMOTE_POST_CMD`; anggap sebagai kode shell berhak istimewa dan tinjau sebelum menjalankan.

Portal:

```bash
bash portal/deploy.sh --dry-run
bash portal/deploy.sh
```

Dry-run tetap dapat menjalankan sinkronisasi BAHAN terlebih dahulu. Setelah deploy Worker pertama, pasang Cloudflare Access pada hostname internal dan verifikasi akses dari sesi browser terautentikasi. Perintah deploy bersifat eksternal; jalankan hanya atas permintaan eksplisit dan setelah tujuan/konfigurasi dipastikan.

## Sinkronisasi cermin: pengaman wajib

Skrip sinkronisasi memeriksa berkas cermin yatim serta berkas yang lebih baru dan berbeda dari sumber, lalu membatalkan proses jika terdeteksi. Namun, ketika lolos, skrip tetap melakukan `rm -rf` pada seluruh `portal/public/deliverable/`, membuatnya lagi, lalu menyalin `BAHAN/`. Oleh sebab itu:

1. Periksa Git diff/status serta perubahan cermin dan sumber sebelum sinkronisasi.
2. Jangan menganggap pemeriksaan timestamp sebagai cadangan atau bukti perubahan boleh dibuang.
3. Jangan gunakan `PAKSA=1` kecuali pengguna menyetujui daftar perubahan yang akan hilang.
4. Bila pengguna hanya meminta telaah, dokumentasi, atau edit kode lain, jangan jalankan sinkronisasi sebagai langkah rutin.

## Trajektori Git

```bash
python portal/scripts/bangun-trajektori.py
```

Sumber bawaan skrip adalah repo `situs/`; `REPO_SITUS` dapat dioverride. Generator merekam commit, tanggal, subjek/body, statistik, tag, dan perubahan belum commit. Trailer `Milestone: M3` di body commit menentukan milestone. Tanpa trailer, peta hash di skrip dapat digunakan; selebihnya milestone melekat dari commit sebelumnya, dan commit pertama mulai pada M1. Bila `situs/` belum repo atau belum memiliki commit, generator menghasilkan catatan kosong; jangan mengklaim ada riwayat.

## Rilis dan dokumentasi

M5 mensyaratkan M4/UAT disetujui, konten final, DNS/domain siap, dan jalur HTTPS terverifikasi. Sesudah deploy, cek halaman, formulir, analitik, serta cadangan bila ada data; buat tag versi di repo `situs/`, perbarui trajektori, sinkronkan laporan akhir dan tutorial. Akses/kredensial diserahkan melalui pengelola kata sandi, bukan ditulis ke dokumen.