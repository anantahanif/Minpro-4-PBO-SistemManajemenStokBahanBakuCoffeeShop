# Mini Project 4 PBO - Sistem Manajemen Stok Bahan Baku Coffee Shop

**Nama**  : Ananta Hanif Fidzya Pratama  
**NIM**   : 2509116023  
**Kelas** : Sistem Informasi A 2025

---

## Informasi Umum Project

| Aspek | Keterangan |
|-------|------------|
| **Nama Repository** | Minpro-4-PBO-SistemManajemenStokBahanBakuCoffeeShop |
| **Tema** | Sistem Manajemen Stok Bahan Baku Coffee Shop |
| **Bahasa Pemrograman** | Java (Console Application) |
| **Database** | MySQL (nama database: `coffe_shop`) |
| **API Database** | JDBC (Java Database Connectivity) dengan `mysql-connector-j` 8.3.0 |
| **Arsitektur** | MVC (Model-View-Controller) dipadukan dengan DAO (Data Access Object) Pattern |
| **Build Tool** | Maven (`pom.xml`) |

---

## 1. Deskripsi Singkat Program

### Latar Belakang

Pengelolaan stok bahan baku pada usaha *coffee shop*, seperti biji kopi, susu, dan sirup, memerlukan pencatatan yang teliti agar ketersediaan bahan baku tetap terjaga dan operasi harian berjalan lancar. Pada Mini Project 3, data bahan baku hanya disimpan di dalam `ArrayList` yang berada di RAM, sehingga **seluruh data hilang setiap kali program ditutup**.

Mini Project 4 dikembangkan sebagai lanjutan dari Mini Project 3 dengan mengintegrasikan program ke **database MySQL** menggunakan **JDBC** dan pola **DAO**. Dengan demikian, data tersimpan secara permanen dan tetap tersedia meskipun program dimatikan lalu dijalankan kembali.

### Deskripsi Program

Program ini merupakan aplikasi berbasis konsol (*Command Line Interface* / CLI) yang digunakan untuk mengelola data stok bahan baku *coffee shop*. Data yang dikelola meliputi ID bahan, nama bahan, jumlah stok, jenis kemasan, serta atribut khusus sesuai jenis bahan baku, yaitu **jenis cairan** untuk Bahan Cair dan **bentuk fisik** untuk Bahan Padat.

Program menyediakan lima fitur utama:

1. **Tambah Bahan Baku** - Menambahkan data bahan baku cair atau padat baru ke database (ID dicek agar tidak duplikat).
2. **Tampilkan Semua Bahan Baku** - Menampilkan seluruh data bahan baku yang tersimpan di database.
3. **Update Data Bahan Baku** - Mengubah nama dan jenis kemasan bahan berdasarkan ID.
4. **Kelola Stok** - Menambah stok (biasa), menambah stok dengan catatan/supplier (demo *Method Overloading*), dan mengurangi stok (demo *Interface* `StokOperasi`), dengan hasil yang langsung disimpan ke database.
5. **Hapus Bahan Baku** - Menghapus data bahan baku dari database berdasarkan ID.

### Menu Utama

```text
=============================================
  SISTEM MANAJEMEN STOK BAHAN BAKU COFFEE SHOP
           (TERINTEGRASI DATABASE JDBC)
=============================================
1. Tambah Bahan Baku
2. Tampilkan Semua Bahan Baku
3. Update Data Bahan Baku
4. Kelola Stok (Interface & Database Update)
5. Hapus Bahan Baku
6. Keluar
=============================================
Pilih menu (1-6):
```

<img width="486" height="277" alt="Screenshot 2026-10-15 222840" src="https://github.com/user-attachments/assets/96fe5bbf-9da1-43a8-a5fc-64b724c00a05" />


---

## 2. Penjelasan Struktur Package

```text
minpro4 (Source Packages)
│
├── config
│   └── Koneksi.java          (Koneksi ke database MySQL via DriverManager)
│
├── model
│   ├── Bahan.java            (Abstract Class / Parent Class)
│   ├── StokOperasi.java      (Interface kontrak operasi stok)
│   ├── BahanCair.java        (Child Class: extends Bahan, implements StokOperasi)
│   ├── BahanPadat.java       (Child Class: extends Bahan, implements StokOperasi)
│   └── BahanDAO.java         (Data Access Object: seluruh query SQL CRUD)
│
├── controller
│   └── StokController.java   (Penghubung View dan BahanDAO)
│
└── view
    └── Minpro4.java          (Main Class: CLI interaktif dan validasi input)

coffe_shop.sql                (Struktur tabel dan 2 dummy data, di root directory)
pom.xml                       (Konfigurasi Maven dan dependency MySQL Connector)
```

<img width="333" height="541" alt="image" src="https://github.com/user-attachments/assets/ec24bd54-b1ca-4512-a8db-ef253231f93e" />


