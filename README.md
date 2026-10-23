# Arabika

Website pembelajaran bahasa Arab fusha untuk pemula berbahasa Indonesia. HTML, CSS, JavaScript modules, dan Vercel Node Functions; tanpa dependency aplikasi atau database.

## Deploy ke GitHub dan Vercel

1. Ekstrak ZIP. Masuk folder `arabika`.
2. Buat repository GitHub. Upload **isi folder arabika**, termasuk `package.json`, `vercel.json`, folder `public`, dan folder `api`, ke root repository. Jangan upload ZIP sebagai satu file.
3. Di Vercel: Add New → Project → impor repository GitHub.
4. Framework Preset: **Other**. Root Directory: root repository (atau `arabika` bila Anda mengunggah folder induknya). Build Command: `npm run build`. Output Directory: `dist`. Pilih Node.js 22.x pada Project Settings jika tersedia.
5. Tambahkan Environment Variables berikut sebelum deploy:
   - `OPENAI_API_KEY`: API key OpenAI milik Anda.
   - `OPENAI_MODEL`: `gpt-4.1-mini` (opsional; dapat diganti model yang mendukung Chat Completions).
   - `OPENAI_TRANSCRIBE_MODEL`: `gpt-4o-mini-transcribe` (opsional).
   - `AI_ACCESS_CODE`: kode akses bersama pilihan Anda (opsional; disarankan untuk membatasi pemakaian API berbayar).
6. Klik Deploy. Perubahan environment variable memerlukan Redeploy.
7. Bila kode akses diaktifkan, masukkan kode tersebut di Pengaturan website. Jangan memasukkan API key pada halaman web.

Website dan latihan mandiri dapat berjalan tanpa key. Tutor serta transkripsi memerlukan API key dengan akses model dan saldo API aktif; langganan ChatGPT tidak menjadi konfigurasi API aplikasi ini. Key hanya dibaca di server dan tidak disertakan ke browser.

## Menjalankan lokal

Memerlukan Node.js 22 atau lebih baru. Tidak perlu npm install karena tidak ada dependency.

```sh
cp .env.example .env.local
# Isi .env.local bila ingin menggunakan AI
npm run dev
```

Buka http://localhost:3000. Mikrofon berjalan pada localhost atau HTTPS. Jangan membuka index.html dengan file://.

```sh
npm test
npm run build
```

## Fitur

- Dashboard responsif, enam tema: perkenalan, keluarga, kelas, rutinitas, makanan, waktu.
- Istima': suara sintetis bahasa Arab, kecepatan normal/lambat, transkrip tersembunyi, pertanyaan pemahaman.
- Qira'ah: teks berharakat, arti, pertanyaan pemahaman.
- Kalam: perekaman maksimal 45 detik, pemutaran ulang lokal, transkripsi audio, koreksi transkrip oleh AI.
- Kitabah: contoh, draft otomatis tersimpan, koreksi AI atau penyelesaian mandiri.
- Tutor AI bertema, level A1/A2, konteks percakapan terbatas, koreksi dan penjelasan bahasa Indonesia.
- Kosakata favorit dengan audio, progres lokal, ekspor JSON, penghapusan progres.
- UI tidak memberikan skor pelafalan dari transkrip.

## Audio dan data

Audio contoh menggunakan SpeechSynthesis perangkat, bukan audio penutur asli. Pasang suara Arab melalui pengaturan sistem jika belum tersedia; browser tidak selalu menyediakan suara Arab. Sebagai pengembangan pedagogis, ganti dengan audio penutur asli yang sudah diperiksa pengajar.

Rekaman berada di memori browser dan dikirim ke OpenAI hanya saat tombol transkripsi dipilih. Transkrip bisa diedit sebelum dikirim ke tutor. Teks latihan dan percakapan yang dikirim ke tutor diproses oleh OpenAI. Rekaman tidak disimpan dalam database. Progres, draft, dan kosakata menggunakan localStorage; kode akses menggunakan sessionStorage. Percakapan berada di memori halaman. Data tidak tersinkron antarperangkat. Ekspor progres tersedia sebagai cadangan JSON; impor belum disediakan.

## Struktur

- `public/index.html`, `style.css`, `app.js`: antarmuka dan interaksi.
- `public/lessons.js`: sumber materi; ubah di sini untuk menambah tema.
- `api/ai.js`: tutor, koreksi, dan transkripsi menggunakan API server-side.
- `api/status.js`: status konfigurasi tanpa membocorkan key.
- `scripts/dev-server.mjs`: server pengembangan dengan endpoint yang sama.
- `build.mjs`: menyalin aset ke dist.
- `tests/api.test.js`: validasi input, kode akses, dan pengujian kontrak dengan upstream simulasi.

Jika menambah tema, perbarui daftar tema dalam `api/ai.js` dan jumlah latihan pada dashboard `public/app.js`.

## Batas cakupan

Proyek ini aplikasi lengkap untuk belajar mandiri dengan penyimpanan lokal. Belum mencakup akun siswa, panel guru, database, analitik kelas, penilaian makhraj khusus, atau percakapan realtime. Tema awal tidak mencakup kursus huruf hijaiyah terpisah. Materi perlu ditinjau pengajar sebelum dipakai sebagai kurikulum resmi.

Kode akses merupakan pembatas sederhana, bukan autentikasi per pengguna atau rate limiter global. Untuk akses publik berskala besar, tambahkan autentikasi, kuota persisten per pengguna, dan pembatasan request. Atur budget pada akun API. Tidak ada rate limiter in-memory yang diklaim persisten di fungsi serverless.

## Verifikasi

`npm test`, `npm run build`, dan pemeriksaan sintaks lulus saat pengemasan. Respons AI dan transkripsi diuji dengan upstream simulasi. Pengujian layanan langsung memerlukan key; izin mikrofon, ketersediaan suara Arab, dan tampilan browser perlu diperiksa pada perangkat target setelah deployment. Belum dilakukan deployment ke akun GitHub/Vercel pengguna.

## Referensi integrasi

- https://platform.openai.com/docs/api-reference/chat/create
- https://platform.openai.com/docs/api-reference/audio/createTranscription
- https://vercel.com/docs/functions

## Troubleshooting

- AI belum aktif: isi OPENAI_API_KEY lalu redeploy.
- Kode tidak sesuai: masukkan AI_ACCESS_CODE di Pengaturan.
- API 429: periksa kuota, saldo, dan batas request akun API.
- Mikrofon ditolak: izinkan pada pengaturan situs dan gunakan HTTPS.
- Tidak ada suara Arab: pasang paket suara Arab pada sistem lalu muat ulang.
- Halaman 404: periksa Root Directory dan pastikan isi proyek berada di sana.

## Pembaruan deployment 1.0.1
ZIP perbaikan berisi file proyek langsung di root. Gunakan Framework Other, Node 22.x, Output dist. Hapus server.mjs lama dari root repository; server lokal dipindah ke scripts/dev-server.mjs. Jika 500 tetap muncul, buka deployment → Runtime Logs dan ambil stack trace; screenshot 500 saja belum menentukan penyebab.
