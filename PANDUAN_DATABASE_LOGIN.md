# Panduan Membuat Database untuk Login Siswa

## LANGKAH 1: Persiapan Database

### 1.1 Buat Database Baru
```sql
CREATE DATABASE IF NOT EXISTS wifa_siswa_db;
USE wifa_siswa_db;
```

### 1.2 Buat Tabel Siswa
```sql
CREATE TABLE siswa (
    id_siswa INT AUTO_INCREMENT PRIMARY KEY,
    nim VARCHAR(20) UNIQUE NOT NULL,
    nama_lengkap VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    kelas VARCHAR(50),
    nomor_hp VARCHAR(15),
    tanggal_daftar TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status ENUM('aktif', 'nonaktif') DEFAULT 'aktif'
);
```

### 1.3 Buat Tabel Materi Pembelajaran
```sql
CREATE TABLE materi (
    id_materi INT AUTO_INCREMENT PRIMARY KEY,
    judul_materi VARCHAR(150) NOT NULL,
    deskripsi TEXT,
    tipe_konten ENUM('video', 'h5p', 'dokumen') DEFAULT 'video',
    url_konten VARCHAR(500),
    kelas VARCHAR(50),
    urutan INT,
    tanggal_dibuat TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 1.4 Buat Tabel Progress Siswa
```sql
CREATE TABLE progress_siswa (
    id_progress INT AUTO_INCREMENT PRIMARY KEY,
    id_siswa INT NOT NULL,
    id_materi INT NOT NULL,
    tanggal_akses TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status_selesai BOOLEAN DEFAULT FALSE,
    skor INT,
    FOREIGN KEY (id_siswa) REFERENCES siswa(id_siswa),
    FOREIGN KEY (id_materi) REFERENCES materi(id_materi)
);
```

---

## LANGKAH 2: Persiapan File Backend PHP

### 2.1 File: `config.php` (Koneksi Database)
Letakkan di folder root website

```php
<?php
define('DB_HOST', 'localhost');
define('DB_USER', 'root');
define('DB_PASS', ''); // ubah sesuai password MySQL Anda
define('DB_NAME', 'wifa_siswa_db');

$conn = new mysqli(DB_HOST, DB_USER, DB_PASS, DB_NAME);

if ($conn->connect_error) {
    die("Koneksi gagal: " . $conn->connect_error);
}

$conn->set_charset("utf8");
?>
```

### 2.2 File: `register.php` (Registrasi Siswa)

```php
<?php
session_start();
require 'config.php';

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $nim = mysqli_real_escape_string($conn, $_POST['nim']);
    $nama = mysqli_real_escape_string($conn, $_POST['nama_lengkap']);
    $email = mysqli_real_escape_string($conn, $_POST['email']);
    $password = password_hash($_POST['password'], PASSWORD_DEFAULT);
    $kelas = mysqli_real_escape_string($conn, $_POST['kelas']);

    $check = $conn->query("SELECT * FROM siswa WHERE email='$email' OR nim='$nim'");
    
    if ($check->num_rows > 0) {
        $error = "Email atau NIM sudah terdaftar!";
    } else {
        $insert = $conn->query("INSERT INTO siswa (nim, nama_lengkap, email, password, kelas) 
                               VALUES ('$nim', '$nama', '$email', '$password', '$kelas')");
        
        if ($insert) {
            $_SESSION['success'] = "Registrasi berhasil! Silakan login.";
            header('Location: login.php');
            exit();
        } else {
            $error = "Error: " . $conn->error;
        }
    }
}
?>
```

### 2.3 File: `process_login.php` (Proses Login)

```php
<?php
session_start();
require 'config.php';

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $email = mysqli_real_escape_string($conn, $_POST['email']);
    $password = $_POST['password'];

    $query = $conn->query("SELECT * FROM siswa WHERE email='$email'");
    
    if ($query->num_rows == 1) {
        $row = $query->fetch_assoc();
        
        if (password_verify($password, $row['password'])) {
            $_SESSION['id_siswa'] = $row['id_siswa'];
            $_SESSION['nama_siswa'] = $row['nama_lengkap'];
            $_SESSION['email'] = $row['email'];
            $_SESSION['kelas'] = $row['kelas'];
            
            header('Location: dashboard.php');
            exit();
        } else {
            $error = "Password salah!";
        }
    } else {
        $error = "Email tidak ditemukan!";
    }
}
?>
```

### 2.4 File: `logout.php` (Logout)

```php
<?php
session_start();
session_destroy();
header('Location: login.php');
exit();
?>
```

---

## LANGKAH 3: Update File HTML Login

### 3.1 Update `login.html` untuk Menggunakan PHP

Ubah form action dari `#` menjadi `process_login.php`:

```html
<form method="POST" action="process_login.php">
    <div class="input-box">
        <input type="email" name="email" placeholder="Email" required>
    </div>
    <div class="input-box">
        <input type="password" name="password" placeholder="Password" required>
    </div>
    <button type="submit">Masuk</button>
</form>

<p>Belum punya akun? <a href="register.html">Daftar di sini</a></p>
```

---

## LANGKAH 4: Buat File Registrasi HTML

### 4.1 File: `register.html`

```html
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Registrasi - CompLearn</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Poppins', sans-serif;
        }

        body {
            background: #2957D1;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        .card {
            width: 100%;
            max-width: 450px;
            background: white;
            padding: 40px 35px;
            border-radius: 10px;
            box-shadow: 0px 4px 20px rgba(0,0,0,0.1);
        }

        .card h2 {
            font-size: 22px;
            font-weight: 600;
            margin-bottom: 5px;
            color: #111;
            text-align: center;
        }

        .card p {
            font-size: 14px;
            color: #555;
            margin-bottom: 25px;
            text-align: center;
        }

        .input-box {
            width: 100%;
            margin-bottom: 15px;
        }

        .input-box input {
            width: 100%;
            border: none;
            border-bottom: 2px solid #ddd;
            padding: 10px 0;
            font-size: 16px;
            outline: none;
            transition: 0.3s;
        }

        .input-box input:focus {
            border-bottom-color: #2957D1;
        }

        button {
            width: 100%;
            padding: 12px;
            background: #2957D1;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 16px;
            cursor: pointer;
            margin-top: 20px;
            transition: 0.3s;
        }

        button:hover {
            background: #1d3fa3;
        }

        .link {
            text-align: center;
            margin-top: 15px;
            font-size: 14px;
        }

        .link a {
            color: #2957D1;
            text-decoration: none;
        }

        .error {
            color: red;
            text-align: center;
            margin-bottom: 15px;
        }
    </style>
</head>
<body>
    <div class="card">
        <h2>Daftar Akun</h2>
        <p>Buat akun CompLearn Anda</p>

        <?php if(isset($error)) echo "<p class='error'>$error</p>"; ?>

        <form method="POST" action="register.php">
            <div class="input-box">
                <input type="text" name="nim" placeholder="NIM" required>
            </div>
            <div class="input-box">
                <input type="text" name="nama_lengkap" placeholder="Nama Lengkap" required>
            </div>
            <div class="input-box">
                <input type="email" name="email" placeholder="Email" required>
            </div>
            <div class="input-box">
                <input type="password" name="password" placeholder="Password" required>
            </div>
            <div class="input-box">
                <input type="text" name="kelas" placeholder="Kelas (contoh: 12-A)" required>
            </div>
            <button type="submit">Daftar</button>
        </form>

        <div class="link">
            Sudah punya akun? <a href="login.php">Login di sini</a>
        </div>
    </div>
</body>
</html>
```

---

## LANGKAH 5: Implementasi di Server

1. **Pastikan Apache & MySQL Running** (Gunakan XAMPP, WAMP, atau Laragon)
2. **Copy file config.php, register.php, process_login.php ke folder website**
3. **Rename login.html menjadi login.php dan update form action**
4. **Import database dengan menjalankan SQL queries di phpMyAdmin**
5. **Test login di `http://localhost/website/login.php`**

---

## LANGKAH 6: Keamanan Database

- ✅ Selalu gunakan password_hash() untuk password
- ✅ Gunakan prepared statements (mysqli_prepare)
- ✅ Validasi input dari user
- ✅ Simpan session dengan aman
- ✅ Gunakan HTTPS di production

---

**Selamat! Database login siswa Anda sudah siap digunakan! 🎉**