### Peran Tiap Package dan File

| Package / File | Peran |
|----------------|-------|
| **`config/Koneksi.java`** | Membangun koneksi ke database MySQL menggunakan `DriverManager`. Koneksi dibuat ulang secara otomatis jika belum ada atau sudah tertutup. |
| **`model/Bahan.java`** | *Abstract class* induk yang menyimpan atribut umum (`idBahan`, `namaBahan`, `stok`, `kemasan`) beserta validasinya. |
| **`model/StokOperasi.java`** | *Interface* yang mendefinisikan kontrak operasi stok (`tambahStok` dan `kurangiStok`). |
| **`model/BahanCair.java`** | Subclass `Bahan` untuk bahan cair, menambah atribut `jenisCairan`. |
| **`model/BahanPadat.java`** | Subclass `Bahan` untuk bahan padat, menambah atribut `bentukFisik`. |
| **`model/BahanDAO.java`** | Satu-satunya class yang berkomunikasi dengan database. Seluruh query SQL (`SELECT`, `INSERT`, `UPDATE`, `DELETE`) ditulis di sini menggunakan `PreparedStatement`. |
| **`controller/StokController.java`** | Menghubungkan View dengan `BahanDAO`, serta menampilkan pesan hasil operasi. |
| **`view/Minpro4.java`** | Menampilkan menu CLI, menerima input, dan memvalidasi input pengguna. |
| **`coffe_shop.sql`** | Berisi struktur tabel `bahan_baku` dan 2 dummy data awal yang **wajib di-import** sebelum program dijalankan. |

### Arsitektur MVC + DAO

```text
┌────────┐    ┌──────────────┐    ┌──────────┐    ┌──────────┐    ┌─────────┐
│  View  │ -> │  Controller  │ -> │   DAO    │ -> │ Koneksi  │ -> │  MySQL  │
│Minpro4 │    │StokController│    │BahanDAO  │    │  (JDBC)  │    │coffe_shop│
└────────┘    └──────────────┘    └──────────┘    └──────────┘    └─────────┘
                                        │
                                        ▼
                               Model (Bahan, BahanCair, BahanPadat)
```

- **Model** merepresentasikan data bahan baku beserta aturan validasinya.
- **View** hanya menangani tampilan dan input pengguna.
- **Controller** menjembatani View dengan lapisan akses data.
- **DAO** memisahkan logika akses database dari logika bisnis, sehingga View dan Controller tidak berisi query SQL sama sekali.

---

## 3. Penjelasan Alur Program

Alur program dijelaskan secara berurutan, mulai dari koneksi database hingga operasi CRUD.

### 3.1 Persiapan Database

Sebelum program dijalankan, file `coffe_shop.sql` harus di-import ke MySQL (misalnya melalui phpMyAdmin). File ini membuat database `coffe_shop`, tabel `bahan_baku`, dan 2 dummy data awal. Struktur tabel `bahan_baku` mengikuti kolom yang diakses oleh `BahanDAO`:

| Kolom | Keterangan |
|-------|------------|
| `id_bahan` | ID unik bahan baku |
| `nama_bahan` | Nama bahan baku |
| `stok` | Jumlah stok |
| `kemasan` | Jenis kemasan |
| `kategori` | Kategori bahan: `Cair` atau `Padat` |
| `detail_spesifik` | Jenis cairan (untuk `Cair`) atau bentuk fisik (untuk `Padat`) |

### 3.2 Koneksi ke Database

Koneksi dibangun oleh class `Koneksi` menggunakan `DriverManager` dengan URL, *username*, dan *password* MySQL:

```java
public class Koneksi {
    private static final String URL = "jdbc:mysql://localhost:3306/coffe_shop";
    private static final String USER = "root";
    private static final String PASS = "";

    private static Connection conn;

    public static Connection getConnection() {
        try {
            if (conn == null || conn.isClosed()) {
                conn = DriverManager.getConnection(URL, USER, PASS);
            }
        } catch (SQLException e) {
            System.out.println("Koneksi Database GAGAL: " + e.getMessage());
        }
        return conn;
    }
}
```

Jika koneksi belum ada atau sudah tertutup, koneksi baru dibuat. Jika gagal, `SQLException` ditangkap dan pesan kesalahan ditampilkan sehingga program tidak *crash*.

### 3.3 Menjalankan Program

Program dimulai dari method `main()` pada class `Minpro4`. Saat class dimuat, objek `Scanner` dan `StokController` dibuat sebagai atribut `static`. *Constructor* `StokController` membuat objek `BahanDAO`. Selanjutnya, perulangan `while (running)` menampilkan menu utama dan memproses pilihan pengguna dengan `switch-case` hingga pengguna memilih menu **6 (Keluar)**.

