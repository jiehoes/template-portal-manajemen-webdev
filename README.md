# Template Portal Manajemen WebDev — skill Hermes

Skill Hermes Agent untuk menelaah, mengembangkan, menguji, menginisiasi, dan men-deploy proyek turunan template Portal Manajemen Pengembangan Website.

## Paket unduhan

`template-portal-manajemen-webdev-skill.zip` berisi folder skill lengkap (README, `SKILL.md`, dan referensi), siap diekstrak dan disalin ke direktori skills Hermes.

## Pasang lewat Hermes

```bash
hermes skills install jiehoes/template-portal-manajemen-webdev/skills/template-portal-manajemen-webdev
```

Hermes mengunduh `SKILL.md` beserta referensi yang dirujuknya. Tinjau pemindaian keamanan skill saat instalasi. Setelah terpasang, muat dengan:

```text
/template-portal-manajemen-webdev
```

## Pasang manual

Salin seluruh folder `skills/template-portal-manajemen-webdev/` ke `skills/` pada `HERMES_HOME` profil Hermes yang digunakan. Jangan hanya menyalin `SKILL.md`; folder `references/` diperlukan untuk panduan operasional lengkap.

## Isi

- `skills/template-portal-manajemen-webdev/SKILL.md` — arsitektur, aturan kerja, milestone, keselamatan, dan verifikasi.
- `skills/template-portal-manajemen-webdev/references/workflow-reference.md` — struktur dokumen, perintah lokal, sinkronisasi, deploy Pages/SSH, dan trajektori Git.
- `skills/template-portal-manajemen-webdev/README.md` — dokumentasi paket dan petunjuk instalasi fresh install.

Skill ini tidak membawa kode runtime, rahasia, atau dependensi tambahan. Deployment dan operasi yang berisiko tetap memerlukan permintaan/otorisasi pengguna.
