# Dokumentasi Analisis Sistem Koperasi Simpan Pinjam

**Unit kompetensi:** J.620100.002.01 — Menganalisis Skalabilitas Perangkat Lunak  
**Kegiatan:** Praktik Demonstrasi Bagian #1  
**Objek:** Koperasi Simpan Pinjam “Sejahtera Mandiri”  
**Acuan:** Skenario pada Tugas Praktik 1 LSP UNPAM 2026/2027

## 1. Pendahuluan

Koperasi Sejahtera Mandiri mengelola Simpanan Pokok, Simpanan Wajib, Simpanan Sukarela, serta fasilitas pinjaman berbunga tetap dengan tenor bulanan. Pada skenario, pencatatan masih dilakukan melalui buku besar fisik dan lembar kerja terpisah. Cara tersebut berisiko menyebabkan inkonsistensi saldo simpanan, keterlambatan perhitungan sisa tagihan, dan hambatan pencarian riwayat pinjaman ketika jumlah anggota dan volume transaksi meningkat.

Solusi yang dirumuskan dalam skenario adalah aplikasi web client-server dengan lapisan presentasi PHP/Bootstrap dan basis data relasional MySQL InnoDB yang terindeks.

### 1.1 Tujuan
- Menentukan entitas, atribut, primary key, foreign key, dan relasi melalui ERD.
- Memetakan ERD ke struktur relasional melalui LRS.
- Menggambarkan interaksi sistem dengan entitas eksternal melalui DFD Level 0.
- Mendokumentasikan aturan bisnis, integritas, keamanan, dan pertimbangan skalabilitas.

### 1.2 Ruang Lingkup
- Autentikasi petugas/admin.
- Pendaftaran dan pengelolaan anggota.
- Pencatatan setoran simpanan pokok, wajib, dan sukarela.
- Pencatatan pinjaman dengan bunga tetap dan tenor bulanan.
- Pencatatan angsuran dan pembaruan sisa tagihan/status pinjaman.
- Penyajian saldo, riwayat, dan laporan.

### 1.3 Asumsi dan batasan
1. Hanya petugas/admin terautentikasi yang disebut sebagai pengguna sistem. Anggota diperlakukan sebagai subjek data/transaksi, bukan pengguna login langsung.
2. Skenario tidak menetapkan formula matematis bunga tetap, basis periode bunga, ataupun metode angsuran. Formula harus dikonfirmasi ke koperasi.
3. Denda keterlambatan disebut dalam uraian masalah, tetapi aturan nominal dan perhitungannya tidak ditentukan. Karena itu rancangan tidak mengarang rumus denda.
4. Skenario menjelaskan setoran simpanan; penarikan belum dimasukkan ke ruang lingkup.
5. Kebijakan pembayaran melebihi sisa tagihan perlu ditetapkan sebelum implementasi.

## 2. Aktor dan proses bisnis

### 2.1 Aktor

| Aktor | Peran |
|---|---|
| Petugas/Admin | Login, mendaftarkan anggota, mencatat setoran, memasukkan pinjaman, mencatat pembayaran angsuran, melihat saldo, status pinjaman, dan laporan. |
| Anggota | Pihak yang didaftarkan dan melakukan setoran, mengajukan pinjaman, serta membayar angsuran. Pencatatan dilakukan petugas/admin. |

### 2.2 Alur proses
1. Petugas/admin login menggunakan username dan password.
2. Petugas mendaftarkan anggota. Sistem menyimpan identitas dan nomor anggota unik.
3. Petugas mencatat setoran dengan memilih anggota dan jenis simpanan serta memasukkan jumlah. Saldo simpanan anggota bertambah otomatis.
4. Petugas mencatat pinjaman dengan memilih anggota dan memasukkan pokok, tingkat bunga, dan tenor. Sistem menyimpan total tagihan dan sisa tagihan awal berdasarkan formula resmi koperasi.
5. Petugas mencatat pembayaran terhadap pinjaman. Sisa tagihan berkurang otomatis.
6. Jika sisa tagihan mencapai Rp0, status pinjaman berubah menjadi LUNAS; jika belum, status tetap BERJALAN.

### 2.3 Aturan bisnis

| Kode | Aturan |
|---|---|
| BR-01 | Sistem hanya diakses petugas/admin dengan akun tervalidasi. |
| BR-02 | Setiap anggota mempunyai nomor anggota unik. |
| BR-03 | Setiap transaksi simpanan terkait satu anggota, satu jenis simpanan, dan satu petugas pencatat. |
| BR-04 | Jenis simpanan: Pokok, Wajib, atau Sukarela. |
| BR-05 | Setiap pinjaman terkait satu anggota dan satu petugas pencatat. |
| BR-06 | Pinjaman baru berstatus BERJALAN dan sisa tagihan awal sama dengan total tagihan. |
| BR-07 | Setiap pembayaran angsuran terkait satu pinjaman dan satu petugas pencatat. |
| BR-08 | Sisa tagihan tidak boleh negatif. Pembayaran melebihi sisa tagihan ditolak atau ditangani sesuai kebijakan yang disahkan. |
| BR-09 | Jika sisa tagihan = Rp0, status pinjaman otomatis menjadi LUNAS. |

