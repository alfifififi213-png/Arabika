# Arabika 1.2 — Login lampu dan dashboard baru

Baca ini sebelum mengganti deployment yang sudah berjalan.

## Yang berubah

- Layar masuk bertema lampu: klik tali lampu atau tombol Langsung masuk untuk menampilkan formulir.
- Daftar akun, login email/kata sandi, tampilkan kata sandi, lupa kata sandi, ganti kata sandi melalui email, dan logout.
- Dashboard memakai #202734, #36415A, #B7D894, #E0F8C7.
- Cookie sesi HttpOnly dan SameSite; Secure pada Vercel/HTTPS. Sesi diverifikasi pada layanan Supabase, termasuk sebelum memanggil AI.
- Kata sandi diserahkan ke Supabase Auth; tidak ditulis ke file atau localStorage.
- Progres tetap di browser, kini terpisah menurut ID akun. Belum ada sinkronisasi progres ke database.

## 1. Siapkan Supabase

1. Masuk https://supabase.com/dashboard dan buat proyek.
2. Pada pengaturan proyek, salin Project URL dan publishable key. Jika proyek hanya menyediakan legacy key, gunakan anon key sebagai nilai SUPABASE_PUBLISHABLE_KEY.
3. Aktifkan provider Email di Authentication. Gunakan konfirmasi email untuk pendaftaran.
4. Pada Authentication → URL Configuration, isi Site URL dengan URL produksi Arabika, misalnya https://arabika-anda.vercel.app.
5. Tambahkan URL yang sama dengan akhiran / pada daftar Redirect URLs. Untuk pengembangan lokal, tambahkan http://localhost:3000/.
6. Atur pengiriman email/SMTP untuk konfirmasi dan pemulihan. Pengirim bawaan Supabase memiliki pembatasan; untuk pengguna umum siapkan SMTP Anda. Tidak perlu membuat tabel database untuk versi ini.
7. Atur kebijakan password minimal 8 karakter di Supabase, sesuai validasi form.

## 2. Tambahkan variabel di proyek Vercel yang sama

SUPABASE_URL = Project URL Supabase
SUPABASE_PUBLISHABLE_KEY = publishable key atau legacy anon key
AUTH_ENABLED = true

Pertahankan OPENAI_API_KEY, OPENAI_MODEL, OPENAI_TRANSCRIBE_MODEL, dan AI_ACCESS_CODE yang sudah ada. Jangan menggunakan Supabase service_role/secret key. Jangan unggah file .env.local ke GitHub.

Pilih Production; tambahkan Preview jika Anda juga menguji deployment Preview. URL Preview yang digunakan untuk email callback perlu ditambahkan pada Redirect URLs Supabase.

## 3. Perbarui repository

1. Cadangkan repository atau buat branch sebelum pembaruan. ZIP ini memuat BACKUP/Arabika-sebelum-login.zip dari versi lokal sebelumnya.
2. Upload isi proyek dari ZIP ini ke root repository yang sudah terhubung ke Vercel. File package.json dan vercel.json langsung berada pada root yang dipilih Vercel.
3. Jangan hanya mengganti CSS: login memerlukan file API, lib, boot, dan auth sekaligus.
4. Pastikan server.mjs lama tidak tersisa di root. Server lokal tetap di scripts/dev-server.mjs.
5. Commit lalu deploy. Framework Other, Node 22.x, Build npm run build, Output dist.
6. Jika baru menyimpan variabel, redeploy setelah penyimpanan selesai.

Tidak ada perubahan pada model/API key OpenAI. Tanpa konfigurasi Supabase, form memberi keterangan login belum dikonfigurasi dan tidak berpura-pura mengautentikasi pengguna.

## 4. Uji setelah deployment

- Klik tali lampu: formulir muncul. Tombol Langsung masuk juga membuka formulir.
- Daftar menggunakan email yang Anda miliki; ikuti konfirmasi email.
- Masuk; dashboard muncul dengan identitas akun dan tombol Keluar.
- Isi satu latihan, logout, lalu masuk kembali di browser yang sama: progres akun dipertahankan.
- Akun lain menggunakan penyimpanan lokal berbeda.
- Uji lupa kata sandi dan tautan dari email. Form kata sandi baru muncul setelah callback recovery.
- Uji tutor setelah login. AI masih memerlukan billing API aktif.

## Progres lama

Data lama pada key localStorage 'arabika' tidak dihapus. Progres akun baru dimulai terpisah agar data tidak otomatis diklaim oleh akun lain pada perangkat bersama. Untuk mengakses/mengekspor progres lama, gunakan mode pemulihan sementara di bawah pada browser dan domain yang sama. Impor dan sinkronisasi belum disediakan.

## Pemulihan

Jika login belum siap tetapi Anda perlu mengembalikan akses website seperti sebelumnya:
- Ubah AUTH_ENABLED menjadi false di Vercel lalu redeploy. Ini sengaja menonaktifkan persyaratan login, termasuk untuk API AI; AI_ACCESS_CODE tetap berlaku bila disetel. Aktifkan kembali setelah konfigurasi akun selesai.
- Atau revert commit pembaruan di GitHub untuk kembali ke versi sebelumnya. BACKUP/Arabika-sebelum-login.zip juga tersedia.

Jangan menghapus environment variable OpenAI yang sudah berfungsi.

## Cakupan keamanan

Halaman statis dan materi latihan bukan dokumen rahasia: file statis tetap dapat diunduh. Akses dashboard normal melewati pemeriksaan sesi, dan semua permintaan AI diperiksa oleh server. Login ini tidak mengubah materi statis menjadi konten berbayar terlindungi. Kuota AI per akun dan rate limiter persisten belum ditambahkan; pembatasan Supabase dan AI_ACCESS_CODE yang ada tetap digunakan.

## Verifikasi sebelum pengemasan

Build, pemeriksaan sintaks, dan 4 pengujian otomatis lulus: validasi API, kode akses, origin, login, sesi, refresh, logout, penolakan akses AI tanpa login. Pengujian Supabase/OpenAI memakai respons simulasi. Login/email langsung memerlukan proyek Anda. Pratinjau visual browser belum dapat diverifikasi karena browser tidak dapat menjangkau server lokal pada lingkungan kerja ini. Periksa tampilan ponsel dan desktop setelah deployment Preview sebelum mempromosikan ke produksi.

## Referensi

https://supabase.com/docs/guides/auth/passwords
https://supabase.com/docs/reference/javascript/auth-getuser
https://supabase.com/docs/reference/javascript/auth-refreshsession
