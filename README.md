# Arabika 1.2

Pembelajaran bahasa Arab fusha: enam tema, empat kemahiran, tutor AI, perekam mikrofon, dan login email dengan animasi lampu.

**Mulai dari PANDUAN-LOGIN.md** sebelum memperbarui situs yang sudah berjalan. Autentikasi memerlukan Supabase Auth; AI memerlukan OpenAI API. Progres disimpan lokal per akun, belum disinkron antarperangkat.

## Lokal

Node.js 22.x. Tanpa dependency aplikasi.

```sh
cp .env.example .env.local
# isi variabel sesuai PANDUAN-LOGIN.md
npm run dev
```

Buka http://localhost:3000. Jangan membuka HTML melalui file://.

```sh
npm test
npm run build
```

## Vercel

Framework Other; Build npm run build; Output dist; Node 22.x. Upload isi proyek langsung ke root repository. Simpan kredensial pada Environment Variables Vercel dan redeploy. Daftar variabel pada .env.example.

## Berkas

public/boot.js dan public/auth.js: alur masuk dan pengaktifan aplikasi.
public/login.css: animasi lampu dan form responsif.
public/style.css dan public/app.js: dashboard dan latihan.
public/lessons.js: materi.
api/auth.js dan lib/auth.js: autentikasi server, cookie sesi, refresh.
api/ai.js: tutor/koreksi/transkripsi, wajib sesi bila AUTH_ENABLED=true.
api/status.js: status konfigurasi OpenAI tanpa key.
scripts/dev-server.mjs: server lokal; bukan entrypoint hosting.
BACKUP/Arabika-sebelum-login.zip: salinan sumber sebelum perubahan login.

Materi audio memakai suara Arab sintetis perangkat. Perangkat perlu menyediakan suara Arab. Rekaman maksimal 45 detik; dikirim ke OpenAI ketika transkripsi dipilih. Tutor menerima teks, tidak memberikan penilaian makhraj. Konfirmasi/pemulihan email dikirim melalui konfigurasi Supabase Anda.
