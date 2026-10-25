# SISTEM PENDAFTARAN BOWLING SEJAYA

Aplikasi SPA satu fail HTML, Firebase Realtime Database, import Excel, analisis pendaftaran masa nyata, dan admin demo.

## Susunan fail

- `index.html` — semua HTML/CSS/JavaScript dan sambungan Firebase.
- `template-guru-akp.xlsx` — templat Excel dengan tajuk tepat `NAMA` dan `KATEGORI`.
- `logo-sekolah.png` — **pilihan**: letak logo sebenar sekolah dengan nama ini di akar projek. Jika tiada, teks LOGO SEJAYA dipaparkan.

## Firebase

Projek: `sksj-2026`. Konfigurasi SDK diberikan oleh pemilik dan telah dipasang pada `index.html`.

Laluan aplikasi: `apps/bowlingSejaya/v1` pada Realtime Database. Laluan ini memisahkan data bowling daripada aplikasi lain yang berkongsi projek Firebase.

Struktur data:

```
apps/bowlingSejaya/v1/
  roster/{id}              -> { name, category, createdAt }
  registrations/{hash}     -> { name, category, spouse, rosterId, createdAt }
  settings/registrationOpen -> boolean
```

**Security Rules tidak disertakan dan tidak diubah.** Rules sedia ada (`.read: true`, `.write: true`) membenarkan semua orang membaca/mengubah/memadam data tanpa autentikasi. Kata laluan UI `sksj` hanya kosmetik, bukan sekatan sebenar. Jangan simpan data sensitif; nama guru dan pasangan boleh diakses awam. Fungsi admin bukan selamat daripada penyalahgunaan. Keselamatan sebenar memerlukan Firebase Authentication dan rules bersyarat, hanya selepas pemilik membenarkan perubahan.

## Import Excel

1. Muat turun `template-guru-akp.xlsx` melalui tab Tetapan.
2. Isikan nama di kolum `NAMA` dan `Guru`/`AKP` di kolum `KATEGORI`.
3. Log masuk melalui tab Tetapan menggunakan `sksj`, pilih Excel, semak pratinjau dan klik **Simpan Senarai ke Firebase**.
4. Nama yang sama dalam kategori yang sama akan dilangkau. Baris yang tidak sah tidak diimport.
5. Pendaftar Guru/AKP memilih nama daripada senarai; PIBG memasukkan nama secara manual.

Import menggunakan SheetJS 0.20.3 dari CDN rasmi. Firebase menggunakan SDK 13.1.0 sebagaimana dibekalkan. Internet diperlukan. Tidak perlu Node.js atau build.

## GitHub + Vercel

1. Buka GitHub dan buat repository, contoh `bowling-sejaya`.
2. Upload `index.html`, `template-guru-akp.xlsx` dan pilihan `logo-sekolah.png` ke root repository. Jangan muat naik README sahaja.
3. Dalam Vercel klik **Add New → Project**, import repository tadi.
4. Pada konfigurasi projek pilih **Framework Preset: Other**, **Root Directory: `./`**; tinggalkan Build Command dan Output Directory kosong (static HTML).
5. Klik **Deploy**. Buka URL yang Vercel beri.
6. Uji pendaftaran, analisis, admin, import Excel dan refresh halaman untuk memastikan data kekal.

Jika Firebase mengembalikan `permission_denied`, semak akses yang berkuat kuasa pada Firebase Console, tetapi **jangan ubah rules tanpa kelulusan pemilik**. Semak juga keperluan domain/API key restrictions Firebase jika dipasang kemudian.

## Ujian fungsi logik

- Radio `Pasangan` menunjukkan medan nama pasangan; `TIADA` menyembunyikan dan mengosongkannya.
- Borang menolak nama kosong / pasangan sama nama / peserta berulang dalam kategori sama.
- Daftar serentak nama sama menggunakan `runTransaction` dan hanya satu pendaftaran dibenarkan.
- Statistik mengira jumlah pendaftar dan jumlah orang termasuk pasangan secara berasingan.
- Data disegerakkan masa nyata menggunakan `onValue`.
- Fail CSV eksport dengan BOM UTF-8 untuk Excel.
- Admin log masuk/kunci UI, tambah/padam roster, import pukal, tutup/buka borang, padam rekod.

**Nota:** Ujian sambungan Firebase secara langsung tidak dibuat di persekitaran penyediaan ini. Sistem perlu diuji pada URL sebenar setelah deploy. Import pukal bukan transaksi serentak menyeluruh: jika sambungan terputus di pertengahan, sebahagian nama mungkin sudah disimpan. Import semula akan melangkau yang telah wujud.