```java
switch (pilihan) {
    case 1:
        menuTambahBahan();
        break;
    case 2:
        controller.tampilkanSemuaBahan();
        break;
    case 3:
        menuUpdateBahan();
        break;
    case 4:
        menuKelolaStok();
        break;
    case 5:
        menuHapusBahan();
        break;
    case 6:
        System.out.println("Terima kasih! Program selesai.");
        running = false;
        break;
    default:
        System.out.println("Pilihan tidak valid! Silakan pilih angka 1-6.");
        break;
}
```

### 3.4 Alur Umum Setiap Permintaan

```text
Pengguna -> View (validasi input) -> Controller -> BahanDAO
         -> Koneksi.getConnection() -> PreparedStatement -> MySQL
         -> hasil dikembalikan ke Controller -> pesan ditampilkan di View
```

### 3.5 Operasi CRUD

#### a. Create - Tambah Bahan Baku

* **Proses:** View meminta ID (dicek lewat `isIdExist()` agar tidak duplikat), nama, stok awal, kemasan, lalu jenis bahan (Cair/Padat) beserta detail khususnya. View membuat objek `BahanCair` atau `BahanPadat`, kemudian memanggil `controller.tambahBahan()`. Controller meneruskannya ke `BahanDAO.tambahBahan()` yang menjalankan `INSERT`.

```java
// StokController.java
public void tambahBahan(Bahan bahan) {
    if (bahanDAO.tambahBahan(bahan)) {
        System.out.println("Berhasil menambahkan bahan ke database: " + bahan.getNamaBahan());
    } else {
        System.out.println("Gagal menambahkan bahan ke database.");
    }
}
```

<img width="652" height="521" alt="image" src="https://github.com/user-attachments/assets/f66875cb-76db-4283-8d21-4f23813f137a" />

#### b. Read - Tampilkan Semua Bahan Baku

* **Proses:** `StokController.tampilkanSemuaBahan()` memanggil `BahanDAO.getAllBahan()` yang menjalankan `SELECT` dan mengubah setiap baris hasil menjadi objek `BahanCair` atau `BahanPadat`. Controller lalu mengiterasi list dan memanggil `tampilkanInfo()` pada setiap objek.

```java
// StokController.java
public void tampilkanSemuaBahan() {
    ArrayList<Bahan> listBahan = bahanDAO.getAllBahan();
    if (listBahan.isEmpty()) {
        System.out.println("Data bahan baku di database masih kosong.");
    } else {
        System.out.println("\n=== Daftar Stok Bahan Baku (Database MySQL) ===");
        for (Bahan bahan : listBahan) {
            bahan.tampilkanInfo(); // Polymorphism Overriding
        }
    }
}
```

Method pencarian berdasarkan ID yang dipakai secara internal oleh fitur lain:

```java
public Bahan getBahanById(String idBahan) {
    ArrayList<Bahan> listBahan = bahanDAO.getAllBahan();
    for (Bahan bahan : listBahan) {
        if (bahan.getIdBahan().equalsIgnoreCase(idBahan)) {
            return bahan;
        }
    }
    return null;
}

public boolean isIdExist(String idBahan) {
    return getBahanById(idBahan) != null;
}
```

<img width="692" height="372" alt="image" src="https://github.com/user-attachments/assets/a11c7d64-2f30-4bdc-a7c0-89a9819c58a6" />


#### c. Update - Update Data Bahan Baku

* **Proses:** View memeriksa apakah ID ada di database, lalu meminta nama dan kemasan baru. `BahanDAO.updateBahan()` menjalankan `UPDATE` pada kolom `nama_bahan` dan `kemasan`.

```java
// StokController.java
public boolean updateBahan(String idBahan, String namaBaru, String kemasanBaru) {
    return bahanDAO.updateBahan(idBahan, namaBaru, kemasanBaru);
}
```

<img width="504" height="402" alt="image" src="https://github.com/user-attachments/assets/98f3d9e4-efea-43a8-b2fc-bce6896832c5" />

Hasil setelah di update:

<img width="686" height="384" alt="image" src="https://github.com/user-attachments/assets/1feb244f-a639-4f5f-8565-be0f85be30fa" />

#### d. Kelola Stok (Update Stok ke Database)

* **Proses:** View mengambil objek bahan dari database lewat `getBahanById()`. Pengguna memilih aksi: (1) tambah stok biasa, (2) tambah stok dengan catatan/supplier (*Method Overloading*), atau (3) kurangi stok (*Interface* `StokOperasi`). Operasi tersebut mengubah stok pada objek (sehingga validasi setter tetap berjalan), lalu hasilnya disimpan ke database lewat `controller.updateStokDatabase()` yang memanggil `BahanDAO.updateStok()`.

