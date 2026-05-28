# DOKUMENTASI PROJECT UAS PEMROGRAMAN VISUAL
## SISTEM INFORMASI PENJUALAN OBAT SCM PADA APOTEK MAJU

**Dosen Pengampu:** [RIZALDI, S.Kom., M.Kom.]
**Mata Kuliah:** Pemrograman Visual
**Topik:** Supply Chain Management (SCM) Apotek

---

## 1. PENDAHULUAN (MODEL WATERFALL)
Sistem ini dibangun menggunakan metode **Waterfall** yang terdiri dari:
1. **Analisis Kebutuhan:** Mengumpulkan data kebutuhan Apotek Maju (Obat, Supplier, Penjualan, Keamanan Sistem).
2. **Desain Sistem:** Merancang Database MySQL (4 Tabel + 1 View) dan antarmuka aplikasi dengan tambahan fitur keamanan (Login).
3. **Implementasi Kode:** Membuat kode program menggunakan VB.NET Framework 4.0 dan MySql.Data.
4. **Pengujian:** Mengetes setiap fitur CRUD (Create, Read, Update, Delete), proses transaksi perhitungan total penjualan, dan fitur cetak struk.

Aplikasi ini mengadopsi struktur dari project referensi `AplNilaiMhs`, menerapkan pola koneksi, CRUD, dan UI yang stabil. Tema visual disesuaikan menjadi warna hijau (Apotek/Kesehatan) untuk pengalaman pengguna yang lebih baik.

---

## 2. STRUKTUR DATABASE (`db_apotek_maju`)
Database menggunakan **PHPMyAdmin/MySQL** dan terdiri dari:

1. **`tblsupplier` (Master Supplier)**
   - Fungsi: Menyimpan data pemasok obat ke apotek (elemen kunci SCM).
   - Atribut: `kd_supplier` (PK), `nm_supplier`, `alamat`, `telp`.

2. **`tblkategori` (Master Kategori Obat)**
   - Fungsi: Mengelompokkan obat (cth: Obat Bebas, Obat Keras, Vitamin).
   - Atribut: `kd_kategori` (PK), `nm_kategori`, `keterangan`.

3. **`tblobat` (Master Obat/Produk)**
   - Fungsi: Menyimpan data inventaris obat.
   - Atribut: `kd_obat` (PK), `nm_obat`, `kd_kategori` (FK), `kd_supplier` (FK), `satuan`, `harga`, `stok`.

4. **`tblpenjualan` (Transaksi)**
   - Fungsi: Mencatat arus keluar barang (penjualan ke konsumen).
   - Atribut: `id_penjualan` (PK, AutoIncrement), `tgl_jual`, `kd_obat` (FK), `jumlah`, `harga_satuan`, `total_harga`, `nama_pembeli`, `keterangan`.

5. **`querypenjualan` (View SQL)**
   - Fungsi: Melakukan `JOIN` pada keempat tabel di atas agar mudah ditampilkan di ListView secara lengkap beserta nama obat, nama supplier, dan kategori.

---

## 3. STRUKTUR APLIKASI (VB.NET)

### 3.1 Keamanan Sistem (`FormLogin.vb`)
*   **Penjelasan:** Aplikasi ini dilindungi oleh form login sebagai *Startup Form*. Hanya pengguna yang memiliki otorisasi (Admin) yang dapat masuk ke Menu Utama.
*   **Akun Tersedia:** 
    - Username: `salsa`, Password: `salsa123`
    - Username: `dinda`, Password: `dinda123`
    - Username: `juan`, Password: `juan123`

### 3.2 Modul Koneksi (`BukaKoneksi.vb`)
*   **Penjelasan:** Ini adalah *jantung* dari aplikasi. Modul ini melakukan inisialisasi objek koneksi `MySqlConnection`.
*   **Referensi:** Diadopsi langsung dari `bukakoneksi` milik project AplNilaiMhs.

### 3.3 `F_MenuUtama.vb`
*   **Penjelasan:** Menu navigasi utama menggunakan kontrol `MenuStrip` untuk mengarahkan pengguna ke setiap form fitur.
*   **Tema:** Background color diatur ke hijau gelap (`0, 64, 0`) sesuai dengan identitas Apotek.

