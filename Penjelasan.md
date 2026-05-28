# DOKUMENTASI PROJECT UAS PEMROGRAMAN VISUAL
## SISTEM INFORMASI PENJUALAN OBAT SCM PADA APOTEK MAJU

**Dosen Pengampu:** [Nama Dosen]
**Mata Kuliah:** Pemrograman Visual
**Topik:** Supply Chain Management (SCM) Apotek

---

## 1. PENDAHULUAN (MODEL WATERFALL)
Sistem ini dibangun menggunakan metode **Waterfall** yang terdiri dari:
1. **Analisis Kebutuhan:** Mengumpulkan data kebutuhan Apotek Maju (Obat, Supplier, Penjualan).
2. **Desain Sistem:** Merancang Database MySQL (4 Tabel + 1 View) dan antarmuka aplikasi.
3. **Implementasi Kode:** Membuat kode program menggunakan VB.NET Framework 4.0 dan MySql.Data.
4. **Pengujian:** Mengetes setiap fitur CRUD (Create, Read, Update, Delete) dan proses transaksi perhitungan total penjualan.

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

### 3.1 `BukaKoneksi.vb` (Modul Koneksi)
*   **Penjelasan:** Ini adalah *jantung* dari aplikasi. Modul ini melakukan inisialisasi objek koneksi `MySqlConnection`. Jika status koneksi tertutup (`Closed`), maka aplikasi akan memanggil perintah `.Open()` untuk menyambung ke server lokal (XAMPP).
*   **Referensi:** Diadopsi langsung dari `bukakoneksi` milik project AplNilaiMhs.

### 3.2 `F_MenuUtama.vb`
*   **Penjelasan:** Menu navigasi utama. Menggunakan kontrol `MenuStrip` untuk mengarahkan pengguna ke setiap form fitur. Form ini di-set sebagai *Startup Form* agar pertama kali muncul saat aplikasi "Run".
*   **Tema:** Background color diatur ke hijau gelap (`0, 64, 0`) sesuai dengan identitas Apotek.

### 3.3 Form Master Data (CRUD)
Sistem memiliki 3 Form Master yang melakukan proses C-R-U-D. Alur standarnya:
1.  **Read (`isilist`):** Saat form `Load`, aplikasi melakukan `SELECT * FROM [tabel]` dan memindahkannya ke dalam kontrol `ListView`.
2.  **Create (`btnSave_Click`):** Memvalidasi TextBox, jika terisi penuh, menjalankan query `INSERT INTO` untuk menambah data.
3.  **Update (`btnEdit_Click`):** Saat baris di `ListView` diklik, data pindah ke TextBox. Pengguna mengubah data, klik EDIT, dan query `UPDATE [tabel] SET ...` dijalankan.
4.  **Delete (`btnDelete_Click`):** Memunculkan kotak konfirmasi (MsgBoxStyle.OkCancel). Jika OK, menjalankan query `DELETE FROM [tabel] WHERE...`.
5.  **Search (`caridata`):** Menerapkan event handler `TextChanged` pada kolom pencarian. Query yang dijalankan: `SELECT * FROM [tabel] WHERE id LIKE '%X%' OR nama LIKE '%X%'`. Fitur ini merespon ketikan secara *real-time*.

**Daftar Form Master:**
*   `FormSupplier.vb`: Untuk mengelola `tblsupplier`. (Diadaptasi dari logika `FormDosen`).
*   `FormKategoriObat.vb`: Untuk mengelola `tblkategori`. (Diadaptasi dari logika `FormMataKuliah`).
*   `FormObat.vb`: Mengelola `tblobat`. Memiliki `ComboBox` untuk satuan dan tombol pencarian popup. (Diadaptasi dari logika `FormMahasiswa`).

### 3.4 Form Transaksi (`FormTransaksiPenjualan.vb`)
*   **Penjelasan:** Mencatat transaksi hilir rantai pasok (SCM) yaitu dari Apotek ke Pelanggan.
*   **Proses Logika (`BtnProses_Click`):** Ketika obat dipilih, Harga Satuan dan Satuan akan terisi otomatis (readonly). Saat `jumlah` dimasukkan dan tombol `PROSES` ditekan, sistem melakukan perhitungan: `Total Harga = Jumlah * Harga Satuan`.
*   **Simpan Transaksi:** Menyimpan riwayat beserta nama pembeli dan status (`Lunas` / `Hutang`). (Diadaptasi penuh dari logika perhitungan `FormNilai`).

### 3.5 Form Popup Pencarian (`FormCariObat.vb` & `FormCariSupplier.vb`)
*   **Penjelasan:** Sebagai form *helper* untuk memudahkan input FK (Foreign Key). Saat di `FormTransaksi`, user menekan tombol cari obat -> `FormCariObat` muncul -> User klik obat di tabel -> Form pencarian tertutup dan data obat (Kode, Nama, Harga) langsung berpindah otomatis ke TextBox di `FormTransaksi`.

---

## 4. INTEGRASI TEMA SCM (Supply Chain Management)
Project ini mengintegrasikan alur sederhana dari proses rantai pasok:
1.  **Hulu (Upstream):** Terdapat data `Supplier`. Setiap Obat wajib memiliki kaitan dengan siapa suppliernya (`kd_supplier`).
2.  **Internal (Inventory):** Pengaturan jumlah persediaan barang dalam kolom `Stok` pada master Obat, beserta informasi Kategori produk.
3.  **Hilir (Downstream):** Modul Penjualan ke *end-user* / pembeli.

## 5. CARA MENJALANKAN (DEPLOYMENT)
1. Nyalakan server **Apache** dan **MySQL** dari XAMPP Control Panel.
2. Buka `localhost/phpmyadmin` di browser.
3. Buat database baru bernama `db_apotek_maju`.
4. Pilih menu **Import**, lalu masukkan file `db_apotek_maju.sql` yang ada di root folder project ini.
5. Buka project `SCMPenjualanObat.sln` di Microsoft Visual Studio (versi 2010 ke atas didukung).
6. Tekan `F5` atau tombol **Start Debugging** (ikon hijau).
7. Aplikasi Apotek Maju siap digunakan.

---
*Dokumen ini dibuat untuk melengkapi presentasi Ujian Akhir Semester (UAS).*