```java
// Minpro4.java (menu Kelola Stok, pilihan 1)
int jumlahTambah1 = readIntInput("Masukkan jumlah penambahan stok: ");
if (bahan instanceof BahanCair) {
    ((BahanCair) bahan).tambahStok(jumlahTambah1);
} else if (bahan instanceof BahanPadat) {
    ((BahanPadat) bahan).tambahStok(jumlahTambah1);
}
controller.updateStokDatabase(bahan.getIdBahan(), bahan.getStok());
System.out.println("Perubahan stok berhasil disimpan ke database.");
```

<img width="582" height="520" alt="image" src="https://github.com/user-attachments/assets/af86c6e3-3cc1-4e6e-812f-d3b910776075" />

#### e. Delete - Hapus Bahan Baku

* **Proses:** `BahanDAO.hapusBahan()` menjalankan `DELETE` berdasarkan ID. Hasilnya berupa `boolean` yang menentukan pesan yang ditampilkan View.

```java
// StokController.java
public boolean hapusBahan(String idBahan) {
    return bahanDAO.hapusBahan(idBahan);
}
```

<img width="667" height="508" alt="image" src="https://github.com/user-attachments/assets/7f1a926f-ffe9-4edb-86c9-6282cce817ac" />

---

## 4. Penjelasan Penerapan Encapsulation dan Inheritance

### 4.1 Encapsulation

Seluruh atribut pada class `Bahan`, `BahanCair`, dan `BahanPadat` dibuat dengan modifier **`private`**. Pengaksesan dan pengubahan nilai atribut dilakukan melalui method **getter** dan **setter** yang lengkap. Atribut `idBahan` dibuat **`final`** sehingga nilainya tidak dapat diubah setelah objek dibuat (hanya memiliki getter, tanpa setter). Setter juga memuat **validasi** untuk mencegah input kosong dan stok negatif.

```java
public abstract class Bahan {
    private final String idBahan;   // final: tidak dapat diubah
    private String namaBahan;
    private int stok;
    private String kemasan;

    public Bahan(String idBahan, String namaBahan, int stok, String kemasan) {
        this.idBahan = idBahan;
        // Setter dipanggil di constructor agar validasi langsung berjalan
        setNamaBahan(namaBahan);
        setStok(stok);
        setKemasan(kemasan);
    }

    public String getIdBahan() { return idBahan; }
    public String getNamaBahan() { return namaBahan; }
    // ...
}
```

**Validasi pada setter:**

```java
public void setNamaBahan(String namaBahan) {
    if (namaBahan == null || namaBahan.trim().isEmpty()) {
        System.out.println("Peringatan: Nama bahan tidak boleh kosong! Disetel ke 'Tanpa Nama'.");
        this.namaBahan = "Tanpa Nama";
    } else {
        this.namaBahan = namaBahan;
    }
}

public void setStok(int stok) {
    if (stok < 0) {
        System.out.println("Peringatan: Stok tidak boleh negatif! Disetel ke 0.");
        this.stok = 0;
    } else {
        this.stok = stok;
    }
}
```

Karena *constructor* memanggil setter, validasi ini otomatis berjalan, termasuk saat objek dibuat dari data hasil `SELECT` di database. Setter `setKemasan()`, `setJenisCairan()`, dan `setBentukFisik()` juga menyetel nilai default ("Default", "Tanpa Jenis", "Tanpa Bentuk") jika input kosong.

**Validasi input pada View (`Minpro4`):**

```java
private static String readNonEmptyString(String prompt) {
    String input;
    do {
        System.out.print(prompt);
        input = scanner.nextLine().trim();
        if (input.isEmpty()) {
            System.out.println("Peringatan: Input tidak boleh kosong! Silakan isi kembali.");
        }
    } while (input.isEmpty());
    return input;
}

private static int readIntInput(String prompt) {
    while (true) {
        System.out.print(prompt);
        String rawInput = scanner.nextLine().trim();
        try {
            return Integer.parseInt(rawInput);
        } catch (NumberFormatException e) {
            System.out.println("Peringatan: Input harus berupa angka bulat valid!");
        }
    }
}
```

`readNonEmptyString()` memastikan input teks tidak kosong, sedangkan `readIntInput()` menangani `NumberFormatException` sehingga program tidak *crash* ketika pengguna memasukkan selain angka bulat.

<img width="570" height="415" alt="image" src="https://github.com/user-attachments/assets/dad30eb2-0984-4a6a-88d4-a9b1857bc36d" />

### 4.2 Inheritance

Parent class `Bahan` diturunkan ke dua child class, yaitu `BahanCair` dan `BahanPadat`, menggunakan kata kunci **`extends`**. Child class mewarisi atribut (`idBahan`, `namaBahan`, `stok`, `kemasan`) beserta getter/setter milik `Bahan`, lalu menambahkan atribut khususnya sendiri. Child class memanggil *constructor* induk menggunakan **`super()`**.

