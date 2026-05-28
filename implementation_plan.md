# Sistem Informasi Penjualan Obat - Apotek Maju (SCM)

## Deskripsi Project
Membuat **Sistem Informasi Penjualan Obat Berbasis Model WaterFall** pada **Apotek Maju** dengan tema **SCM (Supply Chain Management)**. Project dibuat menggunakan VB.NET Framework 4 + MySQL/PHPMyAdmin, dengan pola CRUD yang **mereferensi langsung** dari project latihan `AplNilaiMhs`.

## Analisis Referensi (AplNilaiMhs)

Pola kode yang akan di-adopsi:

| Komponen AplNilaiMhs | Adaptasi SCMPenjualanObat |
|---|---|
| `BukaKoneksi.vb` (Module koneksi MySQL) | `BukaKoneksi.vb` → koneksi ke `db_apotek_maju` |
| `F_MenuUtama` (MenuStrip navigation) | `F_MenuUtama` → Menu navigasi Apotek |
| `FormDosen` (CRUD sederhana) | `FormSupplier` (CRUD Supplier) |
| `FormMataKuliah` (CRUD sederhana) | `FormObat` (CRUD Data Obat) |
| `FormMahasiswa` (CRUD + RadioButton + CheckBox) | `FormKategoriObat` (CRUD Kategori Obat) |
| `FormNilai` (Transaksi + ComboBox + proses logika) | `FormTransaksiPenjualan` (Transaksi Penjualan + perhitungan total) |
| `FormCariMhs`, `FormCariDosen`, `FormCariMtk` (Form pencarian popup) | `FormCariObat`, `FormCariSupplier` (Form pencarian popup) |
| `querynilai` (SQL VIEW join tabel) | `querypenjualan` (SQL VIEW join tabel) |

## Design Database: `db_apotek_maju`

### Tabel-tabel:

**1. `tblsupplier`** — Data Supplier Obat
| Field | Type | Keterangan |
|---|---|---|
| `kd_supplier` | VARCHAR(10) PK | Kode Supplier |
| `nm_supplier` | VARCHAR(50) | Nama Supplier |
| `alamat` | VARCHAR(100) | Alamat |
| `telp` | VARCHAR(15) | Telepon |

**2. `tblkategori`** — Kategori Obat
| Field | Type | Keterangan |
|---|---|---|
| `kd_kategori` | VARCHAR(10) PK | Kode Kategori |
| `nm_kategori` | VARCHAR(30) | Nama Kategori |
| `keterangan` | VARCHAR(50) | Keterangan |

**3. `tblobat`** — Data Obat
| Field | Type | Keterangan |
|---|---|---|
| `kd_obat` | VARCHAR(10) PK | Kode Obat |
| `nm_obat` | VARCHAR(50) | Nama Obat |
| `kd_kategori` | VARCHAR(10) FK | Kode Kategori |
| `kd_supplier` | VARCHAR(10) FK | Kode Supplier |
| `satuan` | VARCHAR(20) | Satuan (tablet/botol/strip/dll) |
| `harga` | DOUBLE | Harga Satuan |
| `stok` | INT | Jumlah Stok |

**4. `tblpenjualan`** — Transaksi Penjualan
| Field | Type | Keterangan |
|---|---|---|
| `id_penjualan` | INT(11) PK AI | ID Penjualan |
| `tgl_jual` | DATE | Tanggal Penjualan |
| `kd_obat` | VARCHAR(10) FK | Kode Obat |
| `jumlah` | INT | Jumlah Beli |
| `harga_satuan` | DOUBLE | Harga Per Satuan |
| `total_harga` | DOUBLE | Total = jumlah × harga_satuan |
| `nama_pembeli` | VARCHAR(50) | Nama Pembeli |
| `keterangan` | VARCHAR(50) | Keterangan |

**5. `querypenjualan`** (SQL VIEW) — Join semua tabel untuk laporan

---

## Proposed Changes

### Komponen 1: Database SQL

#### [NEW] [db_apotek_maju.sql](file:///c:/MAWAN%20KULIAH/SM4/Pemrograman%20Visual/PROJECT%20J/Pemrograman%20Visual/SCMPenjualanObat/db_apotek_maju.sql)
- SQL script untuk membuat database `db_apotek_maju` beserta semua tabel
- Sample data untuk demo
- SQL VIEW `querypenjualan` (join tabel obat, supplier, kategori, penjualan)

---

### Komponen 2: Module Koneksi

#### [NEW] [BukaKoneksi.vb](file:///c:/MAWAN%20KULIAH/SM4/Pemrograman%20Visual/PROJECT%20J/Pemrograman%20Visual/SCMPenjualanObat/SCMPenjualanObat/BukaKoneksi.vb)
- Pola sama persis dengan referensi, hanya ganti database ke `db_apotek_maju`

---

### Komponen 3: Menu Utama & Keamanan

#### [NEW] FormLogin.vb + FormLogin.Designer.vb
- Form login dengan hardcode credentials (salsa/salsa123, dinda/dinda123, juan/juan123)
- Di-set sebagai **Startup Form**.
- Jika sukses login, akan membuka `F_MenuUtama` dan form login disembunyikan.

#### [NEW] F_MenuUtama.vb + F_MenuUtama.Designer.vb
- MenuStrip: **Master Data** (Obat, Kategori Obat, Supplier) → **Transaksi** (Penjualan Obat) → **Cari Data** (Obat, Supplier) → **Laporan** → **Keluar**
- Tema warna hijau apotek/farmasi (ControlDarkDark → warna hijau tua gelap)
- Judul: "SISTEM INFORMASI PENJUALAN OBAT - APOTEK MAJU"

