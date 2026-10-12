# Log Prompt AI --- UTS Coding Akuntansi

Dokumen ini mencatat prompt yang digunakan dalam proses penyusunan
purwarupa Voucher Entri Transaksi Kas Keluar dan evaluasi logika sistem.

## 1. Gemini Canvas --- Pembuatan Purwarupa HTML

### Tujuan

Membuat purwarupa formulir Voucher Entri Transaksi Kas Keluar
menggunakan HTML5, Tailwind CSS melalui CDN, dan Vanilla JavaScript,
dengan struktur data yang mengacu pada rancangan General Ledger
PostgreSQL.

### Prompt yang digunakan

> Buat prototipe aplikasi web Voucher Entri Transaksi Kas Keluar
> menggunakan HTML5, Tailwind CSS CDN, dan Vanilla JavaScript.
> Integrasikan rancangan dengan struktur database PostgreSQL, yaitu
> `chart_of_accounts` (`account_id`, `account_code`, `account_name`,
> `normal_balance`, `report_class`), `journal_headers` (`header_id`,
> `transaction_date`, `reference_no`, `description`, `posting_status`),
> dan `journal_lines` (`line_id`, `header_id`, `account_id`, `debit`,
> `credit`).
>
> Persyaratan:
>
> 1.  Buat formulir tanggal transaksi, nomor bukti, deskripsi, serta
>     tabel rincian akun, debit, dan kredit.
> 2.  Sediakan fitur tambah dan hapus baris jurnal serta kalkulasi total
>     debit-kredit secara real-time.
> 3.  Aktifkan tombol Submit hanya jika data wajib terisi, minimal dua
>     baris valid, setiap baris hanya memiliki salah satu sisi debit
>     atau kredit bernilai positif, dan total debit sama dengan total
>     kredit serta lebih besar dari nol.
> 4.  Siapkan integrasi backend PostgreSQL/Supabase untuk menyimpan
>     header dan seluruh baris jurnal secara atomik.
> 5.  Terapkan validasi backend, foreign key akun, dan pencegahan
>     perubahan atau penghapusan jurnal berstatus `POSTED` sesuai
>     rancangan audit trail.
> 6.  Tambahkan `created_by`, `created_at`, `posted_at`,
>     `hash_checksum`, serta mekanisme jurnal pembalik untuk menjaga
>     keterlacakan transaksi.
> 7.  Jika menambahkan modul persediaan, gunakan `inventory_items` dan
>     `inventory_transactions` dari Soal 2. Pertahankan logika trigger
>     yang memperbarui stok dan menolak stok negatif. Jangan menganggap
>     modul persediaan sudah terhubung otomatis dengan jurnal nominal
>     sebelum pemetaan akun dan pencatatan jurnal diimplementasikan.
> 8.  Gunakan desain responsif dan profesional. Pisahkan file frontend,
>     konfigurasi backend, dan SQL jika diperlukan.
> 9.  Berikan kode lengkap, petunjuk konfigurasi Supabase, skenario
>     pengujian, dan jelaskan fitur yang sudah berjalan versus yang
>     masih berupa prototipe. Jangan mengklaim data tersimpan jika
>     integrasi belum diterapkan.

### Hasil yang diharapkan

Purwarupa menampilkan formulir jurnal dinamis, kalkulasi debit-kredit,
indikator keseimbangan, daftar voucher, serta rancangan koneksi ke
Supabase.

### Catatan verifikasi

Tangkapan layar Gemini Canvas dan file HTML digunakan sebagai
dokumentasi proses. Tampilan antarmuka tidak dengan sendirinya
membuktikan bahwa seluruh fungsi backend, penyimpanan atomik, audit
trail, atau checksum telah berjalan. Fitur harus diuji pada lingkungan
database yang sebenarnya.

## 2. NotebookLM --- Evaluasi Logika dan Kepatuhan Akuntansi

### Tujuan

Meminta NotebookLM meninjau rancangan General Ledger, trigger
persediaan, penanganan concurrency, formulir voucher, dan kebutuhan
audit trail dengan merujuk pada sumber yang dimasukkan ke notebook.