```java
public class BahanCair extends Bahan implements StokOperasi {
    private String jenisCairan;

    public BahanCair(String idBahan, String namaBahan, int stok, String kemasan, String jenisCairan) {
        super(idBahan, namaBahan, stok, kemasan);
        setJenisCairan(jenisCairan);
    }
}
```

```java
public class BahanPadat extends Bahan implements StokOperasi {
    private String bentukFisik;

    public BahanPadat(String idBahan, String namaBahan, int stok, String kemasan, String bentukFisik) {
        super(idBahan, namaBahan, stok, kemasan);
        setBentukFisik(bentukFisik);
    }
}
```

| Class | Mewarisi dari `Bahan` | Atribut tambahan |
|-------|-----------------------|------------------|
| `BahanCair` | `idBahan`, `namaBahan`, `stok`, `kemasan` | `jenisCairan` |
| `BahanPadat` | `idBahan`, `namaBahan`, `stok`, `kemasan` | `bentukFisik` |

---

## 5. Penjelasan Penerapan Polymorphism dan Abstraction

### 5.1 Abstraction

Class `Bahan` dideklarasikan sebagai **`abstract class`** sehingga tidak dapat diinstansiasi langsung (`new Bahan(...)` tidak diperbolehkan). Class ini memiliki **abstract method** `tampilkanInfo()` yang hanya berisi deklarasi tanpa isi, sehingga setiap child class **wajib** mengimplementasikannya sesuai karakteristik masing-masing.

```java
public abstract class Bahan {
    // ...

    // Abstract method: wajib diimplementasikan oleh class turunan
    public abstract void tampilkanInfo();
}
```

Dengan abstraction, `Bahan` hanya menjadi kerangka umum untuk semua jenis bahan baku, sedangkan detail tampilan diserahkan kepada `BahanCair` dan `BahanPadat`.

### 5.2 Polymorphism

#### A. Method Overriding

Abstract method `tampilkanInfo()` di-*override* oleh `BahanCair` dan `BahanPadat` dengan format tampilan yang berbeda.

```java
// BahanCair.java
@Override
public void tampilkanInfo() {
    System.out.println("ID: " + getIdBahan() + " | [Cair] " + getNamaBahan()
        + " | Jenis: " + jenisCairan + " | Stok: " + getStok() + " " + getKemasan());
}

// BahanPadat.java
@Override
public void tampilkanInfo() {
    System.out.println("ID: " + getIdBahan() + " | [Padat] " + getNamaBahan()
        + " | Bentuk: " + bentukFisik + " | Stok: " + getStok() + " " + getKemasan());
}
```

Polymorphism terlihat jelas pada `StokController.tampilkanSemuaBahan()` dan `BahanDAO.getAllBahan()`: `ArrayList<Bahan>` dapat menampung objek `BahanCair` maupun `BahanPadat`. Saat `bahan.tampilkanInfo()` dipanggil dalam perulangan, Java otomatis menjalankan versi method sesuai tipe objek sebenarnya.

```java
ArrayList<Bahan> listBahan = bahanDAO.getAllBahan();
for (Bahan bahan : listBahan) {
    bahan.tampilkanInfo(); // versi BahanCair atau BahanPadat, tergantung objeknya
}
```

#### B. Method Overloading

Method `tambahStok()` dibuat dalam dua versi dengan jumlah parameter berbeda pada `BahanCair` dan `BahanPadat`:

| Versi | Parameter | Keterangan |
|-------|-----------|------------|
| Biasa | `tambahStok(int jumlah)` | Implementasi dari interface `StokOperasi` |
| Overloading | `tambahStok(int jumlah, String supplier)` | Menambah stok disertai catatan/nama supplier |

```java
// BahanCair.java
@Override
public void tambahStok(int jumlah) {
    if (jumlah > 0) {
        setStok(getStok() + jumlah);
        System.out.println("Stok " + getNamaBahan() + " berhasil ditambahkan.");
    }
}

// Overloading: jumlah parameter berbeda
public void tambahStok(int jumlah, String supplier) {
    if (jumlah > 0) {
        setStok(getStok() + jumlah);
        System.out.println("Stok " + getNamaBahan() + " ditambah sejumlah " + jumlah
            + " dari supplier: " + supplier);
    }
}
```

Pada `BahanPadat`, parameter tambahannya berupa catatan kondisi (`String kondisiCatatan`). Kedua versi dipanggil dari menu **Kelola Stok** (pilihan 1 dan 2), lalu stok hasil perubahan disimpan ke database.

---

## 6. Penjelasan Penerapan JDBC

**JDBC (Java Database Connectivity)** adalah API standar Java yang menghubungkan program Java dengan berbagai jenis database. Dengan JDBC, data tidak lagi hilang saat program ditutup seperti pada `ArrayList` di Minpro 3, karena disimpan secara permanen di database MySQL.

