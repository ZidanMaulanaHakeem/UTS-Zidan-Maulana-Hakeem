# UTS-Zidan-Maulana-Hakeem 12030123140327
# Voucher Kas Keluar --- Sistem Informasi Akuntansi

Proyek ini merupakan purwarupa web **Voucher Entri Transaksi Kas
Keluar** untuk tugas UTS mata kuliah Pemrograman/Coding, Program Studi
S1 Akuntansi, Fakultas Ekonomika dan Bisnis, Universitas Diponegoro.

## Tujuan

Membantu pengguna memasukkan transaksi jurnal kas keluar dengan validasi
debit-kredit pada antarmuka, menampilkan daftar voucher, serta
mendokumentasikan rancangan integrasi PostgreSQL/Supabase dan mekanisme
audit trail.

## Isi Repositori

-   `voucher_kas_keluar_transaksi_akuntansi.html` --- antarmuka
    purwarupa berbasis HTML, Tailwind CSS, dan Vanilla JavaScript.
-   `LOG_PROMPT.md` --- dokumentasi prompt yang digunakan untuk Gemini
    Canvas dan NotebookLM.
-   `README.md` --- dokumentasi proyek, fitur, batasan, dan cara
    penggunaan.

> Pastikan nama file HTML di repositori sama dengan nama file yang
> ditautkan di atas. Jika nama file diubah, perbarui bagian ini.

## Fitur Purwarupa

-   Formulir tanggal transaksi, nomor referensi, deskripsi, serta
    rincian akun, debit, dan kredit.
-   Penambahan dan penghapusan baris jurnal.
-   Perhitungan total debit dan kredit secara real-time.
-   Indikator keseimbangan jurnal dan validasi formulir di sisi
    frontend.
-   Aturan satu sisi per baris: debit atau kredit.
-   Daftar voucher yang disimpan pada mode lokal sesi browser.
-   Opsi konfigurasi koneksi Supabase melalui antarmuka aplikasi.
-   Dokumentasi rancangan tabel General Ledger dan trigger pengamanan
    transaksi berstatus `POSTED`.

## Cara Menjalankan

1.  Unduh atau clone repositori ini.
2.  Buka file `voucher_kas_keluar_transaksi_akuntansi.html` di browser,
    atau jalankan folder melalui ekstensi Live Server di Visual Studio
    Code.
3.  Uji formulir dengan mengisi tanggal, nomor referensi, deskripsi, dan
    minimal dua baris jurnal.
4.  Masukkan nominal debit dan kredit yang seimbang untuk melihat
    indikator validasi.
5.  Untuk menguji koneksi Supabase, siapkan proyek Supabase dan jalankan
    skema SQL yang sesuai terlebih dahulu. Masukkan URL proyek dan
    anon/publishable key melalui konfigurasi aplikasi apabila fitur
    tersebut tersedia.

## Skema Database yang Dirancang

Tabel inti yang didokumentasikan:

-   `chart_of_accounts` --- daftar akun.
-   `journal_headers` --- informasi umum transaksi, status posting, dan
    metadata audit.
-   `journal_lines` --- rincian debit dan kredit.
-   `inventory_items` dan `inventory_transactions` --- rancangan
    tambahan untuk modul persediaan pada pembahasan tugas.

Relasi utama:

-   Satu `journal_headers` memiliki banyak `journal_lines`.
-   Satu `chart_of_accounts` dapat digunakan pada banyak
    `journal_lines`.

## Audit Trail dan Immutability

Rancangan audit trail mencakup `created_by`, `created_at`, `posted_at`,
dan `hash_checksum`. Transaksi berstatus `POSTED` dirancang agar tidak
dapat diubah atau dihapus langsung. Koreksi dilakukan melalui jurnal
pembalik (*reversing entry/storno*) yang disimpan sebagai transaksi baru
dan merujuk pada transaksi asal.

**Penting:** SQL yang ditampilkan dalam dokumentasi atau modal aplikasi
merupakan rancangan yang perlu diuji pada database target. Pastikan
trigger diterapkan pada header dan detail, proses posting memvalidasi
keseimbangan jurnal di database, serta hak akses Supabase dikonfigurasi
dengan benar.

## Batasan yang Diketahui

-   Tanpa koneksi database, data dapat disimpan secara lokal di sesi
    browser dan tidak menjadi penyimpanan permanen.
-   Koneksi Supabase harus dikonfigurasi dan skema tabel harus tersedia
    agar data dapat disimpan di database.
-   Validasi keseimbangan pada frontend tidak menggantikan validasi
    backend/database.
-   Jika kode menggunakan `btoa()` untuk checksum, fungsi itu adalah
    encoding Base64, bukan SHA-256. Jangan menganggapnya sebagai
    verifikasi integritas kriptografis.
-   Integrasi jurnal nominal otomatis dari transaksi persediaan belum
    dapat dianggap aktif kecuali pemetaan akun, perhitungan nilai, dan
    pencatatan jurnal benar-benar telah diimplementasikan serta diuji.

## Pengujian yang Disarankan

1.  Formulir tidak lengkap --- tombol simpan harus tetap nonaktif atau
    transaksi ditolak.
2.  Jurnal tidak seimbang --- transaksi tidak boleh diposting.
3.  Baris jurnal hanya memiliki debit atau kredit positif, tidak
    keduanya.
4.  Nomor referensi duplikat --- database harus menolak sesuai
    constraint unik.
5.  Transaksi `POSTED` --- percobaan `UPDATE` atau `DELETE` pada header
    maupun detail harus ditolak.
6.  Jurnal pembalik --- dibuat sebagai transaksi baru dengan nilai
    debit-kredit berlawanan dan referensi ke transaksi asal.
7.  Checksum --- hitung dan verifikasi ulang hash SHA-256 di backend
    jika fitur tersebut telah diimplementasikan.

## Catatan Akademik

Proyek ini adalah purwarupa untuk mendemonstrasikan rancangan sistem
informasi akuntansi. Jelaskan secara jujur fitur yang sudah diuji dan
fitur yang masih berupa rancangan. Jangan mencantumkan kredensial
rahasia, service-role key, atau password ke repositori publik.