### 3.4 Form Master Data (CRUD)
Sistem memiliki 3 Form Master yang melakukan proses C-R-U-D (Create, Read, Update, Delete) dan Pencarian (*Search*):
1.  **Read (`isilist`):** Saat form `Load`, aplikasi melakukan `SELECT * FROM [tabel]` dan memindahkannya ke dalam kontrol `ListView`.
2.  **Create (`btnSave_Click`):** Memvalidasi TextBox, jika terisi penuh, menjalankan query `INSERT INTO`.
3.  **Update (`btnEdit_Click`):** Saat baris di `ListView` diklik, data pindah ke TextBox. Setelah diedit, menjalankan query `UPDATE [tabel] SET ...`.
4.  **Delete (`btnDelete_Click`):** Menjalankan query `DELETE FROM [tabel] WHERE...` dengan konfirmasi keamanan.
5.  **Search (`caridata`):** Menerapkan pencarian *real-time* dengan event `TextChanged`.

**Daftar Form Master:**
*   `FormSupplier.vb`: Untuk mengelola `tblsupplier`. 
*   `FormKategoriObat.vb`: Untuk mengelola `tblkategori`.
*   `FormObat.vb`: Mengelola `tblobat`. Dilengkapi dengan tombol pencarian popup (`...`) untuk mengisi **Kode Kategori** dan **Kode Supplier** secara otomatis (textbox bersifat *Read Only* agar data konsisten).

### 3.5 Form Transaksi (`FormTransaksiPenjualan.vb`)
*   **Penjelasan:** Mencatat transaksi dari Apotek ke Pelanggan.
*   **Proses Kalkulasi:** Ketika obat dipilih, Harga Satuan dan Satuan akan terisi otomatis. Saat `jumlah` dimasukkan dan tombol `PROSES` ditekan, sistem menghitung: `Total Harga = Jumlah * Harga Satuan`.
*   **Pengurutan Data:** Data riwayat penjualan ditampilkan secara berurutan mulai dari transaksi pertama (ID ke-1) menggunakan query `ORDER BY id_penjualan ASC`.
*   **Fitur Cetak Struk:** Menggunakan kelas `PrintDocument` bawaan .NET (tanpa perlu *runtime* tambahan seperti SAP Crystal Reports). Pengguna dapat mengklik data transaksi di tabel, lalu menekan tombol **CETAK** untuk membuka *Print Preview* struk gaya nota kasir, yang siap dicetak ke printer.

### 3.6 Form Popup Pencarian
Sebagai form *helper* untuk memudahkan input FK (Foreign Key) tanpa harus mengetik ID manual:
*   `FormCariObat.vb` (Digunakan di Form Transaksi)
*   `FormCariSupplier.vb` (Digunakan di Form Obat)
*   `FormCariKategori.vb` (Digunakan di Form Obat)

---

## 4. INTEGRASI TEMA SCM (Supply Chain Management)
Project ini mengintegrasikan alur sederhana dari proses rantai pasok:
1.  **Hulu (Upstream):** Terdapat data `Supplier`. Setiap Obat wajib memiliki kaitan dengan siapa pemasoknya.
2.  **Internal (Inventory):** Pengaturan stok persediaan barang pada master Obat, beserta informasi Kategori produk.
3.  **Hilir (Downstream):** Modul Penjualan ke konsumen/pembeli.

## 5. CARA MENJALANKAN (DEPLOYMENT)
1. Nyalakan server **Apache** dan **MySQL** dari XAMPP Control Panel.
2. Buka `localhost/phpmyadmin` di browser.
3. Buat database baru bernama `db_apotek_maju`.
4. Pilih menu **Import**, lalu masukkan file `db_apotek_maju.sql` yang ada di root folder project ini.
5. Buka project `SCMPenjualanObat.sln` di Microsoft Visual Studio.
6. Tekan `F5` atau tombol **Start Debugging** (ikon hijau).
7. Login menggunakan akun `salsa` / `salsa123`.
8. Aplikasi Apotek Maju siap digunakan.

---
*Dokumen ini diperbarui untuk melengkapi presentasi Ujian Akhir Semester (UAS).*