### 6.1 Komponen JDBC yang Digunakan

| Komponen | Fungsi dalam program |
|----------|----------------------|
| `java.sql.Connection` | Jembatan utama untuk mengirim perintah SQL dari Java ke database |
| `java.sql.DriverManager` | Membangun koneksi menggunakan URL, *username*, dan *password* (di `Koneksi.java`) |
| `java.sql.PreparedStatement` | Menjalankan perintah SQL dengan parameter `?` (di `BahanDAO.java`) |
| `java.sql.ResultSet` | Menampung hasil `SELECT` yang dibaca baris per baris |
| `java.sql.SQLException` | Menangkap dan menampilkan pesan kesalahan akses database |

### 6.2 Dependency MySQL Connector

Driver JDBC dipasang melalui `pom.xml`:

```xml
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>8.3.0</version>
</dependency>
```

### 6.3 Penerapan PreparedStatement

Seluruh query pada `BahanDAO` dijalankan menggunakan `PreparedStatement`. Nilai dari pengguna dimasukkan melalui parameter **`?`** dan diisi dengan method `setString()` atau `setInt()`, bukan digabung langsung ke teks query. Dengan cara ini, input pengguna diperlakukan murni sebagai **data**, bukan bagian dari perintah SQL, sehingga program **aman dari SQL Injection**.

Perbandingan penulisan query:

```java
// TIDAK AMAN: nilai digabung langsung ke teks query (rawan SQL Injection)
String query = "DELETE FROM bahan_baku WHERE id_bahan = '" + idBahan + "'";

// AMAN: menggunakan placeholder (?) pada PreparedStatement
String sql = "DELETE FROM bahan_baku WHERE id_bahan = ?";
```

**Contoh INSERT (`tambahBahan`)**

```java
public boolean tambahBahan(Bahan bahan) {
    String sql = "INSERT INTO bahan_baku (id_bahan, nama_bahan, stok, kemasan, kategori, detail_spesifik) VALUES (?, ?, ?, ?, ?, ?)";

    try (Connection conn = Koneksi.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql)) {

        pstmt.setString(1, bahan.getIdBahan());
        pstmt.setString(2, bahan.getNamaBahan());
        pstmt.setInt(3, bahan.getStok());
        pstmt.setString(4, bahan.getKemasan());

        if (bahan instanceof BahanCair) {
            pstmt.setString(5, "Cair");
            pstmt.setString(6, ((BahanCair) bahan).getJenisCairan());
        } else if (bahan instanceof BahanPadat) {
            pstmt.setString(5, "Padat");
            pstmt.setString(6, ((BahanPadat) bahan).getBentukFisik());
        }

        int rowsAffected = pstmt.executeUpdate();
        return rowsAffected > 0;
    } catch (SQLException e) {
        System.out.println("Error saat menambah bahan: " + e.getMessage());
        return false;
    }
}
```

**Contoh UPDATE (`updateBahan` dan `updateStok`)**

```java
public boolean updateBahan(String idBahan, String namaBaru, String kemasanBaru) {
    String sql = "UPDATE bahan_baku SET nama_bahan = ?, kemasan = ? WHERE id_bahan = ?";

    try (Connection conn = Koneksi.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql)) {

        pstmt.setString(1, namaBaru);
        pstmt.setString(2, kemasanBaru);
        pstmt.setString(3, idBahan);

        return pstmt.executeUpdate() > 0;
    } catch (SQLException e) {
        System.out.println("Error saat mengupdate data: " + e.getMessage());
        return false;
    }
}

public boolean updateStok(String idBahan, int stokBaru) {
    String sql = "UPDATE bahan_baku SET stok = ? WHERE id_bahan = ?";
    // pstmt.setInt(1, stokBaru);
    // pstmt.setString(2, idBahan);
    // ...
}
```

**Contoh DELETE (`hapusBahan`)**

```java
public boolean hapusBahan(String idBahan) {
    String sql = "DELETE FROM bahan_baku WHERE id_bahan = ?";

    try (Connection conn = Koneksi.getConnection();
         PreparedStatement pstmt = conn.prepareStatement(sql)) {

        pstmt.setString(1, idBahan);

        return pstmt.executeUpdate() > 0;
    } catch (SQLException e) {
        System.out.println("Error saat menghapus data: " + e.getMessage());
        return false;
    }
}
```

**Contoh SELECT (`getAllBahan`)**

Query `SELECT` juga dijalankan melalui `PreparedStatement`. Karena mengambil seluruh data tanpa input dari pengguna, query ini tidak memerlukan parameter `?`.

```java
String sql = "SELECT * FROM bahan_baku";

try (Connection conn = Koneksi.getConnection();
     PreparedStatement pstmt = conn.prepareStatement(sql);
     ResultSet rs = pstmt.executeQuery()) {

    while (rs.next()) {
        // membaca kolom dan memetakan ke objek (lihat bagian 7.2)
    }
}
```