## 3. Entity Relationship Diagram (ERD)

ERD terdapat pada `diagrams/ERD.drawio`. File dapat dibuka dan diedit di diagrams.net.

### 3.1 Entitas
- **users**: akun petugas/admin.
- **anggota**: identitas anggota dan ringkasan saldo simpanan.
- **jenis_simpanan**: master jenis Pokok, Wajib, Sukarela.
- **simpanan**: transaksi setoran.
- **pinjaman**: pokok, bunga, tenor, total tagihan, sisa tagihan, status.
- **angsuran**: transaksi pembayaran pinjaman.

### 3.2 Relasi

| Relasi | Kardinalitas | Makna |
|---|---:|---|
| users — anggota | 1:N | Satu petugas dapat memasukkan banyak anggota. |
| anggota — simpanan | 1:N | Satu anggota dapat memiliki banyak setoran. |
| jenis_simpanan — simpanan | 1:N | Satu jenis simpanan dipakai pada banyak transaksi. |
| users — simpanan | 1:N | Satu petugas dapat mencatat banyak setoran. |
| anggota — pinjaman | 1:N | Satu anggota dapat memiliki beberapa pinjaman. |
| users — pinjaman | 1:N | Satu petugas dapat mencatat banyak pinjaman. |
| pinjaman — angsuran | 1:N | Satu pinjaman dibayar melalui banyak angsuran. |
| users — angsuran | 1:N | Satu petugas dapat mencatat banyak pembayaran. |

## 4. Logical Record Structure (LRS)

LRS terdapat pada `diagrams/LRS.drawio`.

| Tabel | Atribut inti | Kunci |
|---|---|---|
| users | id_user, username, password_hash, nama_lengkap, peran, status_aktif, created_at, updated_at | PK id_user; UNIQUE username |
| anggota | id_anggota, nomor_anggota, nama_lengkap, alamat, no_telepon, tanggal_daftar, status_anggota, saldo_simpanan, id_user_input, created_at | PK id_anggota; UNIQUE nomor_anggota; FK id_user_input |
| jenis_simpanan | id_jenis_simpanan, kode_jenis, nama_jenis, keterangan, status_aktif | PK id_jenis_simpanan; UNIQUE kode_jenis dan nama_jenis |
| simpanan | id_simpanan, no_transaksi, id_anggota, id_jenis_simpanan, id_user, tanggal_transaksi, jumlah, keterangan, created_at | PK id_simpanan; UNIQUE no_transaksi; FK anggota, jenis_simpanan, users |
| pinjaman | id_pinjaman, no_pinjaman, id_anggota, id_user, tanggal_pinjaman, pokok_pinjaman, bunga_persen, tenor_bulan, total_bunga, total_tagihan, sisa_tagihan, status_pinjaman, created_at | PK id_pinjaman; UNIQUE no_pinjaman; FK anggota, users |
| angsuran | id_angsuran, no_transaksi, id_pinjaman, id_user, tanggal_bayar, jumlah_bayar, denda, keterangan, created_at | PK id_angsuran; UNIQUE no_transaksi; FK pinjaman, users |

### 4.1 Rekomendasi tipe data

| Atribut | Tipe data yang disarankan |
|---|---|
| ID dan foreign key | BIGINT UNSIGNED (jenis_simpanan dapat SMALLINT UNSIGNED) |
| Username | VARCHAR(50) |
| Password hash | VARCHAR(255) |
| Nama | VARCHAR(100) |
| Nomor anggota | VARCHAR(20) |
| Nomor transaksi/pinjaman | VARCHAR(30) |
| Tanggal | DATE atau DATETIME |
| Nominal uang | DECIMAL(15,2) |
| Persentase bunga | DECIMAL(7,4) |
| Tenor | SMALLINT UNSIGNED |
| Status | ENUM atau tabel referensi sesuai kebutuhan implementasi |

Kolom `anggota.saldo_simpanan` dan `pinjaman.sisa_tagihan` merupakan nilai ringkasan untuk mempercepat pembacaan. Riwayat transaksi tetap menjadi sumber audit. Pembaruan ringkasan harus atomik dengan transaksi terkait.

## 5. DFD Level 0 / Context Diagram

Context Diagram terdapat pada `diagrams/DFD-Level-0.drawio`.

**Entitas eksternal:** Petugas/Admin.  
**Proses tunggal:** Sistem Informasi Koperasi Simpan Pinjam Sejahtera Mandiri.

### 5.1 Arus data masuk
- Username dan password.
- Data anggota.
- Data setoran simpanan.
- Data pinjaman: pokok, bunga, tenor.
- Data pembayaran angsuran.
- Permintaan pencarian dan laporan.