### Sumber yang direncanakan/digunakan

1.  Naskah UTS Coding Akuntansi FEB Undip --- konteks soal dan
    persyaratan tugas.
2.  SQL Soal 1 --- rancangan `chart_of_accounts`, `journal_headers`, dan
    `journal_lines`.
3.  SQL Soal 2 --- rancangan `inventory_items`,
    `inventory_transactions`, trigger mutasi stok, dan pengendalian
    concurrency.
4.  Referensi resmi SAK/PSAK yang dapat diakses secara sah --- digunakan
    untuk memeriksa ketentuan akuntansi yang relevan.
5.  Kode HTML formulir web --- digunakan untuk mengevaluasi validasi
    frontend dan rancangan integrasi.

> Sesuaikan daftar ini dengan sumber yang benar-benar dimasukkan ke
> NotebookLM. Jangan menyatakan sumber sudah digunakan jika belum
> diunggah ke notebook.

### Prompt yang digunakan

> Berdasarkan seluruh sumber yang tersedia, evaluasi rancangan sistem
> informasi akuntansi untuk UTS Coding yang terdiri dari struktur
> database General Ledger pada Soal 1, otomasi persediaan menggunakan
> PostgreSQL pada Soal 2, dan formulir Voucher Entri Transaksi Kas
> Keluar pada Soal 3.1(a).
>
> Periksa:
>
> 1.  Konsistensi relasi database dan constraint debit-kredit.
> 2.  Ketepatan logika trigger persediaan dan pencegahan stok negatif.
> 3.  Penanganan transaksi bersamaan dan risiko overselling.
> 4.  Validasi formulir jurnal serta pemisahan validasi frontend dan
>     backend.
> 5.  Pengakuan transaksi pengeluaran kas, klasifikasi kas dan setara
>     kas, dan ketentuan akuntansi yang relevan berdasarkan dokumen
>     standar yang tersedia.
> 6.  Kebutuhan audit trail dan penguncian jurnal berstatus `POSTED`.
>
> Sajikan temuan dalam tabel berisi aspek, bukti dari sumber, hasil
> evaluasi, risiko, dan rekomendasi. Bedakan ketentuan yang didukung
> dokumen standar dengan rekomendasi pengendalian internal. Cantumkan
> referensi sumber untuk setiap temuan. Jangan mengarang kutipan PSAK,
> jangan menganggap kode sudah diuji jika tidak ada bukti pengujian, dan
> nyatakan jika informasi yang diperlukan belum tersedia.

### Ringkasan temuan yang didokumentasikan

-   Formulir memvalidasi kelengkapan input, aturan satu sisi per baris,
    dan keseimbangan debit-kredit pada frontend.
-   Validasi frontend tidak menggantikan validasi pada database/backend.
-   Trigger persediaan dirancang untuk memperbarui kuantitas stok dan
    menolak stok negatif.
-   Penguncian baris dengan `SELECT ... FOR UPDATE` digunakan untuk
    mengurangi risiko overselling.
-   Rancangan audit trail memerlukan penguncian transaksi `POSTED`,
    jurnal pembalik, dan metadata transaksi.
-   Klasifikasi aktivitas arus kas belum tersedia pada formulir apabila
    field operasi, investasi, dan pendanaan belum ditambahkan.
-   `btoa()` merupakan encoding Base64, bukan SHA-256. Implementasi
    checksum kriptografis perlu dilakukan secara terpisah.

Ringkasan ini harus dipahami sebagai dokumentasi hasil evaluasi
rancangan. Klaim kepatuhan akhir harus mengikuti sumber yang benar-benar
tersedia dan pengujian yang benar-benar dilakukan.

## 3. Catatan Integritas Dokumentasi

-   Simpan prompt dan hasil AI secara terpisah jika dosen meminta log
    lengkap.
-   Tambahkan tanggal penggunaan, tangkapan layar, atau tautan
    percakapan bila tersedia.
-   Jangan mengarang hasil pengujian, referensi PSAK, atau bukti
    integrasi.
-   Sebutkan secara eksplisit bagian yang masih berupa rancangan atau
    prototipe.