### 6.4 Rangkuman Method DAO

| Method | Query SQL | Method JDBC | Keterangan |
|--------|-----------|-------------|------------|
| `getAllBahan()` | `SELECT * FROM bahan_baku` | `executeQuery()` | Membaca seluruh data |
| `tambahBahan()` | `INSERT INTO bahan_baku (...) VALUES (?, ?, ?, ?, ?, ?)` | `executeUpdate()` | 6 parameter |
| `updateBahan()` | `UPDATE bahan_baku SET nama_bahan = ?, kemasan = ? WHERE id_bahan = ?` | `executeUpdate()` | 3 parameter |
| `updateStok()` | `UPDATE bahan_baku SET stok = ? WHERE id_bahan = ?` | `executeUpdate()` | 2 parameter |
| `hapusBahan()` | `DELETE FROM bahan_baku WHERE id_bahan = ?` | `executeUpdate()` | 1 parameter |

### 6.5 Penutupan Resource Otomatis

Seluruh method DAO menggunakan **`try-with-resources`** sehingga `Connection`, `PreparedStatement`, dan `ResultSet` otomatis ditutup setelah digunakan. Setiap kesalahan akses database ditangani dengan `catch (SQLException e)` agar program tidak berhenti tiba-tiba.

---

## 7. Penjelasan Letak Penerapan Nilai Tambah

### 7.1 Interface `StokOperasi`

Interface `StokOperasi` mendefinisikan kontrak operasi stok yang harus dimiliki setiap bahan baku, dan diimplementasikan oleh `BahanCair` dan `BahanPadat` menggunakan kata kunci **`implements`**.

**Letak penerapan:**

| File | Peran |
|------|-------|
| `model/StokOperasi.java` | Deklarasi interface |
| `model/BahanCair.java` | `implements StokOperasi` |
| `model/BahanPadat.java` | `implements StokOperasi` |
| `view/Minpro4.java` | Memanggil `tambahStok()` dan `kurangiStok()` pada menu Kelola Stok, lalu menyimpan hasilnya ke database |

**Kode interface `StokOperasi.java`:**

```java
package model;

public interface StokOperasi {
    void tambahStok(int jumlah);
    boolean kurangiStok(int jumlah);
}
```

**Contoh implementasi `kurangiStok()` pada `BahanCair.java`:**

```java
@Override
public boolean kurangiStok(int jumlah) {
    if (jumlah > 0 && getStok() >= jumlah) {
        setStok(getStok() - jumlah);
        return true;
    }
    return false; // Gagal: jumlah tidak valid atau melebihi stok
}
```

Method mengembalikan `boolean` sebagai penanda berhasil atau tidaknya pengurangan. Pengurangan gagal jika jumlah tidak lebih dari 0 atau melebihi stok yang tersedia, sehingga stok tidak pernah menjadi negatif. Perubahan hanya disimpan ke database jika pengurangan berhasil.

**Pemanggilan pada View (`menuKelolaStok()`, pilihan 3):**

```java
int jumlahKurang = readIntInput("Masukkan jumlah pengurangan stok: ");
boolean sukses = false;
if (bahan instanceof BahanCair) {
    sukses = ((BahanCair) bahan).kurangiStok(jumlahKurang);
} else if (bahan instanceof BahanPadat) {
    sukses = ((BahanPadat) bahan).kurangiStok(jumlahKurang);
}

if (sukses) {
    controller.updateStokDatabase(bahan.getIdBahan(), bahan.getStok());
    System.out.println("Stok " + bahan.getNamaBahan() + " berhasil dikurangi dan diperbarui di database.");
} else {
    System.out.println("Gagal mengurangi stok! Jumlah tidak valid atau melebihi stok yang ada.");
}
```

### 7.2 Pola DAO dan Konsep Dasar ORM

**DAO (Data Access Object)** adalah pola yang memisahkan logika bisnis dari logika akses database. Class `BahanDAO` menjadi satu-satunya "pintu" untuk berkomunikasi dengan database, sehingga seluruh perintah SQL (`SELECT`, `INSERT`, `UPDATE`, `DELETE`) hanya ditulis di sini. View dan Controller tidak mengandung query SQL sama sekali.

**Letak penerapan:**

| File | Peran |
|------|-------|
| `model/BahanDAO.java` | Class DAO: seluruh query SQL dan pemetaan data |
| `config/Koneksi.java` | Menyediakan koneksi yang dipakai oleh DAO |
| `controller/StokController.java` | Memanggil method `BahanDAO` tanpa mengetahui detail SQL |

**Pemetaan objek-relasional (konsep dasar ORM)**

