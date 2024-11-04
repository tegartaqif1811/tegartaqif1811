<!---
tegartaqif1811/tegartaqif1811 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
Nama : Tegar Taqif Yassar <br>
Nim : 21.01.55.0014 </br>

Deskripsi Project : Project ini mengenai Ujian Tengah Semester - Web Service Developmentent tentang Elektronics. Dengan REST API Development dengan PHP dan MySQL. Pada project ini tabel elektronik  akan berisi mengenai id (PK), name, brand, price, stock.

Alat yang Dibutuhkan
- XAMPP (atau server web lain dengan PHP dan MySQL)
- Text editor (misalnya Visual Studio Code, Notepad++, dll)
- Postman
  
### LANGKAH - LANGKAH 

### 1. Persiapan Lingkungan
- Aktifkan XAMPP .
- Buat folder baru bernama elektronik di dalam direktori htdocs XAMPP .

### 2. Membuat Database
- Buka phpMyAdmin (http://localhost/phpmyadmin)
- Buat database baru bernama elektronik
- Pilih database elektronik, lalu buka tab SQL
Jalankan query SQL berikut untuk membuat tabel dan menambahkan :

```sql
CREATE TABLE elektronik (
    id INT AUTO_INCREMENT PRIMARY KEY,
   name VARCHAR(30) NOT NULL,
    brand VARCHAR(30) NOT NULL,
    price INT NOT NULL,
    stock INT NOT NULL
);

INSERT INTO elektronik (name, brand, price, stock ) VALUES
('tv', 'Samsung', '300000', '15'),
('kulkas', 'polytron', '500000','10'),
('setrika', 'cosmos', '150000','20'),
('radio', 'panasonic', '120000','25'),
('kipas angin', 'sogo', '120000', '15');
```
### 3. Membuat File PHP untuk Web Service
- Buka text editor .
- Buat file baru dan simpan sebagai elektronik.php di dalam folder elektronik.
- Salin dan tempel kode berikut ke dalam book_api.php:

```php 
<?php
header("Content-Type: application/json; charset=UTF-8");
header("Access-Control-Allow-Origin: *");
header("Access-Control-Allow-Methods: GET, POST, PUT, DELETE");
header("Access-Control-Allow-Headers: Content-Type, Access-Control-Allow-Headers, brandization, X-Requested-With");

$method = $_SERVER['REQUEST_METHOD'];
$request = [];

if (isset($_SERVER['PATH_INFO'])) {
    $request = explode('/', trim($_SERVER['PATH_INFO'],'/'));
}

function getConnection() {
    $host = 'localhost';
    $db   = 'electronics';
    $user = 'root';
    $pass = ''; // Ganti dengan password MySQL Anda jika ada
    $charset = 'utf8mb4';

    $dsn = "mysql:host=$host;dbname=$db;charset=$charset";
    $options = [
        PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
        PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
        PDO::ATTR_EMULATE_PREPARES   => false,
    ];
    try {
        return new PDO($dsn, $user, $pass, $options);
    } catch (\PDOException $e) {
        throw new \PDOException($e->getMessage(), (int)$e->getCode());
    }
}

function response($status, $data = NULL) {
    header("HTTP/1.1 " . $status);
    if ($data) {
        echo json_encode($data);
    }
    exit();
}

$db = getConnection();

switch ($method) {
    case 'GET':
        if (!empty($request) && isset($request[0])) {
            $id = $request[0];
            $stmt = $db->prepare("SELECT * FROM elektronik WHERE id = ?");
            $stmt->execute([$id]);
            $elektronik = $stmt->fetch();
            if ($elektronik) {
                response(200, $elektronik);
            } else {
                response(404, ["message" => "elektronik not found"]);
            }
        } else {
            $stmt = $db->query("SELECT * FROM elektronik");
            $elektronik = $stmt->fetchAll();
            response(200, $elektronik);
        }
        break;
    
    case 'POST':
        $data = json_decode(file_get_contents("php://input"));
        if (!isset($data->name) || !isset($data->brand) || !isset($data->price) || !isset($data->stock)) {
            response(400, ["message" => "Missing required fields"]);
        }
        $sql = "INSERT INTO elektronik (name, brand, price,stock) VALUES (?, ?, ?, ?)";
        $stmt = $db->prepare($sql);
        if ($stmt->execute([$data->name, $data->brand,$data->price, $data->stock])) {
            response(201, ["message" => "elektronik created", "id" => $db->lastInsertId()]);
        } else {
            response(500, ["message" => "Failed to create elektronik"]);
        }
        break;
    
    case 'PUT':
        if (empty($request) || !isset($request[0])) {
            response(400, ["message" => "elektronik ID is required"]);
        }
        $id = $request[0];
        $data = json_decode(file_get_contents("php://input"));
        if (!isset($data->name) || !isset($data->brand) || !isset($data->price) || !isset($data->stock)) {
            response(400, ["message" => "Missing required fields"]);
        }
        $sql = "UPDATE elektronik SET name = ?, brand = ?,price = ? ,stock = ? WHERE id = ?";
        $stmt = $db->prepare($sql);
        if ($stmt->execute([$data->name, $data->brand,$data->price, $data->stock, $id])) {
            response(200, ["message" => "elektronik updated"]);
        } else {
            response(500, ["message" => "Failed to update elektronik"]);
        }
        break;
    
    case 'DELETE':
        if (empty($request) || !isset($request[0])) {
            response(400, ["message" => "elektronik ID is required"]);
        }
        $id = $request[0];
        $sql = "DELETE FROM elektronik WHERE id = ?";
        $stmt = $db->prepare($sql);
        if ($stmt->execute([$id])) {
            response(200, ["message" => "elektronik deleted"]);
        } else {
            response(500, ["message" => "Failed to delete elektronik"]);
        }
        break;
    
    default:
        response(405, ["message" => "Method not allowed"]);
        break;
}
?>
```
### 

### 4. Pengujian dengan Postman
Buka Postman
Buat request baru untuk setiap operasi berikut:
### a. GET All Books
- Method: GET
- URL: http://localhost/elektronik/elektronik.php
- Klik "Send"
### b. GET Specific Book
- Method: GET
- URL: URL: http://localhost/elektronik/elektronik.php/3 (untuk buku dengan ID 3)
- Klik "Send"
### c. POST New Book
- Method: POST
- URL: http://localhost/elektronik/elektronik.php
- Headers:
Key: Content-Type
Value: application/json
- Body:
- Pilih "raw" dan "JSON"
- Masukkan :

```sql
{
    "name": "ac",
    "brand": "lg",
    "price": 200000,
    "stock": 15
}
```

- Klik "Send"
### d. PUT (Update) Book
- Method: PUT
- URL: (http://localhost/elektronik/elektronik.php/5) (asumsikan ID buku baru adalah 5)
- Headers:
Key: Content-Type
Value: application/json
- Body:
- Pilih "raw" dan "JSON"
- Masukkan:
  
```sql
  {
    "name": "ac",
    "brand": "lg",
    "price": 4000000,
    "stock": 20
  ```

- Klik "Send"
  
### e. DELETE Book
- Method: DELETE
- URL: (http://localhost/elektronik/elektronik.php/5) (untuk menghapus buku dengan ID 5)
- Klik "Send"
}