### 5.2 Arus data keluar
- Status autentikasi.
- Nomor anggota dan nomor transaksi.
- Informasi saldo simpanan.
- Total tagihan, sisa tagihan, dan status pinjaman.
- Bukti transaksi dan laporan.

Anggota tidak digambarkan sebagai pengguna login langsung karena skenario menyatakan pencatatan transaksi dilakukan oleh petugas/admin. Jika kelak tersedia portal anggota, diagram perlu diperluas.

## 6. Validasi, keamanan, dan skalabilitas

### 6.1 Validasi dan integritas
- Username, nomor anggota, nomor pinjaman, dan nomor transaksi harus unik.
- Nominal setoran, pokok pinjaman, dan pembayaran angsuran harus lebih dari nol.
- Tenor harus lebih dari nol dan bunga tidak boleh negatif.
- Semua foreign key harus mengacu pada record induk yang valid.
- Pembayaran tidak boleh menyebabkan sisa tagihan negatif.
- Status LUNAS hanya jika sisa tagihan sama dengan nol.
- Data master yang sudah digunakan transaksi sebaiknya dinonaktifkan, bukan dihapus permanen.

### 6.2 Konsistensi transaksi
Penyimpanan transaksi dan pembaruan saldo dilakukan dalam satu transaksi database. Jika salah satu proses gagal, lakukan rollback. Untuk pembayaran angsuran, record pinjaman perlu dikunci saat membaca sisa tagihan (misalnya `SELECT ... FOR UPDATE` pada MySQL InnoDB), kemudian pembayaran disimpan dan sisa tagihan diperbarui.

### 6.3 Keamanan
- Simpan password dalam bentuk hash yang sesuai, misalnya Argon2id atau bcrypt.
- Gunakan prepared statement/parameterized query.
- Terapkan session management, logout, batas waktu sesi, dan proteksi CSRF.
- Terapkan hak akses sesuai peran ADMIN/KASIR.
- Catat petugas dan waktu pada transaksi untuk audit.
- Lakukan backup terjadwal dan uji pemulihan.

### 6.4 Skalabilitas
- Indeks pada foreign key dan kolom pencarian yang sering digunakan.
- Indeks pencarian dapat dipertimbangkan untuk nomor anggota, nomor transaksi, tanggal transaksi, tanggal bayar, status pinjaman, dan kombinasi anggota-tanggal.
- Gunakan pagination untuk daftar anggota dan riwayat transaksi.
- Batasi laporan dengan rentang tanggal dan optimalkan query agregasi.
- Pisahkan lapisan presentasi, logika aplikasi, dan akses data.
- Pantau waktu respons query, CPU/RAM, ukuran tabel, serta volume transaksi.
- Hindari indeks berlebihan karena menambah beban penyimpanan dan penulisan.

## 7. Skenario pengujian

| ID | Skenario | Hasil yang diharapkan |
|---|---|---|
| TC-01 | Login dengan kredensial valid | Petugas berhasil masuk. |
| TC-02 | Login dengan password salah | Login ditolak dan sesi tidak dibuat. |
| TC-03 | Mendaftarkan anggota baru | Nomor anggota unik; saldo awal Rp0. |
| TC-04 | Mencatat setoran | Transaksi tersimpan dan saldo bertambah sesuai nominal. |
| TC-05 | Mencatat pinjaman | Status BERJALAN; sisa tagihan awal sama dengan total tagihan. |
| TC-06 | Pembayaran sebagian | Sisa tagihan berkurang; status tetap BERJALAN. |
| TC-07 | Pelunasan | Sisa tagihan Rp0; status menjadi LUNAS. |
| TC-08 | Pembayaran melebihi sisa tagihan | Ditolak atau ditangani menurut kebijakan; saldo tidak negatif. |
| TC-09 | Referensi anggota/pinjaman tidak valid | Transaksi ditolak oleh validasi/FK. |
| TC-10 | Dua petugas membayar pinjaman yang sama secara bersamaan | Tidak terjadi pembaruan ganda atau sisa tagihan negatif. |

## 8. Kesimpulan

Rancangan menggunakan enam entitas utama: users, anggota, jenis_simpanan, simpanan, pinjaman, dan angsuran. ERD menjelaskan hubungan antartabel, LRS memetakan struktur relasional, dan DFD Level 0 memperlihatkan arus data antara petugas/admin dan sistem. Skalabilitas didukung melalui MySQL InnoDB, indeks yang tepat, pagination, transaksi atomik, validasi, dan audit.

## 9. Ketentuan yang harus dikonfirmasi

1. Bunga dihitung per bulan, per tahun, atau sekali untuk keseluruhan tenor?
2. Metode bunga yang digunakan flat atau metode lain?
3. Berapa nilai dan aturan denda keterlambatan?
4. Pembayaran melebihi sisa tagihan ditolak atau dicatat sebagai kelebihan bayar?
5. Apakah anggota boleh menarik simpanan?

Keputusan ini harus diperoleh dari koperasi sebelum implementasi perhitungan keuangan.