**ORM (Object-Relational Mapping)** adalah teknik memetakan data tabel relasional ke objek pada bahasa pemrograman. Pada program ini, `BahanDAO` melakukan pemetaan tersebut secara manual dalam dua arah:

**a. Dari tabel ke objek (saat `SELECT`)**

Baris pada tabel `bahan_baku` dipetakan menjadi objek Java sesuai nilai kolom `kategori`:

```java
while (rs.next()) {
    String id = rs.getString("id_bahan");
    String nama = rs.getString("nama_bahan");
    int stok = rs.getInt("stok");
    String kemasan = rs.getString("kemasan");
    String kategori = rs.getString("kategori");
    String detail = rs.getString("detail_spesifik");

    if ("Cair".equalsIgnoreCase(kategori)) {
        list.add(new BahanCair(id, nama, stok, kemasan, detail));
    } else if ("Padat".equalsIgnoreCase(kategori)) {
        list.add(new BahanPadat(id, nama, stok, kemasan, detail));
    }
}
```

**b. Dari objek ke tabel (saat `INSERT`)**

Tipe objek (`BahanCair` atau `BahanPadat`) dipetakan kembali menjadi nilai kolom `kategori` dan `detail_spesifik`.

**Tabel pemetaan:**

| Tabel `bahan_baku` | Objek Java |
|--------------------|------------|
| Kolom `id_bahan` | Atribut `idBahan` (`Bahan`) |
| Kolom `nama_bahan` | Atribut `namaBahan` (`Bahan`) |
| Kolom `stok` | Atribut `stok` (`Bahan`) |
| Kolom `kemasan` | Atribut `kemasan` (`Bahan`) |
| `kategori = 'Cair'` + `detail_spesifik` | Objek `BahanCair` dengan atribut `jenisCairan` |
| `kategori = 'Padat'` + `detail_spesifik` | Objek `BahanPadat` dengan atribut `bentukFisik` |

**Perbandingan dengan ORM framework**

| Aspek | JDBC + DAO (Minpro 4) | ORM Framework (Hibernate/JPA) |
|-------|-----------------------|-------------------------------|
| Penulisan query | Manual, SQL ditulis sendiri | Otomatis (`save()`, `find()`) |
| Pemetaan objek-tabel | Manual di dalam DAO | Otomatis lewat anotasi/konfigurasi |
| Kontrol atas query | Penuh | Sebagian tersembunyi |
| Kurva belajar | Lebih mudah dipahami dasarnya | Perlu konsep tambahan (Session, EntityManager) |

Pola DAO dengan pemetaan manual ini merupakan **dasar pemahaman ORM**: program bekerja dengan **objek** (`BahanCair`, `BahanPadat`), sementara detail penyimpanan ke tabel database disembunyikan di dalam lapisan DAO.

---

## Cara Menjalankan Program

1. *Clone* repository:
   ```bash
   git clone https://github.com/<username>/Minpro-4-PBO-SistemManajemenStokBahanBakuCoffeeShop.git
   ```
2. Jalankan **MySQL** (misalnya melalui XAMPP) dan buka **phpMyAdmin**.
3. **Import** file `coffe_shop.sql` dari root directory sehingga database `coffe_shop` beserta tabel `bahan_baku` dan 2 dummy data terbentuk.
4. Buka project menggunakan **NetBeans** (atau IDE Java lainnya yang mendukung Maven). Pastikan JDK sesuai dengan `pom.xml` dan dependency `mysql-connector-j` berhasil terunduh.
5. Sesuaikan `URL`, `USER`, dan `PASS` pada `config/Koneksi.java` jika konfigurasi MySQL berbeda (bawaan: `root` tanpa password).
6. Jalankan class `view.Minpro4` (*Run File*).

---

## Kesimpulan

Mini Project 4 ini dikembangkan sebagai lanjutan dari Mini Project 3 dengan mengintegrasikan program ke database MySQL. Beberapa poin pengembangan utamanya meliputi:

1. **Integrasi database MySQL** menggunakan **JDBC** sehingga data tersimpan permanen dan tidak hilang saat program ditutup.
2. Penggunaan **`PreparedStatement`** dengan parameter `?` pada seluruh query untuk mencegah **SQL Injection**.
3. Penerapan pola **DAO** melalui class `BahanDAO` yang memisahkan logika akses database dari logika bisnis, dipadukan dengan arsitektur **MVC**.
4. Pemetaan data tabel `bahan_baku` ke objek `BahanCair` dan `BahanPadat` sebagai **konsep dasar ORM**.
5. Tetap menerapkan konsep PBO dari Minpro 3: **Encapsulation**, **Inheritance**, **Abstraction**, **Polymorphism** (*Overriding* dan *Overloading*), serta **Interface** `StokOperasi`.
6. Penambahan class `Koneksi` pada package `config` dengan penanganan `SQLException` agar kegagalan koneksi tidak menyebabkan program *crash*.