---

### Komponen 4: Form CRUD Master Data

#### [NEW] FormSupplier.vb + FormSupplier.Designer.vb
- CRUD untuk `tblsupplier` — pola **mirip FormDosen** (TextBox sederhana)
- Field: Kode Supplier, Nama Supplier, Alamat, Telepon
- Button: REFRESH, SAVE, EDIT, DELETE, EXIT
- ListView + Pencarian

#### [NEW] FormKategoriObat.vb + FormKategoriObat.Designer.vb
- CRUD untuk `tblkategori` — pola **mirip FormMataKuliah** (TextBox sederhana)
- Field: Kode Kategori, Nama Kategori, Keterangan
- Button: REFRESH, SAVE, EDIT, DELETE, EXIT
- ListView + Pencarian

#### [NEW] FormObat.vb + FormObat.Designer.vb
- CRUD untuk `tblobat` — pola **mirip FormMahasiswa** (lebih kompleks, ada ComboBox/RadioButton)
- Field: Kode Obat, Nama Obat, Kategori (via Button Cari, TextBox ReadOnly), Supplier (via Button Cari, TextBox ReadOnly), Satuan (ComboBox: Tablet/Botol/Strip/Kapsul/Tube), Harga, Stok
- Button: REFRESH, SAVE, EDIT, DELETE, EXIT, Cari Supplier, Cari Kategori
- ListView + Pencarian

---

### Komponen 5: Form Transaksi

#### [NEW] FormTransaksiPenjualan.vb + FormTransaksiPenjualan.Designer.vb
- Pola **mirip FormNilai** (Transaksi + proses kalkulasi)
- Field: Tanggal (DateTimePicker), Kode Obat (via Button Cari), Nama Obat (readonly), Satuan (readonly), Harga Satuan (readonly), Jumlah (input), Total Harga (auto-kalkulasi), Nama Pembeli, Keterangan
- Proses: Total = Jumlah × Harga Satuan (mirip prosesnilai)
- Cetak Struk: Menggunakan `PrintDocument` dan `PrintPreviewDialog` bawaan .NET. (Cetak format kasir apotek)
- Button: REFRESH, PROSES, SAVE, EDIT, DELETE, CETAK, EXIT, Cari Obat
- ListView menampilkan semua transaksi (query view) terurut dari ID terkecil (`ASC`)

---

### Komponen 6: Form Pencarian Popup

#### [NEW] FormCariObat.vb + FormCariObat.Designer.vb
- Pola **mirip FormCariMhs** — pencarian obat, hasil kembali ke FormTransaksiPenjualan

#### [NEW] FormCariSupplier.vb + FormCariSupplier.Designer.vb
- Pola **mirip FormCariDosen** — pencarian supplier, hasil kembali ke FormObat

#### [NEW] FormCariKategori.vb + FormCariKategori.Designer.vb
- Pola **mirip FormCariDosen** — pencarian kategori, hasil kembali ke FormObat

---

### Komponen 7: Project Configuration

#### [MODIFY] [SCMPenjualanObat.vbproj](file:///c:/MAWAN%20KULIAH/SM4/Pemrograman%20Visual/PROJECT%20J/Pemrograman%20Visual/SCMPenjualanObat/SCMPenjualanObat/SCMPenjualanObat.vbproj)
- Tambah reference MySql.Data
- Tambah semua form baru ke project

#### [MODIFY] App.config
- Tambah konfigurasi sesuai referensi

#### [MODIFY] My Project files
- Update `Application.myapp` → MainForm = FormLogin
- Update `Application.Designer.vb` → Startup form FormLogin
- Update `AssemblyInfo.vb` → nama project SCMPenjualanObat

---

### Komponen 8: Dokumentasi Penjelasan

#### [NEW] Penjelasan.md
- Penjelasan lengkap semua code dan program untuk presentasi UAS
- Penjelasan per-form dan per-fungsi CRUD
- Panduan setup database dan cara menjalankan

---

## Tema Design

Semua form menggunakan tema **Apotek/Farmasi**:
- Panel background: **Hijau tua gelap** (`Color.FromArgb(0, 64, 0)` atau `Color.FromArgb(0, 80, 60)`)
- Label judul: **Font Bold 14pt**, ForeColor putih
- Label field: **Font 11pt**, ForeColor putih
- Button: **BackColor putih**, Font Bold
- ListView: FullRowSelect, GridLines

> [!IMPORTANT]
> Pola CRUD (Save/Edit/Delete/Refresh/Cari) mengikuti **100% identik** dengan referensi AplNilaiMhs — hanya nama tabel, field, dan konteks data yang disesuaikan ke domain Apotek.

---

## Verification Plan

### Manual Verification
1. Buka project di Visual Studio → pastikan semua form terload tanpa error
2. Import `db_apotek_maju.sql` ke PHPMyAdmin → pastikan semua tabel + data + view berhasil
3. Jalankan project → test semua CRUD: Save, Edit, Delete, Refresh, Cari
4. Test transaksi penjualan → kalkulasi total otomatis
5. Test form pencarian popup → data kembali ke form parent

## Open Questions

> [!NOTE]
> **Tidak ada pertanyaan kritis** — semua requirement sudah jelas. Project akan langsung dibuat mengikuti pola referensi AplNilaiMhs yang sudah dianalisis secara detail.
