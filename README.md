Nama : Navyta Budi Yulia

NIM  : 312410184

Kelas : TI.24.A2

# Lab12Web

## Langkah 1
- masi di folder dan database yang sama `Lab11_php_oop`
- Buat tabell baru `Users`
```
   CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    password VARCHAR(255) NOT NULL,
    nama VARCHAR(100)
);
```

<img width="1920" height="1008" alt="Image" src="https://github.com/user-attachments/assets/378f8c7d-8a84-4f0b-ab95-b59d38a2716d" />

<img width="1920" height="1008" alt="Image" src="https://github.com/user-attachments/assets/28177099-e2fe-459f-9ad5-523b625cd6ef" />

- Penjelasan kolom tabel:

  - id → Primary key, otomatis bertambah

  - username → Username untuk login

  - password → Password yang sudah di-hash

  - nama → Nama lengkap user
 
## Langkah 2

- Insert Data Dummy (User Admin)
  
```
INSERT INTO users (username, password, nama)
VALUES ('admin', '$2y$10$uWdZ2x.hQfGqGz/..q7wue.3/a/e/e/e/e/e/e/e/e/e/e',
'Administrator');
```

<img width="1920" height="1008" alt="Image" src="https://github.com/user-attachments/assets/074fa375-e8f8-407e-9129-5f8a864148db" />

<img width="1920" height="1008" alt="Image" src="https://github.com/user-attachments/assets/d3630f39-9339-45b7-850c-50669dbaf672" />

## Langkah 3

-  Update Routing (index.php)

```
<?php
session_start();

include "config.php";
include "class/Database.php";
include "class/Form.php";

// Ambil path URL
$path = $_SERVER['PATH_INFO'] ?? '/home/index';
$segments = explode('/', trim($path, '/'));

$mod  = $segments[0] ?? 'home';
$page = $segments[1] ?? 'index';

// PROTEKSI LOGIN
$public_pages = ['home', 'user'];
if (!in_array($mod, $public_pages)) {
    if (!isset($_SESSION['is_login'])) {
        header('Location: /lab11_php_oop/user/login');
        exit();
    }
}

// Tentukan file modul
$file = "module/$mod/$page.php";

// Load header
include "template/header.php";

// Load halaman
if (file_exists($file)) {
    include $file;
} else {
    echo "<h3>Halaman tidak ditemukan: $mod/$page</h3>";
}

// Load footer
include "template/footer.php";
```

## Langkah 4
- Membuat Modul User (Login & Logout)
- File `login`
  
```
<?php
// JANGAN pakai session_start() di sini
// session sudah dimulai di index.php

$message = '';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $db = new Database();

    $username = trim($_POST['username']);
    $password = trim($_POST['password']);

    // Query pakai method Database (OOP)
    $sql = "SELECT * FROM users WHERE username = '$username' LIMIT 1";
    $result = $db->query($sql);

    if ($result && $result->num_rows === 1) {
        $user = $result->fetch_assoc();

        if (password_verify($password, $user['password'])) {
            $_SESSION['is_login'] = true;
            $_SESSION['id'] = $user['id'];
            $_SESSION['username'] = $user['username'];
            $_SESSION['nama'] = $user['nama'];

            header('Location: /lab11_php_oop/artikel/index');
            exit;
        }
    }

    $message = 'Username atau password salah!';
}
?>

<h3>Login</h3>

<?php if ($message): ?>
    <p style="color:red"><?= $message ?></p>
<?php endif; ?>

<form method="post">
    <label>Username</label><br>
    <input type="text" name="username" required><br><br>

    <label>Password</label><br>
    <input type="password" name="password" required><br><br>

    <button type="submit">Login</button>
</form>
```
- File `logout`

```
<?php
session_start();      // pastikan session dimulai
session_unset();      // hapus semua variabel session
session_destroy();    // hancurkan session
header('Location: ../user/login');  // redirect ke halaman login
exit;
```

## Langkah 5
- Penyesuaian Tampilan (Header)

```
<?php
// Jangan panggil session_start() lagi di sini, sudah ada di index.php root
?>
<!DOCTYPE html>
<html>
<head>
    <title>Framework Modular - Praktikum 11</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            background: #f5f5f5;
        }

        header {
            background: #4CAF50;
            padding: 20px;
            color: white;
            font-size: 24px;
            font-weight: bold;
            text-align: center;
        }

        nav {
            background: #388E3C;
            padding: 10px 40px;
        }

        nav a {
            color: white;
            text-decoration: none;
            margin-right: 15px;
            font-weight: bold;
        }

        nav a:hover {
            text-decoration: underline;
        }

        nav .right {
            float: right;
        }

        footer {
            background: #222;
            color: #ddd;
            text-align: center;
            padding: 15px;
            margin-top: 30px;
        }

        .container {
            padding: 20px 40px;
        }

        a.btn {
            display: inline-block;
            background: #2196F3;
            padding: 8px 14px;
            color: white;
            border-radius: 5px;
            text-decoration: none;
            margin-bottom: 12px;
        }

        a.btn:hover {
            background: #0b7dda;
        }

        table {
            background: white;
            border-collapse: collapse;
            width: 100%;
            margin-top: 10px;
            border-radius: 5px;
            overflow: hidden;
        }

        table th {
            background: #4CAF50;
            color: white;
            padding: 10px;
        }

        table td {
            padding: 10px;
            border-bottom: 1px solid #ddd;
        }

        .title-page {
            font-size: 22px;
            font-weight: bold;
            margin-bottom: 10px;
        }

        input[type=text], textarea {
            width: 50%;
            padding: 8px;
            border: 1px solid #999;
            border-radius: 4px;
        }

        input[type=submit] {
            background: #4CAF50;
            color: white;
            padding: 8px 16px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }

        input[type=submit]:hover {
            background: #45a049;
        }
    </style>
</head>
<body>

<header>
    ✨ Sistem Artikel - Praktikum 11 PHP OOP ✨
</header>

<nav>
    <a href="../home/index">Home</a>

    <?php if (isset($_SESSION['is_login']) && $_SESSION['is_login'] === true): ?>
        <a href="../artikel/index">Artikel</a>
        <a href="../user/profil">Profil</a>
        <a href="../user/logout" class="right">
            Logout (<?= htmlspecialchars($_SESSION['nama']); ?>)
        </a>
    <?php else: ?>
        <a href="../user/login" class="right">Login</a>
    <?php endif; ?>
    <div style="clear:both;"></div>
</nav>

<div class="container">
```

# LANGKAH UJI COBA

- Akses Halaman Artikel (Tanpa Login):
Buka browser: http://localhost/lab11_php_oop/artikel/index.
Hasil: Kamu seharusnya otomatis terlempar (redirect) ke halaman user/login.

<img width="1920" height="1008" alt="Image" src="https://github.com/user-attachments/assets/e5d9a64c-8928-4123-b4e9-67ce7d0168e5" />

<img width="1920" height="1008" alt="Image" src="https://github.com/user-attachments/assets/5b30e49b-3d3d-49e9-8d26-3331115daf46" />
