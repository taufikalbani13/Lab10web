
| Nama      | Taufik eka albani |
| --------- | -----------                  |
| NIM       | 312210347                    |
| Kelas     | TI.22.A.3                    |
# Lab10web
## Instruksi Praktikum
1. Persiapkan text editor misalnya VSCode.
2. Buat folder baru dengan nama lab10_php_oop pada docroot webserver(htdocs)
3. Ikuti langkah-langkah praktikum
## Jalankan XAMPP dan akses ```http://localhost/phpmyadmin/``` untuk membuat database.
![Screenshot 2024-01-15 111925](https://github.com/taufikalbani13/Lab1web/assets/115517181/96674f7d-ff32-4594-b908-9eeb7c987373)
## Buat file ```lab10_php_oop``` pada root directory web server ```(c:\xampp\htdocs)```
![Screenshot 2024-01-15 121104](https://github.com/taufikalbani13/Lab1web/assets/115517181/adf76899-de3e-4038-bde0-e47a32c941ce)
## Buat file baru dengan nama ```mobil.php```
```<?php
/**
* Program sederhana pendefinisian class dan pemanggilan class.
**/
class Mobil
{
    private $warna;
    private $merk;
    private $harga;
    public function __construct()
    {
        $this->warna = "Biru";
        $this->merk = "BMW";
        $this->harga = "10000000";
    }
    public function gantiWarna ($warnaBaru)
    {
        $this->warna = $warnaBaru;
    }
    public function tampilWarna ()
    {
        echo "Warna mobilnya : " . $this->warna;
    }
}

// membuat objek mobil
$a = new Mobil();
$b = new Mobil();

// memanggil objek
echo "<b>Mobil pertama</b><br>";
$a->tampilWarna();
echo "<br>Mobil pertama ganti warna<br>";
$a->gantiWarna("Merah");
$a->tampilWarna();

// memanggil objek
echo "<br><b>Mobil kedua</b><br>";
$b->gantiWarna("Hijau");
$b->tampilWarna();

?>
```
## Class Library
### Class library merupakan pustaka kode program yang dapat digunakan bersama pada beberapa file yang berbeda (konsep modularisasi). Class library menyimpan fungsi-fungsi atau class objek komponen untuk memudahkan dalam proses development aplikasi.
## Contoh Class Library untuk membuat form
## Buat file baru dengan nama ```form.php```
```<?php
/**
* Nama Class: Form
* Deskripsi: CLass untuk membuat form inputan text sederhan
**/
class Form
{
    private $fields = array();
    private $action;
    private $submit = "Submit Form";
    private $jumField = 0;
    public function __construct($action, $submit)
    {
        $this->action = $action;
        $this->submit = $submit;
    }
    public function displayForm()
    {
        echo "<form action='".$this->action."' method='POST'>";
        echo '<table width="100%" border="0">';
        for ($j=0; $j<count($this->fields); $j++) {
            echo                                                    "<tr><td
align='right'>".$this->fields[$j]['label']."</td>";
            echo                 "<td><input                      type='text'
name='".$this->fields[$j]['name']."'></td></tr>";
        }
        echo "<tr><td colspan='2'>";
        echo "<input type='submit' value='".$this->submit."'></td></tr>";
        echo "</table>";
    }
    public function addField($name, $label)
    {
        $this->fields [$this->jumField]['name'] = $name;
        $this->fields [$this->jumField]['label'] = $label;
        $this->jumField ++;

    }
}
?>
```
### File tersebut tidak dapat dieksekusi langsung,karena hanya berisi deklarasi class.Untuk menggunakannya perlu dilakukan include pada file lain yang akan menjalankan dan harus di buat instance objek terlebih dahulu
## Contoh implementasi pemanggilan class library ```form.php```
## Buat file baru dengan nama ```form_input.php```
```<?php
/**
* Program memanfaatkan Program 10.2 untuk membuat form inputan sederhana.
**/

include "form.php";

echo "<html><head><title>Mahasiswa</title></head><body>";
$form = new Form("","Input Form");
$form->addField("txtnim", "Nim");
$form->addField("txtnama", "Nama");
$form->addField("txtalamat", "Alamat");
echo "<h3>Silahkan isi form berikut ini :</h3>";
$form->displayForm();
echo "</body></html>";

?>
```
## Contoh lainny untuk database connection dan query.Buat file baru dengan nama ```database.php```
```<?php

class Database {
    protected $host;
    protected $user;
    protected $password;
    protected $db_name;
    protected $conn;
    
    public function __construct() {
        $this->getConfig();
        $this->conn = new mysqli($this->host, $this->user, $this->password,
        $this->db_name);
        if ($this->conn->connect_error) {
            die("Connection failed: " . $this->conn->connect_error);
        }
    }
    
    private function getConfig() {
        include_once("config.php");
        $this->host = $config['host'];
        $this->user = $config['username'];
        $this->password = $config['password'];
        $this->db_name = $config['db_name'];
    }
    
    public function query($sql) {
        return $this->conn->query($sql);
    }
    
    public function get($table, $where=null) {
        if ($where) {
            $where = " WHERE ".$where;
        }
        $sql = "SELECT * FROM ".$table.$where;
        $sql = $this->conn->query($sql);
        $sql = $sql->fetch_assoc();
        return $sql;
    }

    public function insert($table, $data) {
        if (is_array($data)) {
            foreach($data as $key => $val) {
                $column[] = $key;$value[] = "'{$val}'";
            }
            $columns = implode(",", $column);
            $values = implode(",", $value);
        }
        $sql = "INSERT INTO ".$table." (".$columns.") VALUES (".$values.")";
        $sql = $this->conn->query($sql);
        if ($sql == true) {
            return $sql;
        } else {
            return false;
        }
    }

    public function update($table, $data, $where) {
        $update_value = "";
        if (is_array($data)) {
            foreach($data as $key => $val) 
            {
                $update_value[] = "$key='{$val}'"
            }

            $update_value = implode(",", $update_value);
        }

        $sql = "UPDATE ".$table." SET ".$update_value." WHERE ".$where;
        $sql = $this->conn->query($sql);
        if ($sql == true) {
            return true;
        } else {
            return false;
        }
    }

    public function delete($table, $filter) {
        $sql = "DELETE FROM ".$table." ".$filter;
        $sql = $this->conn->query($sql);
        if ($sql == true) {
            return true;
        } else {
            return false;
        }
    }

}
?>
```
## Output
![Screenshot 2024-01-15 121125](https://github.com/taufikalbani13/Lab1web/assets/115517181/7ce8ad68-a962-4a40-9501-defe3c45af00)
![Screenshot 2024-01-15 121137](https://github.com/taufikalbani13/Lab1web/assets/115517181/49090229-a1e6-400a-9931-4dbec95b2e4c)
![Screenshot 2024-01-15 121153](https://github.com/taufikalbani13/Lab1web/assets/115517181/0c87ab24-8e82-4054-9df3-312222fe5ee8)
