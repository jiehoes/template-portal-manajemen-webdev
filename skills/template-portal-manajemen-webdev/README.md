# Skill Hermes — Template Portal Manajemen WebDev

Skill ini berisi panduan kerja untuk template Portal Manajemen Pengembangan Website: struktur portal dan situs, milestone proyek, sumber dokumen, pengembangan lokal, pemeliharaan data, serta kehati-hatian saat sinkronisasi dan deployment.

## Isi paket

```text
template-portal-manajemen-webdev/
├── README.md
├── SKILL.md
└── references/
    └── workflow-reference.md
```

`SKILL.md` memuat aturan inti. Referensi operasional berisi rincian perintah dan jalur Pages/SSH. Tidak ada kode runtime, kredensial, atau ketergantungan paket yang perlu dipasang untuk menggunakan skill ini.

## Instalasi manual ke Hermes fresh install

Salin **folder skill** `skills/template-portal-manajemen-webdev` (bukan hanya file `SKILL.md`) ke direktori `skills/` pada `HERMES_HOME` profil yang akan digunakan. Pertahankan struktur `references/`.

### Windows native

Default data home Hermes Windows adalah `%LOCALAPPDATA%\hermes`. Jalankan PowerShell dari akar repositori ini atau dari direktori induk tempat ZIP diekstrak:

```powershell
$skillName = 'template-portal-manajemen-webdev'
$fromRepo = Join-Path (Get-Location) "skills\$skillName"
$fromZip = Join-Path (Get-Location) $skillName
$sourcePath = if (Test-Path $fromRepo) { $fromRepo } else { $fromZip }
$source = (Resolve-Path $sourcePath).Path
$skillHome = if ($env:HERMES_HOME) { Join-Path $env:HERMES_HOME 'skills' } else { Join-Path $env:LOCALAPPDATA 'hermes\skills' }
$destination = Join-Path $skillHome $skillName
if (Test-Path $destination) { throw "Skill sudah ada di $destination; tinjau/backup sebelum mengganti." }
New-Item -ItemType Directory -Path $skillHome -Force | Out-Null
Copy-Item -LiteralPath $source -Destination $destination -Recurse
```

Jika menggunakan profile bernama atau `HERMES_HOME` khusus, pasang ke `skills/` di home profil tersebut. Jangan menimpa skill dengan nama sama tanpa membandingkan atau mencadangkan versi yang sudah ada.

### Linux / macOS / WSL

Gunakan `~/.hermes/skills/` untuk home bawaan, atau `$HERMES_HOME/skills/` bila home/profile khusus. Salin folder skill utuh ke sana, sehingga hasilnya menjadi `.../skills/template-portal-manajemen-webdev/SKILL.md`.

Setelah pemasangan, mulai sesi Hermes baru dan panggil `/template-portal-manajemen-webdev` untuk memuat panduan. Hermes menampilkan skill terpasang sebagai slash command.

## Instalasi dari GitHub

Repositori skill ini publik: <https://github.com/jiehoes/template-portal-manajemen-webdev>.

Setelah Hermes terpasang, jalankan perintah berikut di terminal untuk memasang skill dari repositori tersebut ke profil Hermes yang aktif:

```bash
hermes skills install jiehoes/template-portal-manajemen-webdev/skills/template-portal-manajemen-webdev
```

Tinjau hasil pemindaian keamanan saat Hermes meminta persetujuan. Setelah berhasil, mulai sesi Hermes baru dan panggil `/template-portal-manajemen-webdev`.

Perintah ini memasang skill dari GitHub; untuk folder lokal atau file ZIP, gunakan petunjuk **Instalasi manual** di atas.

## Cakupan dan batasan

- Skill berlaku untuk template atau proyek turunannya; selalu baca aturan dan keadaan workspace proyek aktual sebelum mengubahnya.
- Deploy hanya dijalankan atas permintaan eksplisit pemilik proyek.
- Sinkronisasi `BAHAN/` dapat menghapus dan membangun ulang cermin deliverable. Skrip deploy SSH dapat menimpa/menghapus file remote atau menjalankan perintah pasca-deploy; tinjau dampaknya sebelum operasi.
- Skill ini mendokumentasikan workflow. Ia tidak otomatis menginisiasi proyek, menyalakan server, menyinkronkan dokumen, atau melakukan deployment.
