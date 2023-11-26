# Lab8web

## Membuat data base

![image](https://github.com/muhamadabdulanas/Lab8web/assets/115569493/d3eee5d5-2570-4d8d-9e5c-cb6f7a0f6a5d)

## membuat file koneksi php

<?php
    $host = "localhost";
    $user = "root";
    $pass = "";
    $db = "studi_barang";
    $conn = mysqli_connect($host, $user, $pass, $db);
    if ($conn == false)
    {
    echo "Koneksi ke server gagal.";
    die();
    } else echo "Koneksi berhasil";
?>

![image](https://github.com/muhamadabdulanas/Lab8web/assets/115569493/f7cf24d9-7d34-4da5-9292-5f7ebbbd0831)

## membuat file index php

<?php
include("koneksi.php");
// query untuk menampilkan data
$sql = 'SELECT * FROM data_barang';
$result = mysqli_query($conn, $sql);
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Data Barang</title>

</head>
<body>
    <div class="container">
        <h1>Data Barang</h1>
        <a href="plus.php"> Tambah Barang</a>
        <div class="main">
            <table>
            <tr>
                <th>Gambar</th>
                <th>Nama Barang</th>
                <th>Katagori</th>
                <th>Harga Jual</th>
                <th>Harga Beli</th>
                <th>Stok</th>
                <th>Aksi</th>
            </tr>
            <?php if($result): ?>
            <?php while($row = mysqli_fetch_array($result)): ?>
            <tr>
                <td><img src="gambar/<?= $row['gambar'];?>" alt="<?=$row['nama'];?>"></td>
                <td><?= $row['nama'];?></td>
                <td><?= $row['kategori'];?></td>
                <td><?= $row['harga_beli'];?></td>
                <td><?= $row['harga_jual'];?></td>
                <td><?= $row['stok'];?></td>
                <td><a href="ubah.php?id=<?= $row['id_barang']; ?>">ubah</a>
                <a href="hapus.php?id=<?= $row['id_barang']; ?>">hapus</a></td>
            </td>
            </tr>
            <?php endwhile; else: ?>
            <tr>
                <td colspan="1">Belum ada data</td>
            </tr>
            <?php endif; ?>
            </table>
        </div>
    </div>
</body>
</html>


![image](https://github.com/muhamadabdulanas/Lab8web/assets/115569493/3935c26e-fd24-4aa9-a387-0f0dfec07471)

## membuat file plus.php

<?php
error_reporting(E_ALL);

include_once 'koneksi.php';

if (isset($_POST['submit'])) {
    $nama = htmlspecialchars($_POST['nama']);
    $kategori = htmlspecialchars($_POST['kategori']);
    $harga_jual = floatval($_POST['harga_jual']);
    $harga_beli = floatval($_POST['harga_beli']);
    $stok = intval($_POST['stok']);
    $file_gambar = $_FILES['file_gambar'];
    $gambar = null;

    if ($file_gambar['error'] == 0) {
        $filename = str_replace(' ', '_', $file_gambar['name']);
        $destination = 'gambar/' . $filename;

        if (move_uploaded_file($file_gambar['tmp_name'], $destination)) {
            $gambar = 'gambar/' . $filename;
        } else {
            echo "Gagal mengunggah file gambar.";
            exit;
        }
    }

    $sql = 'INSERT INTO data_barang (nama, kategori, harga_jual, harga_beli, stok, gambar) ';
    $sql .= 'VALUES (?, ?, ?, ?, ?, ?)';

    $stmt = mysqli_prepare($conn, $sql);

    if ($stmt) {
        mysqli_stmt_bind_param($stmt, 'ssddis', $nama, $kategori, $harga_jual, $harga_beli, $stok, $gambar);

        $result = mysqli_stmt_execute($stmt);

        if ($result) {
            echo "Data berhasil ditambahkan.";
        } else {
            echo "Gagal mengeksekusi pernyataan SQL: " . mysqli_stmt_error($stmt);
        }

        mysqli_stmt_close($stmt);
    } else {
        echo "Gagal membuat prepared statement: " . mysqli_error($conn);
    }

    mysqli_close($conn);
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css">
    <title>Tambah Barang</title>
</head>
<body>
    <div class="container">
        <h1>Tambah Barang</h1>
        <div class="main">
            <form method="post" action="plus.php" enctype="multipart/form-data">
                <div class="input">
                    <label for="nama">Nama Barang</label>
                    <input type="text" name="nama" id="nama" required>
                </div>
                <div class="input">
                    <label for="kategori">Kategori</label>
                    <select name="kategori" id="kategori" required>
                        <option value="Komputer">Komputer</option>
                        <option value="Elektronik">Elektronik</option>
                        <option value="Hand Phone">Hand Phone</option>
                    </select>
                </div>
                <div class="input">
                    <label for="harga_jual">Harga Jual</label>
                    <input type="text" name="harga_jual" id="harga_jual" required>
                </div>
                <div class="input">
                    <label for="harga_beli">Harga Beli</label>
                    <input type="text" name="harga_beli" id="harga_beli" required>
                </div>
                <div class="input">
                    <label for="stok">Stok</label>
                    <input type="text" name="stok" id="stok" required>
                </div>
                <div class="input">
                    <label for="file_gambar">File Gambar</label>
                    <input type="file" name="file_gambar" id="file_gambar" accept="image/*" required>
                </div>
                <div class="submit">
                    <input type="submit" name="submit" value="Simpan">
                </div>
            </form>
        </div>
    </div>
</body>
</html>

### ouput

![image](https://github.com/muhamadabdulanas/Lab8web/assets/115569493/5072e37f-0a37-44eb-9a3c-6599142df5e3)

## membuat file ubah.php

![image](https://github.com/muhamadabdulanas/Lab8web/assets/115569493/f7d20462-eb9f-41ef-a66e-d9b85e2f3db9)
