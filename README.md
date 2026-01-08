# CompLearn (local)

Instruksi singkat menjalankan dan menguji aplikasi (lokal XAMPP).

## Setup Database
1. Jalankan XAMPP (Apache + MySQL).
2. Import database `wifa_siswa_db_new.sql` lewat phpMyAdmin atau jalankan SQL dump.
3. Perbarui koneksi DB di `config.php` jika diperlukan (default: root, password kosong, DB: wifa_siswa_db_new).

## Membuat Akun Admin
1. Buka di browser: `http://localhost/website/create_admin.php`
2. Script ini membuat tabel `admin` dan akun admin contoh (email: admin@localhost, password: admin123).
3. **HAPUS** file `create_admin.php` setelah berhasil untuk keamanan.

## Alur Login & Registrasi
- **Halaman Utama Login**: `login.php` - Pilih login sebagai Siswa atau Admin, dengan opsi daftar.
- **Login Siswa**: `login_siswa.php` - Form login siswa.
- **Login Admin**: `admin_login.php` - Form login admin.
- **Registrasi Siswa**: `register.php` - Form daftar siswa (admin tidak bisa daftar lewat UI).
- **Dashboard Siswa**: `dashboard.php` - Setelah login siswa.
- **Dashboard Admin**: `admin_dashboard.php` - Setelah login admin.

## Perubahan Terbaru
- Tampilan diperbaiki dengan gradient background, animasi fade-in, dan hover effects.
- Login dipisah: halaman pilihan role di `login.php`, form spesifik di `login_siswa.php` dan `admin_login.php`.
- Registrasi siswa di `register.php` dengan link ke daftar admin (alert bahwa admin dibuat oleh admin).
- Session aman: regenerasi ID saat login, logout membersihkan cookie.
- Validasi: cek status aktif untuk siswa, prepared statements untuk keamanan.
- Database baru: `wifa_siswa_db_new.sql` dengan tabel lengkap (siswa, admin, materi, progress_siswa).

## Keamanan & Rekomendasi
- Gunakan HTTPS di production.
- Ganti password default admin setelah pembuatan.
- Hapus `create_admin.php` segera setelah digunakan.
- Pertimbangkan menambahkan rate-limiting dan CSRF tokens untuk form.

## File yang Dihapus/Ditambah
- **Ditambah**: `login_siswa.php`, `wifa_siswa_db_new.sql`
- **Diubah**: `login.php` (halaman pilihan), `admin_login.php`, `admin_dashboard.php`, `register.php`
- **Hapus setelah setup**: `create_admin.php`

Jika Anda ingin fitur tambahan seperti:
- UI manajemen siswa/admin melalui dashboard admin.
- Upload materi atau manajemen konten.
- Sistem progress pembelajaran yang lebih lengkap.
- Notifikasi atau email.

Beri tahu saya!
