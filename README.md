# Panduan Lengkap Belajar sqlc: Dari Dasar Hingga Mahir

![SQLC Logo](https://sqlc.dev/logo.png)

Selamat datang di panduan komprehensif untuk mempelajari `sqlc`, sebuah _compiler_ SQL yang menghasilkan kode Go (_type-safe_) dari kueri SQL Anda. Panduan ini dirancang untuk membawa Anda dari pemahaman dasar hingga penggunaan fitur-fitur canggih `sqlc`, semuanya dalam Bahasa Indonesia.

Dokumen ini bertujuan untuk menjadi sumber daya utama Anda dalam menguasai `sqlc`. Kami akan membahas filosofi di balik `sqlc`, instalasi, konfigurasi, penulisan kueri, penggunaan kode yang dihasilkan, praktik terbaik, dan banyak lagi.

**Target Audiens:**
Panduan ini ditujukan untuk pengembang Go yang ingin meningkatkan cara mereka berinteraksi dengan basis data SQL. Baik Anda seorang pemula dalam pengembangan Go atau sudah berpengalaman, panduan ini akan memberikan wawasan berharga tentang bagaimana `sqlc` dapat menyederhanakan dan membuat lapisan akses data Anda lebih aman dan efisien.

## Daftar Isi

1.  [Pendahuluan: Apa itu sqlc?](#1-pendahuluan-apa-itu-sqlc)
    *   [Filosofi sqlc](#filosofi-sqlc)
    *   [Mengapa Menggunakan sqlc? Keuntungannya](#mengapa-menggunakan-sqlc-keuntungannya)
    *   [sqlc vs ORM (Object-Relational Mapper)](#sqlc-vs-orm-object-relational-mapper)
    *   [sqlc vs Konstruktor Kueri (Query Builders)](#sqlc-vs-konstruktor-kueri-query-builders)
2.  [Prasyarat](#2-prasyarat)
    *   [Pemahaman Dasar Go](#pemahaman-dasar-go)
    *   [Pemahaman Dasar SQL](#pemahaman-dasar-sql)
    *   [Instalasi Go](#instalasi-go)
    *   [Akses ke Basis Data SQL](#akses-ke-basis-data-sql)
3.  [Instalasi sqlc](#3-instalasi-sqlc)
    *   [Menggunakan Go](#menggunakan-go)
    *   [Menggunakan Homebrew (macOS/Linux)](#menggunakan-homebrew-macoslinux)
    *   [Menggunakan Scoop (Windows)](#menggunakan-scoop-windows)
    *   [Mengunduh Biner](#mengunduh-biner)
    *   [Menggunakan Docker](#menggunakan-docker)
    *   [Verifikasi Instalasi](#verifikasi-instalasi)
4.  [Memulai dengan sqlc: Tutorial Cepat](#4-memulai-dengan-sqlc-tutorial-cepat)
    *   [Inisialisasi Proyek Go](#inisialisasi-proyek-go)
    *   [Membuat File Konfigurasi `sqlc.yaml`](#membuat-file-konfigurasi-sqlcyaml)
    *   [Menulis Skema Basis Data (`schema.sql`)](#menulis-skema-basis-data-schemasql)
    *   [Menulis Kueri SQL (`query.sql`)](#menulis-kueri-sql-querysql)
    *   [Menghasilkan Kode Go](#menghasilkan-kode-go)
    *   [Menggunakan Kode yang Dihasilkan dalam Aplikasi Go](#menggunakan-kode-yang-dihasilkan-dalam-aplikasi-go)
    *   [Menjalankan Aplikasi](#menjalankan-aplikasi)
5.  [Konfigurasi sqlc: `sqlc.yaml` Secara Mendalam](#5-konfigurasi-sqlc-sqlcyaml-secara-mendalam)
    *   [Struktur Dasar `sqlc.yaml`](#struktur-dasar-sqlcyaml)
    *   [Opsi Konfigurasi Utama](#opsi-konfigurasi-utama)
        *   [`version`](#version)
        *   [`sql`](#sql-package-configuration)
        *   [`plugins`](#plugins)
        *   [`rules`](#rules)
    *   [Konfigurasi Paket (`package`)](#konfigurasi-paket-package)
        *   [`name`](#name)
        *   [`path`](#path)
        *   [`engine`](#engine)
        *   [`schema`](#schema)
        *   [`queries`](#queries)
        *   [`emit_json_tags`](#emit_json_tags)
        *   [`emit_prepared_queries`](#emit_prepared_queries)
        *   [`emit_interface`](#emit_interface)
        *   [`emit_exact_table_names`](#emit_exact_table_names)
        *   [`emit_empty_slices`](#emit_empty_slices)
        *   [`emit_exported_queries`](#emit_exported_queries)
        *   [`emit_result_struct_pointers`](#emit_result_struct_pointers)
        *   [`emit_params_struct_pointers`](#emit_params_struct_pointers)
        *   [`emit_methods_with_db_argument`](#emit_methods_with_db_argument)
        *   [`json_tags_case_style`](#json_tags_case_style)
        *   [`output_db_file_name`](#output_db_file_name)
        *   [`output_models_file_name`](#output_models_file_name)
        *   [`output_querier_file_name`](#output_querier_file_name)
        *   [`output_copyfrom_file_name`](#output_copyfrom_file_name)
        *   [`overrides`](#overrides)
        *   [`rules`](#package-level-rules)
        *   [`plugins`](#package-level-plugins)
        *   [`rename`](#rename)
        *   [`sql_package`](#sql_package)
        *   [`strict_order_by`](#strict_order_by)
    *   [Contoh Konfigurasi Lanjutan](#contoh-konfigurasi-lanjutan)
6.  [Menulis Skema Basis Data](#6-menulis-skema-basis-data)
    *   [File `schema.sql`](#file-schemasql)
    *   [Sintaks DDL (Data Definition Language)](#sintaks-ddl-data-definition-language)
    *   [Tipe Data yang Didukung](#tipe-data-yang-didukung)
    *   [Contoh Skema untuk PostgreSQL](#contoh-skema-untuk-postgresql)
    *   [Contoh Skema untuk MySQL](#contoh-skema-untuk-mysql)
    *   [Contoh Skema untuk SQLite](#contoh-skema-untuk-sqlite)
    *   [Mengelola Migrasi Skema](#mengelola-migrasi-skema)
7.  [Menulis Kueri SQL untuk sqlc](#7-menulis-kueri-sql-untuk-sqlc)
    *   [File `query.sql`](#file-querysql)
    *   [Anotasi Kueri sqlc](#anotasi-kueri-sqlc)
        *   `-- name: <nama_fungsi> :<jenis_kueri>`
        *   Jenis Kueri: `:one`, `:many`, `:exec`, `:execrows`, `:copyfrom`
    *   [Parameter dalam Kueri](#parameter-dalam-kueri)
        *   `sqlc.arg(<nama_parameter>)`
        *   `sqlc.slice(<nama_parameter>)`
        *   `@<nama_parameter>` (untuk beberapa engine)
    *   [Mengembalikan Nilai](#mengembalikan-nilai)
        *   `SELECT *`
        *   `SELECT <kolom1>, <kolom2>`
        *   Klausul `RETURNING` (PostgreSQL)
    *   [Struktur dan Tipe Data yang Dihasilkan](#struktur-dan-tipe-data-yang-dihasilkan)
    *   [Contoh Kueri Umum](#contoh-kueri-umum)
        *   Membuat Data (Create)
        *   Membaca Data Tunggal (Read One)
        *   Membaca Banyak Data (Read Many)
        *   Memperbarui Data (Update)
        *   Menghapus Data (Delete)
    *   [Menggunakan Fungsi SQL dan Ekspresi](#menggunakan-fungsi-sql-dan-ekspresi)
    *   [Komentar SQL](#komentar-sql)
8.  [Menghasilkan Kode Go dengan `sqlc generate`](#8-menghasilkan-kode-go-dengan-sqlc-generate)
    *   [Perintah Dasar](#perintah-dasar)
    *   [Struktur Direktori Hasil](#struktur-direktori-hasil)
        *   `db.go`
        *   `models.go`
        *   `<query_file_name>.sql.go`
        *   `copyfrom.go` (jika menggunakan `:copyfrom`)
    *   [Memahami Kode yang Dihasilkan](#memahami-kode-yang-dihasilkan)
        *   File `models.go`: Struct untuk tabel dan hasil kueri
        *   File `<query_file_name>.sql.go`: Fungsi-fungsi Go untuk kueri Anda
        *   File `db.go`: Interface `Querier` dan struct `Queries`
9.  [Menggunakan Kode yang Dihasilkan](#9-menggunakan-kode-yang-dihasilkan)
    *   [Mengimpor Paket yang Dihasilkan](#mengimpor-paket-yang-dihasilkan)
    *   [Membuka Koneksi Basis Data](#membuka-koneksi-basis-data)
        *   Menggunakan `database/sql`
        *   Contoh dengan PostgreSQL (`pgx`, `lib/pq`)
        *   Contoh dengan MySQL (`go-sql-driver/mysql`)
        *   Contoh dengan SQLite (`mattn/go-sqlite3`)
    *   [Membuat Instance `Queries`](#membuat-instance-queries)
    *   [Memanggil Fungsi Kueri](#memanggil-fungsi-kueri)
        *   Meneruskan `context.Context`
        *   Meneruskan Argumen Kueri
        *   Menangani Hasil Kueri
    *   [Penanganan Error](#penanganan-error)
        *   `sql.ErrNoRows`
        *   Error Basis Data Spesifik
    *   [Bekerja dengan Transaksi](#bekerja-dengan-transaksi)
        *   Memulai Transaksi
        *   Menggunakan `*Queries` dengan `*sql.Tx`
        *   Commit dan Rollback
        *   Pola Transaksi Umum
    *   [Contoh Aplikasi Lengkap Sederhana](#contoh-aplikasi-lengkap-sederhana)
10. [Fitur Lanjutan sqlc](#10-fitur-lanjutan-sqlc)
    *   [Nullable Types (Tipe Data Null)](#nullable-types-tipe-data-null)
        *   Bagaimana sqlc menanganinya
        *   Menggunakan `sql.NullString`, `sql.NullInt64`, dll.
    *   [Tipe JSON dan JSONB (PostgreSQL)](#tipe-json-dan-jsonb-postgresql)
        *   Mapping ke `[]byte`
        *   Menggunakan `json.RawMessage`
        *   Override Tipe untuk Struct Kustom
    *   [Array Types (Tipe Data Array - PostgreSQL)](#array-types-tipe-data-array---postgresql)
        *   Mapping ke slice Go (misalnya `[]int3E`, `[]string`)
        *   Menggunakan `pq.Array` untuk tipe yang lebih kompleks
    *   [Enum Types (PostgreSQL dan MySQL)](#enum-types-postgresql-dan-mysql)
        *   Pemetaan otomatis ke tipe string Go
        *   Membuat tipe Go kustom untuk Enum
    *   [UUID Types](#uuid-types)
        *   Pemetaan ke `[16]byte`, `string`, atau tipe UUID kustom
    *   [Overrides Tipe Data (`overrides`)](#overrides-tipe-data-overrides)
        *   Kapan dan Mengapa Menggunakannya
        *   Sintaks dalam `sqlc.yaml`
        *   Contoh: Memetakan `TIMESTAMPTZ` ke `time.Time`
        *   Contoh: Menggunakan `pgtype` dari `jackc/pgx`
        *   Contoh: Tipe Kustom untuk Data Domain Spesifik
    *   [Mengganti Nama Struct dan Field (`rename`)](#mengganti-nama-struct-dan-field-rename)
        *   Sintaks dalam `sqlc.yaml`
        *   Mengganti nama tabel menjadi nama struct Go yang berbeda
        *   Mengganti nama kolom menjadi nama field struct Go yang berbeda
    *   [Query dengan `IN` Clause dan `sqlc.slice`](#query-dengan-in-clause-dan-sqlcslice)
    *   [Perintah `:copyfrom` (PostgreSQL)](#perintah-copyfrom-postgresql)
        *   Untuk Bulk Insert yang Efisien
        *   Cara Menggunakan dan Kode yang Dihasilkan
    *   [Menggunakan `sqlc.embed`](#menggunakan-sqlcembed)
        *   Untuk menyematkan struct dalam hasil kustom
    *   [Fungsi `left_join` (Eksperimental)](#fungsi-left_join-eksperimental)
    *   [Menggunakan `db:*` untuk memilih semua kolom dari tabel dalam JOIN](#menggunakan-db-untuk-memilih-semua-kolom-dari-tabel-dalam-join)
    *   [Global Overrides vs Package Overrides](#global-overrides-vs-package-overrides)
11. [Basis Data yang Didukung](#11-basis-data-yang-didukung)
    *   [PostgreSQL](#postgresql)
        *   Fitur Spesifik dan Dukungan
    *   [MySQL](#mysql)
        *   Fitur Spesifik dan Dukungan
    *   [SQLite](#sqlite)
        *   Fitur Spesifik dan Dukungan
    *   [Microsoft SQL Server (Dukungan Beta)](#microsoft-sql-server-dukungan-beta)
12. [Praktik Terbaik Menggunakan sqlc](#12-praktik-terbaik-menggunakan-sqlc)
    *   [Organisasi File SQL](#organisasi-file-sql)
        *   Satu file per tabel/domain atau berdasarkan fitur
    *   [Penamaan Kueri yang Konsisten](#penamaan-kueri-yang-konsisten)
    *   [Menjaga Kueri Tetap Sederhana](#menjaga-kueri-tetap-sederhana)
    *   [Manfaatkan Fitur Basis Data Anda](#manfaatkan-fitur-basis-data-anda)
    *   [Manajemen Skema dan Migrasi](#manajemen-skema-dan-migrasi)
        *   Alat seperti `migrate`, `goose`, `Atlas`
    *   [Pengujian](#pengujian)
        *   Unit testing untuk logika bisnis yang menggunakan kode sqlc
        *   Integration testing dengan basis data nyata
    *   [Integrasi dengan `go generate`](#integrasi-dengan-go-generate)
    *   [Versi Kontrol untuk Skema dan Kueri](#versi-kontrol-untuk-skema-dan-kueri)
    *   [Perhatikan Performa](#perhatikan-performa)
        *   Gunakan `EXPLAIN` untuk menganalisis kueri
        *   Indeks yang tepat
13. [Integrasi dengan Alat Lain](#13-integrasi-dengan-alat-lain)
    *   [Alat Migrasi (migrate, goose, Atlas, dll.)](#alat-migrasi-migrate-goose-atlas-dll)
    *   [Framework Web Go (Gin, Echo, Chi, dll.)](#framework-web-go-gin-echo-chi-dll)
    *   [Sistem Linting dan Formatting](#sistem-linting-dan-formatting)
14. [Plugin sqlc](#14-plugin-sqlc)
    *   [Apa itu Plugin sqlc?](#apa-itu-plugin-sqlc)
    *   [Proses Pengembangan Plugin](#proses-pengembangan-plugin)
    *   [Contoh Plugin (misalnya, `sqlc-gen-go-jet` atau plugin kustom)](#contoh-plugin-misalnya-sqlc-gen-go-jet-atau-plugin-kustom)
    *   [Konfigurasi Plugin dalam `sqlc.yaml`](#konfigurasi-plugin-dalam-sqlcyaml)
15. [Pemecahan Masalah (Troubleshooting)](#15-pemecahan-masalah-troubleshooting)
    *   [Error Umum Saat `sqlc generate`](#error-umum-saat-sqlc-generate)
    *   [Masalah Tipe Data Tidak Cocok](#masalah-tipe-data-tidak-cocok)
    *   [Kueri Tidak Valid Secara Sintaks](#kueri-tidak-valid-secara-sintaks)
    *   [Perbedaan Perilaku Antar Engine Basis Data](#perbedaan-perilaku-antar-engine-basis-data)
    *   [Tips Debugging](#tips-debugging)
16. [Berkontribusi pada sqlc](#16-berkontribusi-pada-sqlc)
    *   [Melaporkan Bug](#melaporkan-bug)
    *   [Mengajukan Permintaan Fitur](#mengajukan-permintaan-fitur)
    *   [Kontribusi Kode](#kontribusi-kode)
    *   [Komunitas (Discord, GitHub Discussions)](#komunitas-discord-github-discussions)
17. [Kesimpulan dan Langkah Selanjutnya](#17-kesimpulan-dan-langkah-selanjutnya)
    *   [Rangkuman Keunggulan sqlc](#rangkuman-keunggulan-sqlc)
    *   [Bagaimana sqlc Meningkatkan Produktivitas Anda](#bagaimana-sqlc-meningkatkan-produktivitas-anda)
    *   [Sumber Belajar Tambahan](#sumber-belajar-tambahan)

---

## 1. Pendahuluan: Apa itu sqlc?

`sqlc` adalah sebuah _compiler_ yang menghasilkan kode Go yang aman secara tipe (type-safe) dan idiomatis dari kueri SQL. Anda menulis kueri SQL seperti biasa, menambahkan beberapa anotasi khusus, lalu `sqlc` akan menganalisis kueri tersebut beserta skema basis data Anda untuk menghasilkan kode Go yang dapat Anda gunakan langsung dalam aplikasi Anda.

Ini berarti Anda mendapatkan performa SQL mentah dengan keamanan tipe dari bahasa Go, tanpa perlu menulis banyak kode _boilerplate_ untuk memindai baris hasil kueri (row scanning) atau memetakan parameter.

### Filosofi sqlc

Filosofi inti di balik `sqlc` adalah:

1.  **SQL adalah Bahasa Utama**: `sqlc` mendorong Anda untuk menulis SQL. Anda tidak perlu mempelajari DSL (Domain Specific Language) baru untuk berinteraksi dengan basis data. Kekuatan penuh SQL ada di tangan Anda.
2.  **Keamanan Tipe (Type Safety)**: `sqlc` menganalisis kueri SQL dan skema basis data Anda untuk menghasilkan kode Go yang _type-safe_. Ini menangkap banyak kesalahan pada waktu kompilasi (compile-time) daripada saat runtime.
3.  **Kode Idiomatis**: Kode Go yang dihasilkan oleh `sqlc` dirancang agar terasa alami dan mudah digunakan oleh pengembang Go. Ia menggunakan tipe-tipe standar dari paket `database/sql` dan pustaka populer lainnya.
4.  **Minimalisme**: `sqlc` berusaha untuk tidak menjadi _framework_ yang berat. Ia fokus pada satu hal dan melakukannya dengan baik: menghasilkan kode dari SQL.

### Mengapa Menggunakan sqlc? Keuntungannya

Mengadopsi `sqlc` dalam proyek Go Anda menawarkan berbagai keuntungan signifikan:

1.  **Keamanan Tipe**: Kesalahan dalam kueri SQL, seperti salah ketik nama kolom, tipe data yang tidak cocok, atau jumlah parameter yang salah, dapat dideteksi oleh `sqlc` saat proses _generate_, jauh sebelum kode dijalankan di produksi.
2.  **Produktivitas Pengembang**: Mengurangi kode _boilerplate_ secara drastis. Anda tidak perlu lagi menulis kode manual untuk `rows.Scan()` atau mempersiapkan _statement_.
3.  **Performa**: Karena Anda menulis SQL mentah, Anda memiliki kontrol penuh atas performa kueri. Kode yang dihasilkan `sqlc` juga efisien, seringkali hanya merupakan _wrapper_ tipis di atas driver basis data.
4.  **Refactoring Lebih Mudah**: Jika skema basis data atau kueri berubah, `sqlc generate` akan memperbarui kode Go. Jika ada perubahan yang merusak (breaking changes), kompiler Go akan memberitahu Anda.
5.  **SQL Tetap SQL**: Anda bisa menggunakan semua fitur canggih dari basis data SQL Anda (Common Table Expressions, Window Functions, fitur spesifik vendor) tanpa dibatasi oleh abstraksi ORM.
6.  **Mudah Dipelajari**: Jika Anda sudah tahu SQL dan Go, kurva belajar `sqlc` relatif landai. Anda hanya perlu memahami cara menulis anotasi dan mengkonfigurasi `sqlc.yaml`.
7.  **Tidak Ada Ketergantungan Runtime**: `sqlc` adalah alat waktu kompilasi (_compile-time tool_). Kode yang dihasilkannya tidak memerlukan `sqlc` sebagai dependensi saat runtime.
8.  **Integrasi yang Baik**: Dapat diintegrasikan dengan mudah ke dalam alur kerja pengembangan Go, misalnya dengan `go generate`.

### sqlc vs ORM (Object-Relational Mapper)

ORM seperti GORM atau SQLBoiler menyediakan lapisan abstraksi yang lebih tinggi di atas SQL.

| Fitur             | sqlc                                                               | ORM (misal, GORM)                                                    |
| :---------------- | :----------------------------------------------------------------- | :------------------------------------------------------------------- |
| **Pendekatan**    | SQL-first. Anda menulis SQL.                                       | Code-first atau database-first. Sering menggunakan DSL Go.           |
| **Abstraksi**     | Rendah. Kode Go yang dihasilkan dekat dengan operasi SQL.          | Tinggi. Menyembunyikan detail SQL.                                   |
| **Kontrol Kueri** | Penuh. Anda menulis SQL mentah.                                    | Terbatas oleh kemampuan DSL ORM. Kueri kompleks bisa sulit.        |
| **Kurva Belajar** | Relatif rendah jika sudah familiar dengan SQL.                     | Bisa lebih tinggi, perlu belajar API dan perilaku ORM.               |
| **Performa**      | Umumnya sangat baik, setara dengan SQL mentah.                     | Bisa bervariasi. Abstraksi bisa menambah overhead.                   |
| **Boilerplate**   | Minimal untuk interaksi DB. Kode utama ada di file `.sql`.         | Bisa mengurangi boilerplate CRUD, tapi DSL bisa jadi verbose.        |
| **Keamanan Tipe** | Kuat, melalui analisis SQL dan skema saat `generate`.              | Kuat, melalui tipe Go dan DSL.                                     |
| **Magic**         | Minimal. Lebih transparan apa yang terjadi.                        | Bisa ada "magic" (perilaku implisit) yang sulit dipahami.            |
| **Migrations**    | Tidak menangani migrasi skema secara langsung.                     | Beberapa ORM memiliki alat migrasi bawaan.                           |

**Kapan memilih sqlc?**
*   Ketika tim Anda kuat dalam SQL.
*   Ketika performa kueri sangat kritis.
*   Ketika Anda memerlukan fleksibilitas penuh untuk menulis kueri SQL yang kompleks.
*   Ketika Anda lebih suka pendekatan yang lebih eksplisit dan sedikit "magic".

**Kapan ORM mungkin lebih cocok?**
*   Untuk prototyping cepat dengan banyak operasi CRUD standar.
*   Ketika tim kurang familiar dengan SQL atau ingin abstraksi yang lebih tinggi.
*   Jika fitur seperti _lazy loading_, _eager loading_, atau _caching_ bawaan ORM sangat dibutuhkan.

Penting dicatat bahwa SQLBoiler, meskipun sering dikategorikan sebagai ORM, memiliki filosofi yang lebih dekat dengan `sqlc` dalam hal _database-first_ dan _type-safety_, namun ia menghasilkan lebih banyak kode dan memiliki API yang berbeda.

### sqlc vs Konstruktor Kueri (Query Builders)

Konstruktor kueri seperti Squirrel atau go-jet memungkinkan Anda membangun kueri SQL secara terprogram menggunakan fungsi-fungsi Go.

| Fitur             | sqlc                                                               | Query Builder (misal, Squirrel)                                      |
| :---------------- | :----------------------------------------------------------------- | :------------------------------------------------------------------- |
| **Penulisan Kueri**| Langsung dalam file `.sql`.                                        | Menggunakan API Go untuk menyusun string SQL secara dinamis.         |
| **Keamanan Tipe** | Kuat. Validasi terhadap skema saat `generate`.                     | Terbatas. Kesalahan nama kolom/tabel biasanya baru ketahuan saat runtime.|
| **Pembacaan Kueri**| Kueri SQL mudah dibaca oleh siapa saja yang mengerti SQL.          | Kueri terfragmentasi dalam kode Go, bisa lebih sulit dibaca.        |
| **Kueri Dinamis** | Kurang cocok untuk kueri yang sangat dinamis.                      | Sangat baik untuk membangun kueri secara kondisional.                |
| **Refactoring**   | Jika skema berubah, `sqlc generate` dan kompiler Go akan membantu. | Perubahan skema memerlukan update manual di banyak tempat.             |

**Kapan memilih sqlc?**
*   Untuk sebagian besar kueri yang statis atau semi-statis.
*   Ketika keamanan tipe dan validasi pra-runtime sangat penting.
*   Ketika keterbacaan kueri SQL murni lebih diutamakan.

**Kapan Query Builder mungkin lebih cocok?**
*   Ketika Anda perlu membangun kueri yang sangat dinamis berdasarkan banyak kondisi runtime.
*   Untuk tugas-tugas yang tidak memerlukan analisis skema mendalam, misal, alat admin generik.

Seringkali, `sqlc` dan _query builder_ bisa digunakan bersamaan dalam satu proyek. `sqlc` untuk kueri umum yang terdefinisi dengan baik, dan _query builder_ untuk kasus-kasus yang lebih dinamis.

---

## 2. Prasyarat

Sebelum Anda mulai menggunakan `sqlc`, ada beberapa hal yang perlu Anda siapkan dan pahami:

### Pemahaman Dasar Go

`sqlc` menghasilkan kode Go, jadi Anda harus memiliki pemahaman dasar tentang bahasa Go, termasuk:
*   Sintaks dasar Go (variabel, fungsi, struct, interface).
*   Manajemen paket dan modul Go.
*   Cara mengkompilasi dan menjalankan program Go.
*   Konsep dasar seperti `context.Context` dan penanganan error di Go.
*   Penggunaan paket standar `database/sql`.

### Pemahaman Dasar SQL

Karena Anda akan menulis kueri SQL secara langsung, pemahaman yang baik tentang SQL sangat penting. Ini termasuk:
*   Sintaks DDL (Data Definition Language): `CREATE TABLE`, `ALTER TABLE`, dll.
*   Sintaks DML (Data Manipulation Language): `SELECT`, `INSERT`, `UPDATE`, `DELETE`.
*   Klausul SQL umum: `WHERE`, `JOIN`, `GROUP BY`, `ORDER BY`, `LIMIT`.
*   Tipe data SQL dan konversinya.
*   Konsep dasar basis data relasional (tabel, kolom, kunci primer, kunci asing).

### Instalasi Go

Pastikan Anda telah menginstal Go versi 1.18 atau yang lebih baru. Anda bisa mengunduhnya dari [situs resmi Go](https://golang.org/dl/).
Untuk memeriksa versi Go Anda, jalankan:
```bash
go version
```

### Akses ke Basis Data SQL

`sqlc` perlu mengetahui skema basis data Anda untuk menghasilkan kode yang benar. Anda memerlukan akses ke salah satu basis data yang didukung:
*   PostgreSQL
*   MySQL
*   SQLite

Anda tidak selalu memerlukan basis data yang berjalan *saat* `sqlc generate` dijalankan jika Anda menyediakan file skema. Namun, untuk pengembangan dan pengujian, Anda pasti akan membutuhkannya.

---

## 3. Instalasi sqlc

Ada beberapa cara untuk menginstal `sqlc` CLI:

### Menggunakan Go

Jika Anda memiliki Go versi 1.18+ yang terinstal dan `$GOPATH/bin` atau `$GOBIN` ada di `PATH` Anda:
```bash
go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest
```
Ini akan menginstal biner `sqlc` ke direktori `$GOPATH/bin` (atau `$GOBIN` jika disetel).

### Menggunakan Homebrew (macOS/Linux)

Jika Anda menggunakan macOS atau Linux dengan Homebrew:
```bash
brew install sqlc
```

### Menggunakan Scoop (Windows)

Jika Anda menggunakan Windows dengan Scoop:
```bash
scoop bucket add extras
scoop install sqlc
```

### Mengunduh Biner

Anda dapat mengunduh biner `sqlc` yang sudah dikompilasi untuk sistem operasi Anda dari [halaman rilis GitHub sqlc](https://github.com/sqlc-dev/sqlc/releases). Ekstrak arsipnya dan letakkan biner `sqlc` di direktori yang ada dalam `PATH` sistem Anda.

### Menggunakan Docker

`sqlc` juga menyediakan image Docker yang dapat Anda gunakan untuk menghasilkan kode, terutama berguna dalam lingkungan CI/CD.
```bash
docker pull sqlc/sqlc
```
Untuk menjalankan `sqlc generate` menggunakan Docker:
```bash
docker run --rm -v "$(pwd):/src" -w /src sqlc/sqlc generate
```
Perintah ini me-mount direktori saat ini (`$(pwd)`) ke `/src` di dalam kontainer, menetapkan direktori kerja ke `/src`, dan kemudian menjalankan `sqlc generate`.

### Verifikasi Instalasi

Setelah instalasi, Anda dapat memverifikasi bahwa `sqlc` berhasil diinstal dengan menjalankan:
```bash
sqlc version
```
Ini akan menampilkan versi `sqlc` yang terinstal.

---

## 4. Memulai dengan sqlc: Tutorial Cepat

Mari kita buat proyek sederhana untuk mendemonstrasikan alur kerja dasar `sqlc`. Kita akan menggunakan PostgreSQL untuk contoh ini, tetapi konsepnya serupa untuk MySQL atau SQLite.

**Asumsi:** Anda sudah menginstal Go dan `sqlc`. Anda juga memiliki server PostgreSQL yang berjalan dan dapat diakses.

### Inisialisasi Proyek Go

Buat direktori proyek baru dan inisialisasi modul Go:
```bash
mkdir sqlc-tutorial
cd sqlc-tutorial
go mod init github.com/yourusername/sqlc-tutorial # Ganti dengan path modul Anda
```

### Membuat File Konfigurasi `sqlc.yaml`

Buat file bernama `sqlc.yaml` di root direktori proyek Anda dengan konten berikut:

```yaml
version: "2" # Atau "1" jika menggunakan versi lama, tapi "2" direkomendasikan
sql:
  - engine: "postgresql"
    # Path ke file skema DDL Anda
    schema: "db/migration/schema.sql"
    # Path ke file kueri SQL Anda
    queries: "db/query/query.sql"
    # Konfigurasi untuk kode Go yang dihasilkan
    gen:
      go:
        # Nama paket Go yang akan dihasilkan
        package: "tutorial"
        # Direktori output untuk kode Go yang dihasilkan
        out: "db/sqlc"
        # Opsi tambahan (opsional, defaultnya sudah baik)
        emit_json_tags: true
        emit_interface: true
        emit_exact_table_names: false # false akan membuat nama struct jamak menjadi tunggal (e.g., authors -> Author)
        # emit_prepared_queries: true # jika ingin menggunakan prepared statements
```

Pastikan Anda membuat direktori yang sesuai:
```bash
mkdir -p db/migration
mkdir -p db/query
mkdir -p db/sqlc # Direktori ini akan dibuat oleh sqlc jika belum ada, tapi lebih baik disiapkan
```

### Menulis Skema Basis Data (`schema.sql`)

Buat file `db/migration/schema.sql`. File ini akan berisi pernyataan DDL (Data Definition Language) untuk membuat tabel Anda.

```sql
-- db/migration/schema.sql
CREATE TABLE authors (
  id   BIGSERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  bio  TEXT
);

CREATE TABLE books (
  id   BIGSERIAL PRIMARY KEY,
  author_id BIGINT NOT NULL REFERENCES authors(id),
  title TEXT NOT NULL,
  published_year INT NOT NULL
);
```

**Catatan:** `sqlc` menggunakan file skema ini untuk memahami struktur tabel, kolom, dan tipe datanya. Anda tidak perlu menjalankan skema ini secara manual terhadap basis data *hanya* untuk `sqlc generate` (kecuali jika engine Anda memerlukannya, seperti beberapa versi MySQL lama), tetapi Anda pasti perlu menjalankannya untuk aplikasi Anda berinteraksi dengan basis data nyata. Untuk PostgreSQL dan SQLite, `sqlc` dapat mem-parsing DDL secara langsung.

### Menulis Kueri SQL (`query.sql`)

Buat file `db/query/query.sql`. File ini akan berisi kueri SQL Anda dengan anotasi `sqlc`.

```sql
-- db/query/query.sql

-- name: GetAuthor :one
SELECT * FROM authors
WHERE id = $1 LIMIT 1;

-- name: ListAuthors :many
SELECT * FROM authors
ORDER BY name;

-- name: CreateAuthor :one
INSERT INTO authors (
  name, bio
) VALUES (
  $1, $2
)
RETURNING *;

-- name: UpdateAuthorBio :one
UPDATE authors
SET bio = $2
WHERE id = $1
RETURNING *;

-- name: DeleteAuthor :exec
DELETE FROM authors
WHERE id = $1;

-- name: CreateBook :one
INSERT INTO books (
    author_id, title, published_year
) VALUES (
    $1, $2, $3
)
RETURNING *;

-- name: ListBooksByAuthor :many
SELECT * FROM books
WHERE author_id = $1
ORDER BY published_year;
```

Penjelasan anotasi:
*   `-- name: GetAuthor :one`:
    *   `GetAuthor`: Akan menjadi nama fungsi Go yang dihasilkan.
    *   `:one`: Menandakan kueri ini diharapkan mengembalikan tepat satu baris. `sqlc` akan menghasilkan fungsi yang mengembalikan satu struct model.
*   `-- name: ListAuthors :many`:
    *   `:many`: Menandakan kueri ini dapat mengembalikan banyak baris. `sqlc` akan menghasilkan fungsi yang mengembalikan slice dari struct model.
*   `-- name: CreateAuthor :one`: Menggunakan `RETURNING *` untuk mendapatkan baris yang baru dimasukkan.
*   `-- name: DeleteAuthor :exec`:
    *   `:exec`: Menandakan kueri ini tidak mengembalikan baris (misalnya, `DELETE` tanpa `RETURNING`, atau `UPDATE` tanpa `RETURNING`). Fungsi yang dihasilkan tidak akan memiliki nilai kembali untuk data, hanya `error`.
*   `$1`, `$2`, `$3`: Ini adalah placeholder parameter untuk PostgreSQL. Untuk MySQL, Anda akan menggunakan `?`. Untuk SQLite, keduanya bisa digunakan. `sqlc` akan menghasilkan fungsi Go dengan parameter yang sesuai.

### Menghasilkan Kode Go

Sekarang, jalankan perintah `sqlc generate` dari root direktori proyek Anda:
```bash
sqlc generate
```

Jika semuanya berjalan lancar, `sqlc` akan membaca `sqlc.yaml`, mem-parsing `schema.sql` dan `query.sql`, lalu menghasilkan file-file Go di direktori `db/sqlc/` (sesuai konfigurasi `out`). Anda akan melihat file-file seperti:
*   `db/sqlc/db.go`: Berisi interface `Querier` dan implementasi `Queries`.
*   `db/sqlc/models.go`: Berisi struct Go yang merepresentasikan tabel Anda (`Author`, `Book`) dan parameter/hasil kueri jika diperlukan.
*   `db/sqlc/query.sql.go`: Berisi implementasi fungsi-fungsi Go untuk setiap kueri yang Anda definisikan (`GetAuthor`, `ListAuthors`, `CreateAuthor`, dll.).

**Isi `models.go` (Contoh):**
```go
// Code generated by sqlc. DO NOT EDIT.
// versions:
//   sqlc vX.Y.Z

package tutorial

import (
	"database/sql"
)

type Author struct {
	ID   int64
	Name string
	Bio  sql.NullString // Karena kolom bio bisa NULL
}

type Book struct {
	ID            int64
	AuthorID      int64
	Title         string
	PublishedYear int32
}
```
Perhatikan bagaimana `bio` (yang `TEXT` dan bisa `NULL` di skema) dipetakan ke `sql.NullString`. `published_year` (yang `INT`) dipetakan ke `int32`. `id` (`BIGSERIAL`) dipetakan ke `int64`.

### Menggunakan Kode yang Dihasilkan dalam Aplikasi Go

Buat file `main.go` di root proyek Anda untuk mencoba menggunakan kode yang dihasilkan.

```go
// main.go
package main

import (
	"context"
	"database/sql"
	"fmt"
	"log"
	"os" // Untuk mengambil DSN dari environment variable

	// Impor paket yang dihasilkan sqlc
	"github.com/yourusername/sqlc-tutorial/db/sqlc" // Sesuaikan dengan path modul Anda

	// Impor driver PostgreSQL. _ untuk side effect registrasi driver.
	_ "github.com/lib/pq"
	// Atau jika menggunakan pgx:
	// _ "github.com/jackc/pgx/v5/stdlib"
)

const (
	dbDriver = "postgres"
	// Ganti dengan DSN PostgreSQL Anda atau set via environment variable
	// Contoh DSN: "postgresql://root:secret@localhost:5432/simple_bank?sslmode=disable"
)

func run() error {
	// Ambil DSN dari environment variable atau gunakan default
	dbSource := os.Getenv("DB_SOURCE")
	if dbSource == "" {
		// DSN Default jika tidak ada env var. JANGAN GUNAKAN INI DI PRODUKSI.
		dbSource = "postgresql://user:password@localhost:5432/mydatabase?sslmode=disable"
		log.Printf("Warning: DB_SOURCE environment variable not set, using default DSN: %s", dbSource)
	}

	// Buka koneksi ke basis data
	conn, err := sql.Open(dbDriver, dbSource)
	if err != nil {
		return fmt.Errorf("tidak dapat terhubung ke basis data: %w", err)
	}
	defer conn.Close()

	// Ping basis data untuk memastikan koneksi berhasil
	if err := conn.Ping(); err != nil {
		return fmt.Errorf("tidak dapat melakukan ping ke basis data: %w", err)
	}
	log.Println("Berhasil terhubung ke basis data!")

	// Buat instance Queries
	queries := tutorial.New(conn) // tutorial adalah nama paket yang kita definisikan di sqlc.yaml

	// Buat konteks
	ctx := context.Background()

	// 1. Buat penulis baru
	newAuthor, err := queries.CreateAuthor(ctx, tutorial.CreateAuthorParams{
		Name: "Kahlil Gibran",
		Bio:  sql.NullString{String: "Seorang penyair Lebanon-Amerika", Valid: true},
	})
	if err != nil {
		return fmt.Errorf("gagal membuat penulis: %w", err)
	}
	log.Printf("Penulis baru dibuat: %+v\n", newAuthor)

	// 2. Dapatkan penulis berdasarkan ID
	fetchedAuthor, err := queries.GetAuthor(ctx, newAuthor.ID)
	if err != nil {
		return fmt.Errorf("gagal mendapatkan penulis: %w", err)
	}
	log.Printf("Penulis ditemukan: %+v\n", fetchedAuthor)

	// 3. Buat buku untuk penulis tersebut
	newBook, err := queries.CreateBook(ctx, tutorial.CreateBookParams{
		AuthorID:      newAuthor.ID,
		Title:         "Sang Nabi",
		PublishedYear: 1923,
	})
	if err != nil {
		return fmt.Errorf("gagal membuat buku: %w", err)
	}
	log.Printf("Buku baru dibuat: %+v\n", newBook)

	// 4. Daftar semua buku oleh penulis tersebut
	books, err := queries.ListBooksByAuthor(ctx, newAuthor.ID)
	if err != nil {
		return fmt.Errorf("gagal mendaftar buku: %w", err)
	}
	log.Printf("Buku oleh %s:\n", newAuthor.Name)
	for _, book := range books {
		log.Printf("- %s (%d)\n", book.Title, book.PublishedYear)
	}

	// 5. Daftar semua penulis
	allAuthors, err := queries.ListAuthors(ctx)
	if err != nil {
		return fmt.Errorf("gagal mendaftar semua penulis: %w", err)
	}
	log.Println("Semua penulis:")
	for _, author := range allAuthors {
		log.Printf("ID: %d, Nama: %s, Bio: %s\n", author.ID, author.Name, author.Bio.String)
	}

	// 6. Hapus penulis (ini juga akan gagal jika ada buku yang mereferensikannya,
	// kecuali jika Anda memiliki ON DELETE CASCADE pada foreign key)
	// Untuk contoh ini, kita akan menghapus buku terlebih dahulu, lalu penulis
	// (atau kita bisa menonaktifkan foreign key constraint untuk demo)
	// Demi kesederhanaan, kita akan langsung coba hapus, ini mungkin error jika ada foreign key
	// err = queries.DeleteAuthor(ctx, newAuthor.ID)
	// if err != nil {
	//  return fmt.Errorf("gagal menghapus penulis: %w", err)
	// }
	// log.Printf("Penulis dengan ID %d berhasil dihapus.\n", newAuthor.ID)

	return nil
}

func main() {
	if err := run(); err != nil {
		log.Fatalf("Error menjalankan aplikasi: %v", err)
	}
	log.Println("Aplikasi selesai.")
}
```

**Penting:**
*   Ganti `github.com/yourusername/sqlc-tutorial` dengan path modul Go Anda.
*   Pastikan driver basis data yang sesuai telah diimpor (misalnya, `github.com/lib/pq` untuk PostgreSQL). Jalankan `go get github.com/lib/pq` jika belum ada.
*   Sesuaikan string koneksi `dbSource` dengan konfigurasi basis data PostgreSQL Anda. **Sangat direkomendasikan** untuk mengatur ini melalui variabel lingkungan (`DB_SOURCE`) daripada _hardcoding_ kredensial.
*   Pastikan Anda telah membuat basis data yang dituju oleh DSN Anda dan telah menjalankan skrip `db/migration/schema.sql` terhadap basis data tersebut untuk membuat tabel `authors` dan `books`. Anda bisa menggunakan `psql` atau alat GUI basis data.

    Contoh perintah `psql` untuk menjalankan skema:
    ```bash
    psql -U youruser -d yourdatabase -a -f db/migration/schema.sql
    ```

### Menjalankan Aplikasi

Pertama, dapatkan dependensi:
```bash
go mod tidy
# atau go get github.com/lib/pq
```

Kemudian, jalankan aplikasi:
```bash
# Set DSN sebagai environment variable (rekomendasi)
export DB_SOURCE="postgresql://user:password@localhost:5432/mydatabase?sslmode=disable"
go run main.go
```
Anda akan melihat output log yang menunjukkan operasi basis data yang berhasil dilakukan.

Selamat! Anda telah menyelesaikan tutorial cepat `sqlc`. Anda telah belajar cara:
1.  Mengkonfigurasi `sqlc`.
2.  Menulis skema basis data.
3.  Menulis kueri SQL dengan anotasi `sqlc`.
4.  Menghasilkan kode Go dari kueri tersebut.
5.  Menggunakan kode yang dihasilkan dalam aplikasi Go untuk berinteraksi dengan basis data.

Langkah selanjutnya adalah memahami lebih dalam setiap komponen ini.

---

## 5. Konfigurasi sqlc: `sqlc.yaml` Secara Mendalam

File `sqlc.yaml` (atau `sqlc.json`) adalah jantung dari konfigurasi `sqlc`. File ini memberitahu `sqlc` di mana menemukan file skema dan kueri Anda, engine basis data apa yang digunakan, dan bagaimana cara menghasilkan kode Go.

### Struktur Dasar `sqlc.yaml`

Versi konfigurasi yang direkomendasikan adalah `version: "2"`. Versi "1" juga masih didukung untuk kompatibilitas mundur.

```yaml
version: "2" # atau "1"
# Opsi global seperti cloud (jika menggunakan sqlc Cloud) atau plugin
# cloud:
#   organization: "your-org"
#   project: "your-project"
#   auth_token: "${SQLC_AUTH_TOKEN}"

# Daftar konfigurasi paket SQL. Anda bisa memiliki beberapa.
sql:
  - # Konfigurasi paket pertama
    engine: "postgresql" # atau "mysql", "sqlite"
    schema: "path/to/schema.sql" # atau ["path/to/schema1.sql", "path/to/schema2.sql"]
    queries: "path/to/queries.sql" # atau ["path/to/queries1.sql", "path/to/queries2.sql"] atau direktori "path/to/query_dir/"
    gen:
      go: # Hanya generator Go yang didukung secara resmi saat ini
        package: "db"
        out: "internal/db"
        # Opsi spesifik Go lainnya...
    # Opsi lain seperti `database`, `rules`, `plugins`...

  # - # Konfigurasi paket kedua (jika Anda memiliki beberapa, misal untuk DB berbeda atau modul berbeda)
    # ...

# Daftar plugin global (jika ada)
# plugins:
#   - name: "myplugin"
#     process:
#       cmd: "myplugin-processor"

# Daftar aturan linting global (jika ada)
# rules:
#   - "sqlc-dev/summary-card"
```

### Opsi Konfigurasi Utama

#### `version`

*   **Tipe**: `string`
*   **Wajib**: Ya
*   **Deskripsi**: Menentukan versi format file konfigurasi. `"2"` adalah yang terbaru dan direkomendasikan. `"1"` adalah format lama.

#### `sql` (Package Configuration)

*   **Tipe**: `array` dari objek konfigurasi paket
*   **Wajib**: Ya
*   **Deskripsi**: Ini adalah array di mana setiap elemen mendefinisikan satu set skema, kueri, dan opsi _codegen_ yang menghasilkan satu paket Go. Anda mungkin memiliki beberapa entri di sini jika, misalnya, Anda terhubung ke beberapa jenis basis data atau ingin menghasilkan paket Go yang berbeda untuk subset kueri yang berbeda.

    Setiap elemen dalam array `sql` memiliki sub-opsi berikut:

#### `plugins`

*   **Tipe**: `array` dari objek konfigurasi plugin
*   **Wajib**: Tidak
*   **Deskripsi**: Mendefinisikan plugin global yang akan dijalankan oleh `sqlc`. Plugin memungkinkan Anda memperluas fungsionalitas `sqlc`, misalnya untuk menghasilkan kode dalam bahasa lain atau format tambahan.
    ```yaml
    plugins:
      - name: "kotlin" # Nama plugin
        # Opsi untuk plugin tipe "wasm"
        # wasm:
        #   url: "https://.../plugin.wasm"
        #   sha256: "..."
        # Opsi untuk plugin tipe "process"
        process:
          cmd: "sqlc-gen-kotlin" # Perintah untuk menjalankan proses plugin
        # Opsi output spesifik plugin
        # output:
        #   path: "generated/kt"
        #   options:
        #     package: "com.example.db"
    ```

#### `rules`

*   **Tipe**: `array` dari `string` atau objek konfigurasi aturan
*   **Wajib**: Tidak
*   **Deskripsi**: Mendefinisikan aturan linting yang akan dijalankan `sqlc`. Aturan ini dapat membantu menegakkan konvensi atau mendeteksi potensi masalah dalam kueri SQL Anda. Fitur ini lebih relevan jika Anda menggunakan sqlc Cloud atau mengembangkan aturan linting kustom.
    ```yaml
    rules:
      - "sqlc-dev/CR001" # Aturan bawaan dari sqlc-dev
      - name: "my-custom-rule" # Aturan kustom
        rule:
          # Konfigurasi aturan kustom
    ```

### Konfigurasi Paket (`package`)

Setiap item dalam array `sql` adalah objek konfigurasi paket. Berikut adalah opsi yang paling umum digunakan di dalamnya, terutama dalam `gen: go:`:

#### `name`

*   **Tipe**: `string`
*   **Wajib**: Tidak (tetapi sangat direkomendasikan jika Anda memiliki beberapa entri `sql`)
*   **Deskripsi**: Nama unik untuk konfigurasi paket ini. Berguna untuk referensi jika Anda menggunakan beberapa konfigurasi.

#### `path`

*   **Tipe**: `string`
*   **Wajib**: Tidak (tetapi direkomendasikan untuk proyek besar)
*   **Lokasi**: Langsung di bawah item array `sql`.
*   **Deskripsi**: Jika disetel, `sqlc` akan mencari file `sqlc.yaml` atau `sqlc.json` di direktori ini dan menggunakan konfigurasi tersebut. Ini berguna untuk proyek monorepo di mana setiap layanan mungkin memiliki konfigurasi `sqlc` sendiri. Jika `path` digunakan, opsi lain seperti `engine`, `schema`, `queries`, dan `gen` dalam blok ini akan diabaikan karena diambil dari file `sqlc.yaml` yang ada di `path` tersebut.

#### `engine`

*   **Tipe**: `string`
*   **Wajib**: Ya
*   **Nilai yang Mungkin**: `"postgresql"`, `"mysql"`, `"sqlite"`, `"mssql"` (beta)
*   **Deskripsi**: Menentukan jenis basis data yang Anda gunakan. `sqlc` menggunakan informasi ini untuk mem-parsing SQL Anda dengan benar dan menghasilkan tipe Go yang sesuai.

#### `schema`

*   **Tipe**: `string` atau `array` dari `string`
*   **Wajib**: Ya
*   **Deskripsi**: Path ke file atau direktori yang berisi pernyataan DDL (misalnya, `CREATE TABLE`).
    *   Jika string tunggal: path ke satu file skema (misalnya, `db/schema.sql`).
    *   Jika array string: daftar path ke beberapa file skema (misalnya, `["schema/tables.sql", "schema/enums.sql"]`). File akan dibaca dalam urutan yang diberikan.
    *   Jika path ke direktori: `sqlc` akan membaca semua file `.sql` dalam direktori tersebut secara leksikografis.

#### `queries`

*   **Tipe**: `string` atau `array` dari `string`
*   **Wajib**: Ya
*   **Deskripsi**: Path ke file atau direktori yang berisi kueri SQL Anda (dengan anotasi `sqlc`).
    *   Jika string tunggal: path ke satu file kueri (misalnya, `db/query.sql`).
    *   Jika array string: daftar path ke beberapa file kueri.
    *   Jika path ke direktori: `sqlc` akan membaca semua file `.sql` dalam direktori tersebut.

#### `emit_json_tags`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `boolean`
*   **Default**: `false`
*   **Deskripsi**: Jika `true`, `sqlc` akan menambahkan tag JSON (`json:"field_name"`) ke field struct yang dihasilkan. Nama field dalam tag JSON akan mengikuti konvensi _snake_case_ (misalnya, `user_id`). Sangat berguna untuk serialisasi/deserialisasi JSON.

#### `emit_prepared_queries`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `boolean`
*   **Default**: `false`
*   **Deskripsi**: Jika `true`, `sqlc` akan menghasilkan kode yang menggunakan _prepared statements_ untuk setiap kueri. Ini dapat meningkatkan performa untuk kueri yang sering dijalankan. Kode yang dihasilkan akan menyimpan _prepared statements_ pada instance `*Queries`.

#### `emit_interface`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `boolean`
*   **Default**: `false` (tapi dalam tutorial cepat kita set `true`)
*   **Deskripsi**: Jika `true`, `sqlc` akan menghasilkan interface `Querier` di `db.go` yang mendefinisikan semua metode kueri. Struct `Queries` kemudian akan mengimplementasikan interface ini. Ini sangat berguna untuk _mocking_ dan _dependency injection_ dalam pengujian.

#### `emit_exact_table_names`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `boolean`
*   **Default**: `false`
*   **Deskripsi**:
    *   Jika `false` (default): `sqlc` akan mencoba mengubah nama tabel jamak menjadi tunggal untuk nama struct Go (misalnya, tabel `authors` menjadi struct `Author`).
    *   Jika `true`: `sqlc` akan menggunakan nama tabel persis seperti yang ada di skema untuk nama struct Go (misalnya, tabel `authors` menjadi struct `Authors`).

#### `emit_empty_slices`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `boolean`
*   **Default**: `false`
*   **Deskripsi**:
    *   Jika `false` (default): Ketika sebuah kueri `:many` tidak menghasilkan baris, fungsi akan mengembalikan `nil` slice.
    *   Jika `true`: Fungsi akan mengembalikan slice kosong (`[]Model{}`) bukan `nil`.

#### `emit_exported_queries`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `boolean`
*   **Default**: `true`
*   **Deskripsi**: Secara default, semua fungsi kueri yang dihasilkan diekspor (dimulai dengan huruf kapital). Jika disetel ke `false`, fungsi-fungsi tersebut tidak akan diekspor (dimulai dengan huruf kecil), yang mungkin berguna jika Anda ingin membungkusnya lebih lanjut dalam paket yang sama.

#### `emit_result_struct_pointers`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `boolean`
*   **Default**: `false`
*   **Deskripsi**:
    *   Jika `false` (default): Fungsi kueri yang mengembalikan satu baris (`:one`) akan mengembalikan struct model secara langsung (misalnya, `Author`). Fungsi yang mengembalikan banyak baris (`:many`) akan mengembalikan slice dari struct (misalnya, `[]Author`).
    *   Jika `true`: Fungsi `:one` akan mengembalikan pointer ke struct model (misalnya, `*Author`). Fungsi `:many` akan mengembalikan slice dari pointer ke struct model (misalnya, `[]*Author`). Ini bisa berguna untuk membedakan antara baris yang tidak ditemukan (di mana pointer bisa `nil`) dan baris yang ditemukan dengan semua field `NULL` (jika semua field _nullable_). Namun, `sql.ErrNoRows` adalah cara standar untuk menangani baris tidak ditemukan.

#### `emit_params_struct_pointers`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `boolean`
*   **Default**: `false`
*   **Deskripsi**:
    *   Jika `false` (default): Jika sebuah kueri memiliki banyak parameter, `sqlc` menghasilkan struct untuk parameter tersebut (misalnya, `CreateAuthorParams`), dan parameter ini diteruskan sebagai nilai.
    *   Jika `true`: Struct parameter akan diteruskan sebagai pointer (misalnya, `*CreateAuthorParams`).

#### `emit_methods_with_db_argument`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `boolean`
*   **Default**: `false`
*   **Deskripsi**:
    *   Jika `false` (default): Metode kueri yang dihasilkan adalah metode dari struct `*Queries`, yang menyimpan `DBTX` (bisa `*sql.DB` atau `*sql.Tx`).
    *   Jika `true`: Metode kueri yang dihasilkan akan menjadi fungsi biasa yang menerima `DBTX` sebagai argumen pertama. Ini membuat struct `Queries` menjadi _stateless_ dan bisa lebih mudah digunakan dalam beberapa pola. Fungsi `New(DBTX)` tidak akan dihasilkan.

#### `json_tags_case_style`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `string`
*   **Default**: `"snake"`
*   **Nilai yang Mungkin**: `"snake"`, `"camel"`, `"pascal"`, `"lowerSnake"`, `"kebab"`
*   **Deskripsi**: Mengontrol _case style_ untuk tag JSON jika `emit_json_tags` adalah `true`.
    *   `snake`: `field_name`
    *   `camel`: `fieldName`
    *   `pascal`: `FieldName`
    *   `lowerSnake`: (sama dengan `snake`)
    *   `kebab`: `field-name`

#### `output_db_file_name`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `string`
*   **Default**: `"db.go"`
*   **Deskripsi**: Kustomisasi nama file yang berisi interface `Querier` dan struct `Queries`.

#### `output_models_file_name`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `string`
*   **Default**: `"models.go"`
*   **Deskripsi**: Kustomisasi nama file yang berisi definisi struct model.

#### `output_querier_file_name`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `string`
*   **Default**: `"<query_filename>.sql.go"`
*   **Deskripsi**: Kustomisasi template nama file untuk file yang berisi implementasi kueri. Jika disetel, semua kueri dari semua file input akan digabungkan ke dalam satu file output ini. Tidak direkomendasikan jika Anda punya banyak file kueri.

#### `output_copyfrom_file_name`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `string`
*   **Default**: `"copyfrom.go"`
*   **Deskripsi**: Kustomisasi nama file yang berisi helper untuk kueri `:copyfrom` (PostgreSQL).

#### `overrides`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `array` dari objek _override_
*   **Default**: Kosong
*   **Deskripsi**: Memungkinkan Anda mengganti tipe Go default yang dihasilkan `sqlc` untuk tipe kolom SQL tertentu atau untuk kolom tertentu. Sangat kuat untuk kustomisasi.
    Struktur objek _override_:
    ```yaml
    overrides:
      - db_type: "uuid" # Tipe data SQL (misalnya, dari PostgreSQL)
        go_type: "github.com/google/uuid.UUID" # Tipe Go yang diinginkan
        # Opsi tambahan:
        # nullable: true # Jika tipe Go ini adalah pointer atau mengimplementasikan sql.Scanner/driver.Valuer untuk null
        # column: "table_name.column_name" # Menerapkan override hanya untuk kolom spesifik ini
        # go_struct_tag: 'custom_tag:"foo"' # Menambahkan tag struct kustom
        # type_name: "MyCustomUUID" # Jika go_type adalah tipe kustom dalam paket yang sama

      - column: "users.preferences" # Kolom spesifik: tabel 'users', kolom 'preferences'
        db_type: "jsonb"
        go_type: "myapp/internal.UserPreferences" # Struct kustom Anda
        # Bisa juga pakai import path lengkap jika di paket lain
        # go_type: "github.com/myorg/myapp/pkg/types.UserPreferences"

      - db_type: "custom_enum_type" # Untuk tipe ENUM PostgreSQL
        go_type:
          # Jika tipe Go adalah string atau tipe dasar lain yang sudah didefinisikan
          type: "string"
          # atau jika ingin membuat tipe Go baru dari enum tersebut
          # type: "MyEnumType"
          # import: "github.com/yourusername/sqlc-tutorial/db/customtypes" # Paket tempat MyEnumType didefinisikan
          # nullable: false # defaultnya adalah false jika `type` digunakan
    ```
    Lihat bagian [Fitur Lanjutan: Overrides Tipe Data](#overrides-tipe-data-overrides) untuk detail lebih lanjut.

#### `rules` (Package-level rules)

*   **Lokasi**: Langsung di bawah item array `sql`.
*   **Tipe**: `array` dari `string` atau objek konfigurasi aturan.
*   **Wajib**: Tidak.
*   **Deskripsi**: Sama seperti `rules` global, tetapi hanya berlaku untuk paket ini.

#### `plugins` (Package-level plugins)

*   **Lokasi**: Langsung di bawah item array `sql`.
*   **Tipe**: `array` dari objek konfigurasi plugin.
*   **Wajib**: Tidak.
*   **Deskripsi**: Sama seperti `plugins` global, tetapi hanya berlaku untuk paket ini.

#### `rename`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `object`
*   **Default**: Kosong
*   **Deskripsi**: Memungkinkan Anda mengganti nama struct atau field yang dihasilkan `sqlc`. Berguna jika konvensi penamaan default `sqlc` tidak sesuai dengan preferensi Anda.
    ```yaml
    rename:
      # Mengganti nama 'old_name' menjadi 'NewName' untuk semua struct dan field
      # (ini jarang digunakan, lebih sering per kolom atau per tabel)
      # old_name: "NewName"

      # Mengganti nama kolom 'account_id' pada tabel 'users' menjadi 'UserID' pada struct User
      # (biasanya sqlc sudah melakukan ini dengan baik, contoh ini lebih untuk ilustrasi)
      # users.account_id: "UserID"

      # Mengganti nama tabel 'app_users' menjadi struct 'User'
      # Ini berguna jika `emit_exact_table_names: true` atau jika nama tabel tidak standar
      app_users: "User"
    ```

#### `sql_package`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `string`
*   **Default**: `"database/sql"`
*   **Nilai yang Mungkin**: `"database/sql"`, `"pgx/v4"`, `"pgx/v5"`
*   **Deskripsi**: Menentukan paket SQL mana yang akan digunakan oleh kode yang dihasilkan.
    *   `"database/sql"`: Standar, kompatibel dengan kebanyakan driver.
    *   `"pgx/v4"` atau `"pgx/v5"`: Jika Anda menggunakan `pgx` sebagai driver PostgreSQL dan ingin memanfaatkan tipe-tipe spesifik `pgx` (seperti `pgtype`) dan API-nya secara lebih langsung. Ini akan mengubah `DBTX` menjadi `pgx.Tx` atau `pgx.Conn`. Ini juga akan mengubah cara `sqlc` menangani tipe data null dan array, seringkali menghasilkan kode yang lebih bersih jika Anda berkomitmen pada ekosistem `pgx`.

#### `strict_order_by`

*   **Lokasi**: Di dalam `gen: go:`
*   **Tipe**: `boolean`
*   **Default**: `false`
*   **Deskripsi**: Jika `true`, `sqlc` akan mewajibkan setiap kolom yang disebutkan dalam klausa `ORDER BY` dari kueri `SELECT` juga ada dalam daftar kolom yang dipilih. Ini untuk mencegah perilaku tak terduga di beberapa basis data.

### Contoh Konfigurasi Lanjutan

```yaml
version: "2"
sql:
  - engine: "postgresql"
    schema: "db/migration/" # Direktori berisi file .sql untuk skema
    queries: "db/query/"   # Direktori berisi file .sql untuk kueri
    database:
      # Jika Anda ingin sqlc terhubung ke DB saat generate (opsional untuk PG/SQLite, berguna untuk MySQL)
      # uri: "${DATABASE_URL}" # Menggunakan environment variable
    gen:
      go:
        package: "db"
        out: "internal/app/db"
        emit_json_tags: true
        emit_interface: true
        emit_exact_table_names: false
        emit_empty_slices: true
        sql_package: "pgx/v5" # Menggunakan pgx/v5
        overrides:
          - db_type: "pg_catalog.timestamptz" # Tipe PostgreSQL untuk timestamp with timezone
            go_type: "time.Time"
          - db_type: "pg_catalog.text" # Override semua tipe TEXT
            nullable: true # Berlaku jika kolomnya nullable di DB
            go_type: "github.com/jackc/pgx/v5/pgtype.Text" # Menggunakan pgtype.Text dari pgx
          - column: "users.email_verified_at" # Kolom spesifik
            nullable: true # Penting jika kolomnya nullable dan tipe Go bukan pointer/interface
            go_type: "github.com/jackc/pgx/v5/pgtype.Timestamptz"
          - db_type: "status_enum" # Misal Anda punya ENUM 'status_enum' di PostgreSQL
            go_type:
              type: "CustomStatusEnum" # Nama tipe Go kustom Anda
              import: "github.com/yourorg/yourproject/internal/enums"
            # sqlc akan membuat MyEnumType sebagai alias string, atau Anda bisa definisikan sendiri
            # dan sqlc akan memvalidasi bahwa itu adalah tipe string dasar.

        rename:
          # Jika tabel Anda bernama 'APP_USERS' dan Anda ingin struct 'User'
          APP_USERS: "User"
          # Jika kolom di tabel 'APP_USERS' bernama 'USER_EMAIL' dan Anda ingin field 'EmailAddress'
          APP_USERS.USER_EMAIL: "EmailAddress"

    # Aturan linting spesifik untuk paket ini
    # rules:
    #   - "sqlc-dev/NO_SELECT_STAR"
```
Konfigurasi `sqlc.yaml` sangat fleksibel dan memungkinkan kustomisasi mendalam. Selalu rujuk ke [dokumentasi resmi sqlc](https://docs.sqlc.dev/en/latest/reference/config.html) untuk opsi terbaru dan paling detail.

---

## 6. Menulis Skema Basis Data

Skema basis data adalah cetak biru dari struktur data Anda. `sqlc` memerlukan definisi skema ini untuk:
1.  Memahami tabel, kolom, dan tipe datanya.
2.  Memvalidasi kueri SQL Anda terhadap skema tersebut.
3.  Menghasilkan struct Go yang sesuai dan tipe data yang benar.

### File `schema.sql`

Biasanya, Anda akan mendefinisikan skema Anda dalam satu atau beberapa file `.sql`. Path ke file-file ini ditentukan dalam `sqlc.yaml` di bawah kunci `schema`.

Contoh:
```yaml
# sqlc.yaml
version: "2"
sql:
  - engine: "postgresql"
    schema: "db/migrations/" # sqlc akan membaca semua *.sql di direktori ini
    queries: "db/queries/"
    gen:
      go:
        package: "db"
        out: "internal/db"
```
Dalam contoh di atas, `sqlc` akan mencari semua file dengan ekstensi `.sql` di dalam direktori `db/migrations/` dan mem-parsingnya secara berurutan (biasanya leksikografis berdasarkan nama file) untuk membangun pemahaman tentang skema basis data.

Anda juga bisa menentukan file secara eksplisit:
```yaml
# sqlc.yaml
schema: ["db/001_users_table.sql", "db/002_posts_table.sql"]
```

### Sintaks DDL (Data Definition Language)

File skema harus berisi pernyataan DDL yang valid untuk engine basis data yang Anda pilih (`postgresql`, `mysql`, atau `sqlite`). Pernyataan DDL utama yang dipahami `sqlc` meliputi:

*   `CREATE TABLE`
*   `ALTER TABLE` (untuk menambah kolom, mengubah tipe, dll., meskipun `sqlc` lebih fokus pada status akhir skema)
*   `CREATE TYPE` (misalnya, untuk Enum di PostgreSQL)
*   `CREATE DOMAIN`
*   `CREATE INDEX` (tidak secara langsung memengaruhi kode yang dihasilkan, tetapi baik untuk kelengkapan)
*   `COMMENT ON TABLE` / `COMMENT ON COLUMN` (dapat digunakan oleh beberapa plugin atau untuk dokumentasi)

`sqlc` tidak *menjalankan* DDL ini terhadap basis data Anda (kecuali Anda menggunakan fitur `database` dengan engine yang memerlukannya seperti beberapa versi MySQL). Sebaliknya, `sqlc` mem-parsing DDL ini untuk membangun model internal dari skema Anda.

### Tipe Data yang Didukung

`sqlc` mendukung berbagai tipe data SQL standar dan spesifik vendor. Pemetaan default ke tipe Go umumnya intuitif.

**Pemetaan Umum (dapat bervariasi sedikit antar engine):**

| Tipe SQL (Contoh PostgreSQL) | Tipe Go Default                         | Tipe Go jika NULLABLE                                     |
| :--------------------------- | :-------------------------------------- | :-------------------------------------------------------- |
| `BOOLEAN`                    | `bool`                                  | `sql.NullBool` / `pgtype.Bool` (jika `sql_package:"pgx/vN"`) |
| `SMALLINT`, `INT2`           | `int16`                                 | `sql.NullInt16` / `pgtype.Int2`                           |
| `INTEGER`, `INT`, `INT4`     | `int32`                                 | `sql.NullInt32` / `pgtype.Int4`                           |
| `BIGINT`, `INT8`             | `int64`                                 | `sql.NullInt64` / `pgtype.Int8`                           |
| `REAL`, `FLOAT4`             | `float32`                               | `sql.NullFloat64` (ya, `Float64` untuk `float32` SQL) / `pgtype.Float4` |
| `DOUBLE PRECISION`, `FLOAT8` | `float64`                               | `sql.NullFloat64` / `pgtype.Float8`                       |
| `NUMERIC`, `DECIMAL`         | `string` (default, untuk presisi)       | `sql.NullString` / `pgtype.Numeric` (bisa dioverride)   |
| `TEXT`, `VARCHAR`, `CHAR`    | `string`                                | `sql.NullString` / `pgtype.Text`                          |
| `BYTEA` (PG), `BLOB` (MySQL/SQLite) | `[]byte`                           | `[]byte` (slice bisa `nil`)                               |
| `JSON`, `JSONB` (PG)         | `[]byte`                                | `[]byte` (slice bisa `nil`) (bisa dioverride ke `json.RawMessage` atau struct kustom) |
| `DATE`                       | `time.Time`                             | `sql.NullTime` / `pgtype.Date`                            |
| `TIMESTAMP`, `TIMESTAMPTZ`   | `time.Time`                             | `sql.NullTime` / `pgtype.Timestamp`, `pgtype.Timestamptz` |
| `UUID` (PG)                  | `github.com/google/uuid.UUID` (default baru) / `[16]byte` (lama) | `github.com/google/uuid.NullUUID` / `pgtype.UUID`       |
| `ENUM` (PG/MySQL)            | `string` (atau tipe kustom jika didefinisikan) | `sql.NullString` (atau tipe kustom nullable)              |
| `ARRAY` (misal `TEXT[]` PG)  | `[]string`                              | `pq.StringArray` / `[]string` (tergantung driver/pgx)     |

Anda dapat menggunakan `overrides` dalam `sqlc.yaml` untuk menyesuaikan pemetaan ini. (Lihat bagian [Overrides Tipe Data](#overrides-tipe-data-overrides)).

### Contoh Skema untuk PostgreSQL

```sql
-- db/migrations/001_init_schema.sql

-- Ekstensi jika diperlukan (sqlc akan mengabaikannya jika tidak dikenali, tapi baik untuk reproduktifitas)
-- CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TYPE employment_status AS ENUM (
    'employed',
    'unemployed',
    'student',
    'retired'
);

CREATE TABLE departments (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL UNIQUE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE employees (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(), -- Menggunakan fungsi dari uuid-ossp jika diaktifkan, atau gen_random_uuid() bawaan PG13+
    department_id INTEGER REFERENCES departments(id) ON DELETE SET NULL,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    email TEXT NOT NULL UNIQUE,
    phone_number TEXT,
    hire_date DATE NOT NULL,
    salary NUMERIC(10, 2) NOT NULL CHECK (salary > 0),
    status employment_status NOT NULL,
    is_manager BOOLEAN NOT NULL DEFAULT FALSE,
    metadata JSONB,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_employees_department_id ON employees (department_id);
CREATE INDEX idx_employees_email ON employees (email);
CREATE INDEX idx_employees_status ON employees (status);

-- Tabel untuk relasi many-to-many antara employees dan projects
CREATE TABLE projects (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL UNIQUE,
    start_date DATE,
    end_date DATE
);

CREATE TABLE employee_projects (
    employee_id UUID NOT NULL REFERENCES employees(id) ON DELETE CASCADE,
    project_id INTEGER NOT NULL REFERENCES projects(id) ON DELETE CASCADE,
    assigned_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    role TEXT,
    PRIMARY KEY (employee_id, project_id)
);
```

### Contoh Skema untuk MySQL

```sql
-- db/migrations/001_init_schema.mysql.sql

CREATE TABLE departments (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL UNIQUE,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE employees (
    id BINARY(16) PRIMARY KEY, -- Untuk UUID, MySQL tidak punya tipe UUID native sebaik PG
    department_id INT,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    phone_number VARCHAR(50),
    hire_date DATE NOT NULL,
    salary DECIMAL(10, 2) NOT NULL, -- CHECK constraint tidak didukung secara universal sebelum MySQL 8.0.16
    status ENUM('employed', 'unemployed', 'student', 'retired') NOT NULL,
    is_manager BOOLEAN NOT NULL DEFAULT FALSE,
    metadata JSON,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (department_id) REFERENCES departments(id) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE INDEX idx_employees_department_id ON employees (department_id);
CREATE INDEX idx_employees_email ON employees (email);
-- MySQL otomatis membuat index untuk ENUM jika diperlukan.

CREATE TABLE projects (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL UNIQUE,
    start_date DATE,
    end_date DATE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE employee_projects (
    employee_id BINARY(16) NOT NULL,
    project_id INT NOT NULL,
    assigned_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    role VARCHAR(100),
    PRIMARY KEY (employee_id, project_id),
    FOREIGN KEY (employee_id) REFERENCES employees(id) ON DELETE CASCADE,
    FOREIGN KEY (project_id) REFERENCES projects(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```
**Catatan MySQL:**
*   `sqlc` mungkin memerlukan koneksi ke basis data MySQL yang berjalan (via opsi `database` di `sqlc.yaml`) untuk beberapa versi MySQL yang lebih tua agar dapat menginspeksi skema dengan benar. Versi MySQL modern lebih baik dalam hal ini.
*   Untuk UUID, Anda bisa menggunakan `BINARY(16)` dan mengonversinya di aplikasi, atau `VARCHAR(36)`.
*   MySQL `ENUM` akan dipetakan ke tipe string Go, atau Anda bisa membuat tipe Go kustom dan menggunakan `overrides`.

### Contoh Skema untuk SQLite

```sql
-- db/migrations/001_init_schema.sqlite.sql

CREATE TABLE departments (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL UNIQUE,
    created_at TEXT NOT NULL DEFAULT (STRFTIME('%Y-%m-%d %H:%M:%f', 'NOW')) -- SQLite menyimpan datetime sebagai TEXT, REAL, atau INTEGER
);

CREATE TABLE employees (
    id TEXT PRIMARY KEY, -- Biasanya untuk UUID, disimpan sebagai TEXT
    department_id INTEGER,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    email TEXT NOT NULL UNIQUE,
    phone_number TEXT,
    hire_date TEXT NOT NULL, -- Simpan sebagai YYYY-MM-DD
    salary REAL NOT NULL, -- SQLite tidak punya DECIMAL, REAL adalah float
    status TEXT NOT NULL CHECK (status IN ('employed', 'unemployed', 'student', 'retired')), -- ENUM disimulasikan dengan CHECK
    is_manager INTEGER NOT NULL DEFAULT 0, -- BOOLEAN disimulasikan dengan INTEGER 0 atau 1
    metadata TEXT, -- JSON disimpan sebagai TEXT
    updated_at TEXT NOT NULL DEFAULT (STRFTIME('%Y-%m-%d %H:%M:%f', 'NOW')),
    FOREIGN KEY (department_id) REFERENCES departments(id) ON DELETE SET NULL
);

CREATE INDEX idx_employees_department_id ON employees (department_id);
CREATE INDEX idx_employees_email ON employees (email);
CREATE INDEX idx_employees_status ON employees (status);

CREATE TABLE projects (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL UNIQUE,
    start_date TEXT,
    end_date TEXT
);

CREATE TABLE employee_projects (
    employee_id TEXT NOT NULL,
    project_id INTEGER NOT NULL,
    assigned_at TEXT NOT NULL DEFAULT (STRFTIME('%Y-%m-%d %H:%M:%f', 'NOW')),
    role TEXT,
    PRIMARY KEY (employee_id, project_id),
    FOREIGN KEY (employee_id) REFERENCES employees(id) ON DELETE CASCADE,
    FOREIGN KEY (project_id) REFERENCES projects(id) ON DELETE CASCADE
);
```
**Catatan SQLite:**
*   SQLite memiliki sistem tipe yang lebih dinamis. `sqlc` mencoba memetakannya ke tipe Go yang paling masuk akal.
*   `BOOLEAN` biasanya disimpan sebagai `INTEGER` (0 atau 1).
*   `DATETIME` biasanya disimpan sebagai `TEXT` (ISO8601) atau `INTEGER` (Unix timestamp). `sqlc` dapat memetakan ini ke `time.Time` dengan `overrides` jika diperlukan, meskipun defaultnya mungkin sudah cukup baik.
*   `JSON` disimpan sebagai `TEXT`. Anda perlu menangani parsing/serializing JSON secara manual atau menggunakan `overrides`.
*   `ENUM` disimulasikan menggunakan `TEXT` dan `CHECK` constraint.

### Mengelola Migrasi Skema

`sqlc` tidak menangani migrasi skema basis data. Ia hanya membaca file skema DDL Anda untuk memahami keadaan *saat ini* atau *target* dari skema. Anda bertanggung jawab untuk menerapkan perubahan skema ini ke basis data Anda menggunakan alat migrasi.

Alat migrasi populer untuk Go meliputi:
*   **`golang-migrate/migrate`**: Alat migrasi basis data yang sangat populer, mendukung banyak basis data. Bekerja dengan file migrasi SQL `up` dan `down`.
*   **`pressly/goose`**: Alat migrasi lain yang solid, juga menggunakan file SQL.
*   **`Atlas`**: Alat manajemen skema modern yang dapat melakukan migrasi deklaratif (mirip Terraform untuk basis data) dan juga mendukung migrasi berbasis versi. `Atlas` bisa menghasilkan migrasi dari perubahan skema atau membandingkan skema target dengan basis data.

**Alur Kerja Umum dengan Alat Migrasi:**
1.  Anda membuat file migrasi baru (misalnya, `003_add_bio_to_employees.sql`) menggunakan alat migrasi Anda.
    ```sql
    -- File migrasi UP:
    ALTER TABLE employees ADD COLUMN bio TEXT;

    -- File migrasi DOWN:
    ALTER TABLE employees DROP COLUMN bio;
    ```
2.  Anda memperbarui file skema utama yang dibaca `sqlc` (misalnya, `schema.sql` yang berisi gabungan semua migrasi `up`) agar mencerminkan perubahan ini. Beberapa orang memilih untuk `sqlc` membaca direktori file migrasi `up` secara langsung.
    ```sql
    -- schema.sql (setelah migrasi)
    -- ... definisi tabel employees sebelumnya ...
    ALTER TABLE employees ADD COLUMN bio TEXT; -- atau lebih baik, sertakan kolom bio dalam CREATE TABLE awal jika ini adalah skema "saat ini"
    ```
    Lebih umum, `schema.sql` adalah representasi "gabungan" dari semua migrasi `up` Anda. Jadi, jika Anda menambahkan kolom `bio`, Anda akan mengedit `CREATE TABLE employees` di `schema.sql` untuk menyertakan `bio TEXT`.
3.  Anda menjalankan `sqlc generate` untuk memperbarui kode Go Anda.
4.  Anda menjalankan alat migrasi Anda untuk menerapkan perubahan ke basis data aktual (pengembangan, staging, produksi).

**Penting**: Pastikan skema yang dilihat `sqlc` (dari file `schema` di `sqlc.yaml`) selalu sinkron dengan keadaan basis data yang diharapkan oleh aplikasi Anda. Jika tidak, kode yang dihasilkan `sqlc` mungkin tidak cocok dengan basis data aktual, yang menyebabkan error runtime.

---

## 7. Menulis Kueri SQL untuk sqlc

Setelah skema basis data Anda didefinisikan, langkah selanjutnya adalah menulis kueri SQL yang akan digunakan aplikasi Anda untuk berinteraksi dengan basis data. `sqlc` akan mengubah kueri ini menjadi fungsi-fungsi Go yang _type-safe_.

### File `query.sql`

Anda menulis kueri SQL dalam file `.sql` biasa. Path ke file-file ini atau direktori yang mengandungnya ditentukan dalam `sqlc.yaml` di bawah kunci `queries`.

Contoh:
```yaml
# sqlc.yaml
version: "2"
sql:
  - engine: "postgresql"
    schema: "db/migration/schema.sql"
    queries: "db/query/user_queries.sql" # Satu file kueri
    # ATAU
    # queries: ["db/query/user_queries.sql", "db/query/post_queries.sql"] # Beberapa file
    # ATAU
    # queries: "db/query/" # Direktori berisi semua file .sql kueri
    gen:
      go:
        package: "db"
        out: "internal/db"
```

### Anotasi Kueri sqlc

Setiap kueri yang ingin Anda ubah menjadi fungsi Go harus diawali dengan komentar anotasi khusus. Anotasi ini memberitahu `sqlc` nama fungsi yang akan dihasilkan dan jenis operasi kueri.

Format anotasi:
```sql
-- name: FunctionName :QueryType
```

*   `-- name:`: Kata kunci yang menandakan dimulainya definisi kueri `sqlc`.
*   `FunctionName`: Nama yang Anda inginkan untuk fungsi Go yang dihasilkan. Ini harus mengikuti konvensi penamaan Go (misalnya, diawali huruf besar untuk diekspor).
*   `:QueryType`: Menentukan berapa banyak baris yang diharapkan dari kueri dan apakah kueri memodifikasi data. Jenis kueri yang valid adalah:
    *   **:one**: Kueri diharapkan mengembalikan tepat satu baris. Jika kueri mengembalikan nol baris, fungsi Go akan mengembalikan `sql.ErrNoRows` (atau error serupa tergantung `sql_package`). Jika mengembalikan lebih dari satu baris, hanya baris pertama yang akan diproses dan dikembalikan.
        *   Contoh: `SELECT * FROM users WHERE id = $1;`
        *   Hasil Go: `func (q *Queries) GetUser(ctx context.Context, id int64) (User, error)`
    *   **:many**: Kueri diharapkan mengembalikan nol atau lebih baris.
        *   Contoh: `SELECT * FROM users;`
        *   Hasil Go: `func (q *Queries) ListUsers(ctx context.Context) ([]User, error)`
    *   **:exec**: Kueri tidak mengembalikan baris. Biasanya digunakan untuk `INSERT` (tanpa `RETURNING`), `UPDATE` (tanpa `RETURNING`), `DELETE` (tanpa `RETURNING`), atau DDL.
        *   Contoh: `DELETE FROM users WHERE id = $1;`
        *   Hasil Go: `func (q *Queries) DeleteUser(ctx context.Context, id int64) error`
    *   **:execrows**: Mirip dengan `:exec`, tetapi fungsi Go yang dihasilkan akan mengembalikan jumlah baris yang terpengaruh oleh operasi (menggunakan `sql.Result.RowsAffected()`).
        *   Contoh: `UPDATE users SET status = 'inactive' WHERE last_login < $1;`
        *   Hasil Go: `func (q *Queries) DeactivateUsers(ctx context.Context, lastLoginTime time.Time) (int64, error)`
    *   **:copyfrom** (Khusus PostgreSQL): Digunakan untuk operasi `COPY FROM STDIN`. Ini menghasilkan metode yang menerima slice dari struct untuk penyisipan data massal yang efisien.
        *   Contoh: `COPY users (name, email) FROM STDIN;`
        *   Hasil Go: Akan menghasilkan struct seperti `UsersCopyFromSource` dan metode yang sesuai. (Lihat bagian Fitur Lanjutan).

### Parameter dalam Kueri

`sqlc` secara otomatis mendeteksi parameter dalam kueri SQL Anda dan menambahkannya sebagai argumen ke fungsi Go yang dihasilkan.

#### Placeholder Standar Basis Data

*   **PostgreSQL**: Menggunakan placeholder bernomor seperti `$1`, `$2`, dst.
    ```sql
    -- name: GetUserByIDAndStatus :one
    SELECT * FROM users WHERE id = $1 AND status = $2;
    -- Hasil Go: func (q *Queries) GetUserByIDAndStatus(ctx context.Context, id int64, status string) (User, error)
    ```
*   **MySQL**: Menggunakan placeholder tanda tanya `?`. `sqlc` akan menamakannya secara berurutan (arg, arg_2, arg_3, ... atau Anda bisa menggunakan `sqlc.arg` untuk nama kustom).
    ```sql
    -- name: GetUserByIDAndStatus :one
    SELECT * FROM users WHERE id = ? AND status = ?;
    -- Hasil Go (parameter mungkin perlu dinamai dengan sqlc.arg): func (q *Queries) GetUserByIDAndStatus(ctx context.Context, id int64, status string) (User, error)
    ```
*   **SQLite**: Mendukung `$1`, `?1`, `@name`, `:name`. Disarankan menggunakan `$1` atau `?1` untuk konsistensi dengan driver Go.

#### Anotasi `sqlc.arg()`

Jika Anda ingin memberi nama parameter Go secara eksplisit, atau jika kueri Anda menggunakan parameter bernama (misalnya di SQLite `@name`), Anda bisa menggunakan fungsi `sqlc.arg()` dalam komentar SQL sebelum kueri atau dalam kueri itu sendiri (jika engine mendukungnya). Namun, praktik yang lebih umum adalah membiarkan `sqlc` menentukannya dari placeholder atau menggunakan `sqlc.arg` untuk argumen struct.

Jika sebuah kueri memiliki banyak parameter, `sqlc` akan otomatis membuat struct parameter. Misalnya, `CreateUserParams`. Anda dapat mengontrol nama field dalam struct ini menggunakan `sqlc.arg()`:

```sql
-- name: CreateUser :one
-- Parameter struct akan memiliki field `Username` dan `HashedPassword`
INSERT INTO users (username, email, password_hash)
VALUES (sqlc.arg(username), sqlc.arg(email), sqlc.arg(hashed_password))
RETURNING *;
```
Akan menghasilkan:
```go
type CreateUserParams struct {
    Username       string
    Email          string
    HashedPassword string
}

func (q *Queries) CreateUser(ctx context.Context, arg CreateUserParams) (User, error) {
    // ... implementasi ...
}
```
Jika tanpa `sqlc.arg()`, `sqlc` akan mencoba menggunakan nama kolom jika itu adalah `INSERT` atau `UPDATE`, atau menghasilkan nama generik.
Untuk `INSERT INTO users (username, email) VALUES ($1, $2)` maka `sqlc` akan cerdas menamai parameter `username` dan `email`.

Placeholder `sqlc.arg()` juga bisa digunakan langsung dalam kueri jika engine SQL Anda mendukung sintaks komentar atau fungsi yang tidak dikenal:
```sql
-- name: FindUsers :many
SELECT * FROM users
WHERE
  -- sqlc.arg(name_filter) adalah TEXT
  -- sqlc.arg(email_filter) adalah TEXT
  (name_filter IS NULL OR name ILIKE name_filter)
  AND (email_filter IS NULL OR email ILIKE email_filter);
```
Namun, cara yang lebih umum adalah cukup menggunakan placeholder ordinal (`$1`, `$2` atau `?`) dan biarkan `sqlc` menamainya secara otomatis atau gunakan struct parameter jika ada banyak argumen.

#### Anotasi `sqlc.slice()`

Untuk klausa `IN` atau operator `ANY` (di PostgreSQL), Anda sering ingin meneruskan slice Go sebagai parameter. Gunakan `sqlc.slice()`:

```sql
-- name: ListUsersByIDs :many
-- Menerima slice int64 untuk IDs
SELECT * FROM users
WHERE id = ANY(sqlc.slice(ids)); -- Untuk PostgreSQL

-- name: ListUsersByIDsMySQL :many
-- Untuk MySQL atau SQLite (memerlukan driver yang mendukung ekspansi slice)
SELECT * FROM users
WHERE id IN (sqlc.slice(ids));
```
Ini akan menghasilkan parameter `ids []int64` dalam fungsi Go. `sqlc` akan menangani ekspansi parameter yang diperlukan oleh driver (misalnya, `pq.Array` untuk PostgreSQL, atau ekspansi `(?,?,?)` untuk MySQL/SQLite jika driver mendukungnya).

**Perhatian untuk MySQL/SQLite dengan `IN (sqlc.slice(ids))`**:
Tidak semua driver `database/sql` mendukung ekspansi `IN (?)` menjadi `IN (?,?,?)` secara otomatis. `sqlc` sendiri tidak melakukan ekspansi ini; ia bergantung pada driver. Jika driver Anda tidak mendukungnya (seperti `go-sql-driver/mysql` standar), kueri `IN (sqlc.slice(foo))` tidak akan berfungsi seperti yang diharapkan. Anda mungkin perlu menggunakan trik SQL seperti `FIND_IN_SET` di MySQL atau membangun kueri secara dinamis (yang bertentangan dengan filosofi `sqlc`). Untuk PostgreSQL, `ANY(sqlc.slice(ids))` adalah cara yang benar dan didukung dengan baik.

Alternatif untuk `IN` dengan `sqlc.slice` di MySQL/SQLite:
1.  **String yang digabungkan**: Meneruskan string ID yang dipisahkan koma dan menggunakan `FIND_IN_SET(id, ?)` di MySQL. Ini kurang ideal.
2.  **Tabel temporer**: Masukkan ID ke tabel temporer, lalu `JOIN`. Lebih kompleks.
3.  **Beberapa kueri**: Jika jumlah ID kecil, panggil kueri `:one` berulang kali.

Untuk PostgreSQL, `ANY(sqlc.slice(column_name))` adalah solusi terbaik.

#### Parameter Bernama (Engine Dependent)

Beberapa engine, seperti SQLite, mendukung parameter bernama seperti `@name` atau `:name`.
```sql
-- name: GetUser :one
SELECT * FROM users WHERE id = @user_id;
```
`sqlc` akan membuat parameter Go bernama `userID`.

### Mengembalikan Nilai

#### `SELECT *`

Jika Anda menggunakan `SELECT *`, `sqlc` akan menghasilkan struct yang cocok dengan semua kolom dari tabel yang dirujuk (misalnya, `User` jika `SELECT * FROM users`).
```sql
-- name: GetUser :one
SELECT * FROM users WHERE id = $1;
-- Mengembalikan: User, error
```

#### `SELECT <kolom1>, <kolom2>`

Jika Anda memilih kolom tertentu, `sqlc` akan menghasilkan struct _ad-hoc_ khusus untuk hasil kueri tersebut. Nama struct biasanya akan menjadi `<FunctionName>Row`.
```sql
-- name: GetUserNameAndEmail :one
SELECT name, email FROM users WHERE id = $1;
-- Mengembalikan: GetUserNameAndEmailRow, error
-- Dimana GetUserNameAndEmailRow struct { Name string; Email string }
```
Jika Anda ingin struct hasil kustom ini memiliki nama yang lebih baik, Anda bisa menggunakan `sqlc.embed` atau memastikan kueri mengembalikan semua kolom dari satu tabel utama dan mungkin beberapa kolom dari tabel join (yang akan menjadi field terpisah atau struct tersemat).

#### Klausul `RETURNING` (PostgreSQL dan beberapa DB lain)

Klausul `RETURNING` sangat berguna untuk operasi `INSERT`, `UPDATE`, `DELETE` untuk mendapatkan kembali data dari baris yang terpengaruh. `sqlc` mendukung ini dengan baik.
```sql
-- name: CreateUser :one
INSERT INTO users (name, email) VALUES ($1, $2)
RETURNING *;
-- Mengembalikan: User, error (seluruh baris User yang baru dibuat)

-- name: UpdateUserName :one
UPDATE users SET name = $1 WHERE id = $2
RETURNING id, name;
-- Mengembalikan: UpdateUserNameRow, error
-- Dimana UpdateUserNameRow struct { ID int64; Name string }
```

### Struktur dan Tipe Data yang Dihasilkan

*   **Struct Model**: Untuk setiap tabel dalam skema Anda (misalnya, `users`), `sqlc` biasanya menghasilkan struct Go (misalnya, `User`) di `models.go`.
*   **Struct Parameter**: Jika kueri memiliki lebih dari satu parameter (atau jika Anda menggunakan `sqlc.arg()` untuk membuat field struct), `sqlc` akan menghasilkan struct untuk parameter tersebut (misalnya, `CreateUserParams`) di file `<query_name>.sql.go`.
*   **Struct Hasil Kustom**: Jika kueri `SELECT` tidak mengembalikan semua kolom dari satu tabel (yaitu bukan `SELECT * FROM table_name`), `sqlc` akan menghasilkan struct hasil kustom (misalnya, `ListUserEmailsRow`) di `<query_name>.sql.go`.

Tipe data Go yang dihasilkan didasarkan pada tipe kolom SQL dalam skema Anda dan `overrides` yang mungkin Anda tentukan dalam `sqlc.yaml`.
*   Kolom `NOT NULL` akan dipetakan ke tipe Go langsung (misalnya, `TEXT NOT NULL` -> `string`).
*   Kolom `NULLABLE` akan dipetakan ke tipe `sql.NullX` (misalnya, `TEXT` (nullable) -> `sql.NullString`) atau tipe pointer/kustom jika `sql_package` adalah `pgx/vN` atau jika ada `overrides`.

### Contoh Kueri Umum

Menggunakan skema `employees` dan `departments` dari contoh sebelumnya (PostgreSQL):

#### Membuat Data (Create)

```sql
-- name: CreateDepartment :one
INSERT INTO departments (name)
VALUES ($1)
RETURNING *;

-- name: CreateEmployee :one
INSERT INTO employees (
    department_id, first_name, last_name, email, phone_number,
    hire_date, salary, status, is_manager, metadata
) VALUES (
    $1, $2, $3, $4, $5, $6, $7, $8, $9, $10
) RETURNING *;
```
Ini akan menghasilkan fungsi `CreateDepartment` yang mengembalikan `Department` dan `CreateEmployee` yang mengembalikan `Employee`. Keduanya juga mengembalikan `error`. Parameter untuk `CreateEmployee` akan dikelompokkan ke dalam struct `CreateEmployeeParams`.

#### Membaca Data Tunggal (Read One)

```sql
-- name: GetDepartment :one
SELECT * FROM departments
WHERE id = $1;

-- name: GetEmployeeByEmail :one
SELECT * FROM employees
WHERE email = $1;
```
Fungsi `GetDepartment` mengembalikan `Department, error`. Fungsi `GetEmployeeByEmail` mengembalikan `Employee, error`. Jika tidak ada baris yang ditemukan, error `sql.ErrNoRows` akan dikembalikan.

#### Membaca Banyak Data (Read Many)

```sql
-- name: ListDepartments :many
SELECT * FROM departments
ORDER BY name;

-- name: ListEmployeesByDepartment :many
SELECT * FROM employees
WHERE department_id = $1
ORDER BY last_name, first_name;

-- name: ListEmployeesWithSalaryAbove :many
-- Menggunakan sqlc.arg untuk menamai parameter Go secara eksplisit jika nama kolom tidak cocok
SELECT id, first_name, last_name, email, salary
FROM employees
WHERE salary > sqlc.arg(min_salary)
ORDER BY salary DESC;
```
Fungsi `ListDepartments` mengembalikan `[]Department, error`. Fungsi `ListEmployeesByDepartment` mengembalikan `[]Employee, error`. Fungsi `ListEmployeesWithSalaryAbove` akan mengembalikan slice dari struct `ListEmployeesWithSalaryAboveRow`, error.

#### Memperbarui Data (Update)

```sql
-- name: UpdateEmployeeSalary :one
UPDATE employees
SET salary = $1, updated_at = NOW()
WHERE id = $2
RETURNING *;

-- name: PromoteToManager :execrows
-- Menggunakan :execrows untuk mendapatkan jumlah baris yang terpengaruh
UPDATE employees
SET is_manager = TRUE, updated_at = NOW()
WHERE id = $1 AND is_manager = FALSE;
```
Fungsi `UpdateEmployeeSalary` mengembalikan `Employee, error`. Fungsi `PromoteToManager` mengembalikan `int64, error` (jumlah baris yang diupdate).

#### Menghapus Data (Delete)

```sql
-- name: DeleteEmployee :exec
DELETE FROM employees
WHERE id = $1;

-- name: DeleteDepartmentAndOrphanEmployees :execrows
-- Contoh kueri yang lebih kompleks, mungkin dalam CTE atau beberapa statement jika didukung
-- Ini hanya contoh, implementasi sebenarnya akan bergantung pada kebutuhan
-- Untuk menghapus departemen dan kemudian karyawan tanpa departemen (jika foreign key adalah SET NULL)
-- Ini lebih baik dilakukan dalam transaksi di sisi aplikasi.
-- Untuk contoh :execrows:
DELETE FROM departments
WHERE id = $1;
-- (Ini akan mengembalikan jumlah departemen yang dihapus)
```
Fungsi `DeleteEmployee` mengembalikan `error`.

### Menggunakan Fungsi SQL dan Ekspresi

Anda dapat menggunakan fungsi SQL bawaan dan berbagai ekspresi dalam kueri Anda. `sqlc` akan mencoba menentukan tipe hasil.

```sql
-- name: GetEmployeeCount :one
SELECT COUNT(*) FROM employees;
-- Hasil Go: func (...) (int64, error)

-- name: GetAverageSalaryByDepartment :many
SELECT department_id, AVG(salary) as average_salary
FROM employees
GROUP BY department_id;
-- Hasil Go: func (...) ([]GetAverageSalaryByDepartmentRow, error)
-- GetAverageSalaryByDepartmentRow struct { DepartmentID sql.NullInt32; AverageSalary interface{} }
-- Tipe `AverageSalary` mungkin `interface{}` atau `pgtype.Numeric` jika sqlc tidak yakin.
-- Anda mungkin perlu `override` tipe ini ke `float64` atau `string` (untuk `NUMERIC`).
```
Jika tipe hasil dari fungsi SQL tidak jelas, `sqlc` mungkin memetakannya ke `interface{}` atau tipe byte slice. Gunakan `overrides` dalam `sqlc.yaml` jika Anda membutuhkan tipe yang lebih spesifik dan Anda tahu apa seharusnya.

Contoh override untuk `AVG(salary)` (jika salary adalah `NUMERIC`):
```yaml
# sqlc.yaml
# ...
gen:
  go:
    # ...
    overrides:
      - column: "get_average_salary_by_department.average_salary" # nama_kueri.nama_kolom_hasil
        # db_type: "numeric" # Bisa juga pakai db_type jika tipe diketahui dan konsisten
        go_type: "string" # Atau float64 jika presisi tidak terlalu kritikal dan konversi aman
```

### Komentar SQL

Komentar SQL standar (`--` untuk satu baris, `/* ... */` untuk blok) dapat digunakan seperti biasa dalam file kueri Anda. `sqlc` hanya peduli pada komentar anotasi `-- name:` khusus.

```sql
-- File: db/query/complex_report.sql

/*
  Kueri ini menghasilkan laporan ringkasan penjualan bulanan.
  Ini menggabungkan beberapa tabel dan menggunakan window functions.
*/
-- name: MonthlySalesReport :many
WITH monthly_sales AS (
    SELECT
        EXTRACT(YEAR FROM order_date) AS sale_year,
        EXTRACT(MONTH FROM order_date) AS sale_month,
        product_id,
        SUM(quantity * unit_price) AS total_revenue
    FROM orders
    JOIN order_items ON orders.id = order_items.order_id
    WHERE orders.status = 'completed' -- Hanya pesanan yang selesai
    GROUP BY 1, 2, 3
)
SELECT
    sale_year,
    sale_month,
    p.name AS product_name,
    ms.total_revenue,
    SUM(ms.total_revenue) OVER (PARTITION BY sale_year, sale_month) AS total_monthly_revenue
FROM monthly_sales ms
JOIN products p ON ms.product_id = p.id
ORDER BY sale_year, sale_month, total_revenue DESC;
```
`sqlc` akan mem-parsing kueri kompleks ini dan menghasilkan struct `MonthlySalesReportRow` yang sesuai dengan kolom `sale_year`, `sale_month`, `product_name`, `total_revenue`, dan `total_monthly_revenue`, beserta tipe data Go yang paling cocok.

Menulis kueri yang baik adalah inti dari penggunaan `sqlc`. Semakin nyaman Anda dengan SQL, semakin efektif Anda dapat menggunakan `sqlc`.

---

## 8. Menghasilkan Kode Go dengan `sqlc generate`

Setelah Anda menyiapkan `sqlc.yaml`, file skema, dan file kueri, langkah berikutnya adalah menghasilkan kode Go. Ini dilakukan dengan perintah `sqlc generate`.

### Perintah Dasar

Dari direktori root proyek Anda (atau direktori mana pun yang berisi `sqlc.yaml`, atau di mana `sqlc.yaml` dapat ditemukan dengan naik ke direktori induk), jalankan:
```bash
sqlc generate
```

Jika Anda ingin menjalankan untuk file konfigurasi tertentu (misalnya, jika tidak bernama `sqlc.yaml` atau tidak di lokasi standar):
```bash
sqlc -f path/to/your/config.yaml generate
```

Jika proses berhasil, `sqlc` akan membuat atau memperbarui file-file Go di direktori `out` yang ditentukan dalam `sqlc.yaml` untuk setiap paket SQL. Tidak akan ada output di konsol kecuali terjadi error.

Jika ada error (misalnya, sintaks SQL salah, nama kolom tidak ditemukan, anotasi salah), `sqlc` akan mencetak pesan error yang detail untuk membantu Anda memperbaikinya.

**Opsi Tambahan `sqlc generate`:**
*   `-d`, `--debug`: Mengaktifkan output debugging, termasuk kueri internal yang digunakan `sqlc` untuk menginspeksi skema (terutama relevan untuk MySQL).

### Struktur Direktori Hasil

Berdasarkan konfigurasi `gen: go:` di `sqlc.yaml`, `sqlc` akan menghasilkan beberapa file. Misalkan konfigurasi Anda:
```yaml
# sqlc.yaml
# ...
gen:
  go:
    package: "store"
    out: "internal/db/store"
    # ...
```
Dan Anda memiliki file kueri bernama `products.sql` dan `orders.sql`. Maka, `sqlc` akan menghasilkan struktur berikut di dalam `internal/db/store/`:

*   **`db.go`** (atau nama kustom dari `output_db_file_name`):
    *   Jika `emit_interface: true`: Berisi definisi interface `Querier` dan fungsi `New(DBTX) *Queries`. Interface `Querier` mencantumkan semua metode kueri yang dihasilkan.
    *   Struct `Queries` yang mengimplementasikan `Querier` (atau hanya berisi metode jika `emit_interface: false`). Struct ini memiliki field untuk `DBTX` (koneksi atau transaksi basis data).
    *   Tipe `DBTX` itu sendiri, yang merupakan interface yang diabstraksi dari `*sql.DB`, `*sql.Tx`, atau tipe koneksi/transaksi `pgx` jika `sql_package` disetel ke `pgx/vN`.

*   **`models.go`** (atau nama kustom dari `output_models_file_name`):
    *   Berisi definisi struct Go untuk setiap tabel dalam skema Anda (misalnya, `Product`, `Order`).
    *   Juga berisi struct Go untuk tipe enum kustom yang mungkin dihasilkan `sqlc` jika Anda menggunakan tipe `ENUM` di PostgreSQL/MySQL dan tidak melakukan override ke tipe string sederhana.
    *   Struktur ini digunakan sebagai tipe kembalian untuk kueri yang mengembalikan seluruh baris tabel (misalnya, `SELECT *`) atau sebagai tipe untuk parameter dalam operasi `:copyfrom`.

*   **`<query_file_name>.sql.go`** (misalnya, `products.sql.go`, `orders.sql.go`):
    *   Untuk setiap file kueri `.sql` yang Anda miliki (misalnya, `products.sql`), `sqlc` akan menghasilkan file Go yang sesuai (misalnya, `products.sql.go`).
    *   File ini berisi implementasi aktual dari fungsi-fungsi Go untuk setiap kueri yang didefinisikan dalam file `.sql` sumbernya.
    *   Jika kueri memerlukan struct parameter kustom (misalnya, `CreateProductParams`) atau struct hasil kustom (misalnya, `ListProductNamesRow`), definisi struct tersebut juga akan ada di file ini.
    *   Jika `output_querier_file_name` disetel, semua implementasi kueri akan masuk ke file tunggal tersebut.

*   **`copyfrom.go`** (atau nama kustom dari `output_copyfrom_file_name`):
    *   Hanya dihasilkan jika Anda memiliki setidaknya satu kueri dengan tipe `:copyfrom` (khusus PostgreSQL).
    *   Berisi helper dan struct yang diperlukan untuk menggunakan protokol `COPY FROM STDIN` PostgreSQL.

Semua file yang dihasilkan akan memiliki header:
```go
// Code generated by sqlc. DO NOT EDIT.
// versions:
//   sqlc vX.Y.Z // Versi sqlc yang digunakan
// source: <nama_file_query>.sql // File SQL sumber
```
**PENTING**: Jangan pernah mengedit file-file yang dihasilkan `sqlc` secara manual. Perubahan Anda akan hilang saat Anda menjalankan `sqlc generate` berikutnya. Jika Anda perlu mengubah perilaku, ubah file `.sql` Anda atau konfigurasi `sqlc.yaml`, lalu jalankan ulang `sqlc generate`.

### Memahami Kode yang Dihasilkan

Mari kita lihat lebih detail isi dari file-file utama:

#### File `models.go`

Berisi representasi Go dari tabel dan enum Anda.

```go
// internal/db/store/models.go
package store

import (
	"database/sql" // atau "github.com/jackc/pgx/v5/pgtype" jika sql_package: "pgx/v5"
	"time"
)

// Contoh ENUM dari PostgreSQL (CREATE TYPE product_status AS ENUM (...))
type ProductStatus string

const (
	ProductStatusAvailable ProductStatus = "available"
	ProductStatusSoldOut   ProductStatus = "sold_out"
	ProductStatusPending   ProductStatus = "pending"
)

// Validasi untuk tipe enum kustom
func (e *ProductStatus) Scan(src interface{}) error { /* ... */ }
func (e ProductStatus) Value() (driver.Value, error) { /* ... */ }


type Product struct {
	ID          int64          `json:"id"` // Tag JSON jika emit_json_tags: true
	Name        string         `json:"name"`
	Description sql.NullString `json:"description"` // Untuk kolom TEXT NULLABLE
	Price       string         `json:"price"`       // Jika NUMERIC dipetakan ke string
	Status      ProductStatus  `json:"status"`      // Menggunakan tipe ENUM kustom
	CreatedAt   time.Time      `json:"created_at"`
	UpdatedAt   sql.NullTime   `json:"updated_at"`  // Untuk TIMESTAMPTZ NULLABLE
}

type Order struct {
	ID         int64     `json:"id"`
	CustomerID int64     `json:"customer_id"`
	OrderDate  time.Time `json:"order_date"`
	TotalAmount string   `json:"total_amount"`
}

// ... struct lain untuk tabel lain ...
```
*   Tipe data Go dipilih berdasarkan tipe kolom SQL dan _nullability_-nya.
*   `sql.NullString`, `sql.NullInt64`, `sql.NullTime`, dll., digunakan untuk kolom _nullable_ jika `sql_package` adalah `"database/sql"`.
*   Jika `sql_package` adalah `"pgx/v5"`, Anda akan melihat tipe seperti `pgtype.Text`, `pgtype.Int8`, `pgtype.Timestamptz` yang menangani _nullability_ secara internal.
*   Jika `emit_json_tags: true`, tag JSON akan ditambahkan. _Case style_ tag JSON dikontrol oleh `json_tags_case_style`.

#### File `<query_file_name>.sql.go`

Berisi fungsi-fungsi Go untuk kueri Anda dan struct parameter/hasil yang relevan.
Misalkan kita punya `products.sql` dengan kueri:
```sql
-- products.sql
-- name: GetProduct :one
SELECT * FROM products WHERE id = $1;

-- name: CreateProduct :one
INSERT INTO products (name, description, price, status)
VALUES ($1, $2, $3, $4)
RETURNING *;

-- name: ListProductNamesAndPrices :many
SELECT name, price FROM products WHERE status = $1;
```

Maka `products.sql.go` akan terlihat seperti ini (disederhanakan):
```go
// internal/db/store/products.sql.go
package store

import (
	"context"
	"database/sql" // atau pgtype jika pgx
)

const getProduct = `-- name: GetProduct :one
SELECT id, name, description, price, status, created_at, updated_at FROM products WHERE id = $1
`

func (q *Queries) GetProduct(ctx context.Context, id int64) (Product, error) {
	row := q.db.QueryRowContext(ctx, getProduct, id)
	var i Product
	err := row.Scan(
		&i.ID,
		&i.Name,
		&i.Description,
		&i.Price,
		&i.Status,
		&i.CreatedAt,
		&i.UpdatedAt,
	)
	return i, err // err bisa sql.ErrNoRows
}

const createProduct = `-- name: CreateProduct :one
INSERT INTO products (name, description, price, status)
VALUES ($1, $2, $3, $4)
RETURNING id, name, description, price, status, created_at, updated_at
`

// Struct parameter untuk CreateProduct
type CreateProductParams struct {
	Name        string         `json:"name"`
	Description sql.NullString `json:"description"`
	Price       string         `json:"price"`
	Status      ProductStatus  `json:"status"`
}

func (q *Queries) CreateProduct(ctx context.Context, arg CreateProductParams) (Product, error) {
	row := q.db.QueryRowContext(ctx, createProduct,
		arg.Name,
		arg.Description,
		arg.Price,
		arg.Status,
	)
	var i Product
	err := row.Scan(
		&i.ID,
		&i.Name,
		&i.Description,
		&i.Price,
		&i.Status,
		&i.CreatedAt,
		&i.UpdatedAt,
	)
	return i, err
}

const listProductNamesAndPrices = `-- name: ListProductNamesAndPrices :many
SELECT name, price FROM products WHERE status = $1
`

// Struct hasil kustom untuk ListProductNamesAndPrices
type ListProductNamesAndPricesRow struct {
	Name  string `json:"name"`
	Price string `json:"price"`
}

func (q *Queries) ListProductNamesAndPrices(ctx context.Context, status ProductStatus) ([]ListProductNamesAndPricesRow, error) {
	rows, err := q.db.QueryContext(ctx, listProductNamesAndPrices, status)
	if err != nil {
		return nil, err
	}
	defer rows.Close()
	var items []ListProductNamesAndPricesRow // atau items := []ListProductNamesAndPricesRow{} jika emit_empty_slices: true
	for rows.Next() {
		var i ListProductNamesAndPricesRow
		if err := rows.Scan(&i.Name, &i.Price); err != nil {
			return nil, err
		}
		items = append(items, i)
	}
	if err := rows.Close(); err != nil { // Periksa error saat menutup rows
		return nil, err
	}
	if err := rows.Err(); err != nil { // Periksa error iterasi
		return nil, err
	}
	return items, nil
}
```
*   Setiap fungsi kueri menerima `context.Context` sebagai argumen pertama. Ini adalah praktik terbaik di Go untuk operasi I/O.
*   Parameter kueri SQL (`$1`, `$2`, dll.) menjadi argumen fungsi Go. Jika ada banyak, mereka dikelompokkan dalam struct `Params`.
*   Kueri `:one` menggunakan `db.QueryRowContext()` dan `row.Scan()`.
*   Kueri `:many` menggunakan `db.QueryContext()`, lalu melakukan iterasi dengan `rows.Next()` dan `rows.Scan()`.
*   Kueri `:exec` akan menggunakan `db.ExecContext()`.
*   Kueri `:execrows` akan menggunakan `db.ExecContext()` dan kemudian mengembalikan `result.RowsAffected()`.
*   Teks kueri SQL mentah disematkan sebagai konstanta string. Jika `emit_prepared_queries: true`, logika akan berbeda; `sqlc` akan mengelola prepared statements.

#### File `db.go`

File ini mengikat semuanya.
```go
// internal/db/store/db.go
package store

import (
	"context"
	"database/sql"
	// "fmt" // mungkin ada jika ada prepared statements dan error handling
)

// DBTX adalah interface yang diabstraksi oleh sqlc untuk *sql.DB dan *sql.Tx.
// Ini memungkinkan metode kueri digunakan baik dengan koneksi DB langsung maupun dalam transaksi.
type DBTX interface {
	ExecContext(context.Context, string, ...interface{}) (sql.Result, error)
	PrepareContext(context.Context, string) (*sql.Stmt, error)
	QueryContext(context.Context, string, ...interface{}) (*sql.Rows, error)
	QueryRowContext(context.Context, string, ...interface{}) *sql.Row
}

// New membuat instance Queries baru.
// Jika emit_prepared_queries: false, ini akan sederhana.
// Jika emit_prepared_queries: true, ini akan mempersiapkan semua statement.
func New(db DBTX) *Queries {
	return &Queries{db: db}
}

// Queries adalah struct yang menampung DBTX dan (jika emit_prepared_queries: true) prepared statements.
type Queries struct {
	db DBTX
	// Jika emit_prepared_queries: true, akan ada field untuk setiap prepared statement, misalnya:
	// getProductStmt *sql.Stmt
	// createProductStmt *sql.Stmt
	// listProductNamesAndPricesStmt *sql.Stmt
}

// WithTx mengembalikan instance Queries baru yang menggunakan transaksi *sql.Tx yang diberikan.
// Ini berguna untuk menjalankan beberapa operasi kueri dalam satu transaksi atomik.
func (q *Queries) WithTx(tx *sql.Tx) *Queries {
	return &Queries{
		db: tx,
		// Jika emit_prepared_queries: true, prepared statements dari q asli harus disalin atau di-reprepare pada tx.
		// sqlc menangani ini secara internal.
	}
}

// Jika emit_interface: true, interface Querier akan didefinisikan:
type Querier interface {
	GetProduct(ctx context.Context, id int64) (Product, error)
	CreateProduct(ctx context.Context, arg CreateProductParams) (Product, error)
	ListProductNamesAndPrices(ctx context.Context, status ProductStatus) ([]ListProductNamesAndPricesRow, error)
	// ... semua metode kueri lainnya ...
}

// Pastikan *Queries mengimplementasikan Querier (compile-time check)
var _ Querier = (*Queries)(nil)
```

*   `DBTX`: Interface kunci yang memungkinkan fungsi `sqlc` bekerja dengan `*sql.DB` (untuk operasi tunggal) atau `*sql.Tx` (di dalam transaksi). Jika `sql_package` adalah `pgx/vN`, `DBTX` akan menggunakan interface `pgx`.
*   `New(db DBTX) *Queries`: Konstruktor untuk membuat instance `Queries`. Jika `emit_prepared_queries: true`, konstruktor ini juga akan bertanggung jawab untuk memanggil `db.PrepareContext()` untuk setiap kueri. Jika ada kegagalan dalam mempersiapkan statement, `New` akan mengembalikan error.
*   `Queries`: Struct yang menjadi _receiver_ untuk semua metode kueri. Ia menyimpan koneksi/transaksi `db`. Jika `emit_prepared_queries: true`, ia juga akan menyimpan semua `*sql.Stmt`.
*   `WithTx(tx *sql.Tx) *Queries`: Metode helper yang sangat berguna untuk menggunakan instance `Queries` yang ada di dalam konteks transaksi baru.
*   `Querier` (jika `emit_interface: true`): Interface yang mendefinisikan semua metode kueri. Ini sangat berguna untuk _mocking_ dalam pengujian dan untuk _dependency injection_.

Memahami struktur kode yang dihasilkan ini penting untuk menggunakan `sqlc` secara efektif dan untuk men-debug masalah jika muncul. Selalu ingat bahwa `sqlc generate` adalah teman Anda; jalankan setiap kali Anda mengubah skema atau kueri.

---

## 9. Menggunakan Kode yang Dihasilkan

Setelah Anda berhasil menghasilkan kode Go dengan `sqlc generate`, saatnya menggunakannya dalam aplikasi Anda untuk berinteraksi dengan basis data.

### Mengimpor Paket yang Dihasilkan

Pertama, impor paket Go yang dihasilkan `sqlc` ke dalam file Go tempat Anda ingin melakukan operasi basis data. Path impor akan sesuai dengan `module_path/path_to_out_directory`.

Jika modul Go Anda adalah `github.com/myuser/myproject` dan `out` direktori di `sqlc.yaml` adalah `internal/db`, maka impornya adalah:
```go
import (
    "github.com/myuser/myproject/internal/db" // db adalah nama paket dari sqlc.yaml
)
```

### Membuka Koneksi Basis Data

Anda memerlukan koneksi basis data yang aktif (`*sql.DB`) untuk digunakan dengan `sqlc`. Ini dilakukan menggunakan paket `database/sql` dan driver basis data yang sesuai.

#### Menggunakan `database/sql`

Ini adalah pendekatan standar dan berfungsi untuk semua basis data yang didukung `sqlc` jika `sql_package` tidak diubah dari default (`"database/sql"`).

```go
package main

import (
	"database/sql"
	"log"
	"os" // Untuk DSN dari env var

	// Impor driver yang sesuai (pilih salah satu)
	_ "github.com/lib/pq" // Untuk PostgreSQL
	// _ "github.com/go-sql-driver/mysql" // Untuk MySQL
	// _ "github.com/mattn/go-sqlite3" // Untuk SQLite
	// _ "github.com/jackc/pgx/v5/stdlib" // Untuk PostgreSQL menggunakan pgx sebagai driver database/sql
)

func main() {
	dbDriver := "postgres" // atau "mysql", "sqlite3"
	// DSN (Data Source Name) - AMBIL DARI KONFIGURASI ATAU ENV VAR!
	// Contoh: "user=pqgotest dbname=pqgotest sslmode=verify-full"
	// Contoh: "username:password@(hostname:port)/database_name?parseTime=true" (MySQL)
	// Contoh: "/path/to/your/database.db" (SQLite)
	dbSource := os.Getenv("DB_SOURCE")
	if dbSource == "" {
		log.Fatal("DB_SOURCE environment variable is not set")
	}

	conn, err := sql.Open(dbDriver, dbSource)
	if err != nil {
		log.Fatalf("Tidak dapat membuka koneksi DB: %v", err)
	}
	defer conn.Close()

	// Sangat disarankan untuk mengatur ini:
	// conn.SetMaxOpenConns(25)
	// conn.SetMaxIdleConns(25)
	// conn.SetConnMaxLifetime(5 * time.Minute)

	if err := conn.Ping(); err != nil {
		log.Fatalf("Tidak dapat melakukan ping ke DB: %v", err)
	}

	log.Println("Koneksi basis data berhasil!")

	// ... kode untuk menggunakan sqlc di sini ...
}
```

#### Contoh dengan PostgreSQL (`pgx` sebagai `sql_package`)

Jika Anda mengatur `sql_package: "pgx/v5"` (atau `v4`) di `sqlc.yaml`, kode yang dihasilkan akan mengharapkan koneksi `pgx` asli, bukan `*sql.DB`.

```go
package main

import (
	"context"
	"log"
	"os"

	"github.com/jackc/pgx/v5/pgxpool" // Menggunakan pgxpool untuk connection pool

	"github.com/myuser/myproject/internal/db" // Paket sqlc Anda
)

func main() {
	dbSource := os.Getenv("DB_SOURCE_PGX") // DSN untuk pgx, cth: "postgresql://user:pass@host:port/dbname"
	if dbSource == "" {
		log.Fatal("DB_SOURCE_PGX environment variable is not set")
	}

	// Menggunakan pgxpool untuk connection pool
	pool, err := pgxpool.New(context.Background(), dbSource)
	if err != nil {
		log.Fatalf("Tidak dapat membuat connection pool pgx: %v", err)
	}
	defer pool.Close()

	if err := pool.Ping(context.Background()); err != nil {
		log.Fatalf("Tidak dapat melakukan ping ke DB (pgx): %v", err)
	}
	log.Println("Koneksi basis data pgx berhasil!")

	// ... kode untuk menggunakan sqlc dengan pool pgx di sini ...
}
```
Saat menggunakan `pgxpool`, Anda akan meneruskan `pool` ke fungsi `db.New()`. Metode-metode di `*db.Queries` akan menggunakan `pool.Exec()`, `pool.QueryRow()`, dll. Untuk transaksi, Anda akan menggunakan `pool.Begin()` dan kemudian `q.WithTx(tx)`.

### Membuat Instance `Queries`

Setelah Anda memiliki koneksi basis data (`*sql.DB` atau `pgxpool.Pool` atau `pgx.Conn`), Anda dapat membuat instance dari struct `Queries` yang dihasilkan `sqlc`.

```go
// Jika menggunakan database/sql:
// 'conn' adalah *sql.DB Anda
// 'db' adalah alias untuk paket yang dihasilkan sqlc, misalnya "github.com/myuser/myproject/internal/db"
store := db.New(conn) // 'store' sekarang adalah *db.Queries

// Jika menggunakan pgx/v5 dengan pgxpool:
// 'pool' adalah *pgxpool.Pool Anda
storePgx := db.New(pool) // 'storePgx' juga *db.Queries, tetapi field db internalnya adalah pgx type
```
Fungsi `db.New()` (atau apa pun nama paket Anda) adalah konstruktor yang disediakan oleh `sqlc` di file `db.go` yang dihasilkannya.

Jika `emit_prepared_queries: true` diaktifkan, dan `sql_package: "database/sql"`, maka `db.New()` mungkin mengembalikan `(*db.Queries, error)` karena proses persiapan _statement_ bisa gagal. Anda harus menangani error ini.
```go
// Jika emit_prepared_queries: true
store, err := db.New(conn)
if err != nil {
    log.Fatalf("Gagal mempersiapkan kueri sqlc: %v", err)
}
```
Untuk `pgx/v5`, `db.New()` biasanya tidak mengembalikan error karena `pgx` menangani _prepared statements_ secara berbeda (seringkali secara otomatis di sisi server atau saat pertama kali digunakan).

### Memanggil Fungsi Kueri

Sekarang Anda dapat memanggil metode pada instance `store` (atau `storePgx`) untuk menjalankan kueri Anda.

```go
// Misalkan Anda memiliki kueri `GetUser(id int64)` dan `CreateUser(params CreateUserParams)`
ctx := context.Background() // Selalu gunakan context

// Contoh: Membuat pengguna baru
newUserParams := db.CreateUserParams{
	Username: "johndoe",
	Email:    "john.doe@example.com",
	// ... field lain ...
}
createdUser, err := store.CreateUser(ctx, newUserParams)
if err != nil {
	log.Printf("Error membuat pengguna: %v", err)
	// Tangani error (misalnya, email duplikat)
} else {
	log.Printf("Pengguna baru dibuat: %+v", createdUser)
}

// Contoh: Mendapatkan pengguna berdasarkan ID
userID := int64(123)
fetchedUser, err := store.GetUser(ctx, userID)
if err != nil {
	if err == sql.ErrNoRows { // Atau errors.Is(err, sql.ErrNoRows)
		log.Printf("Pengguna dengan ID %d tidak ditemukan.", userID)
	} else {
		log.Printf("Error mendapatkan pengguna: %v", err)
	}
} else {
	log.Printf("Pengguna ditemukan: %+v", fetchedUser)
}

// Contoh: Mendapatkan daftar pengguna
users, err := store.ListUsers(ctx) // Misalkan ada ListUsers :many
if err != nil {
    log.Printf("Error mendapatkan daftar pengguna: %v", err)
} else {
    if len(users) == 0 && !db.EmitEmptySlicesInConfig { // db.EmitEmptySlicesInConfig adalah pseudo-code, cek config Anda
        log.Println("Tidak ada pengguna ditemukan (slice adalah nil).")
    } else if len(users) == 0 {
        log.Println("Tidak ada pengguna ditemukan (slice kosong).")
    }
    for _, u := range users {
        log.Printf("Pengguna: %+v", u)
    }
}
```

**Ingat:**
*   Selalu teruskan `context.Context`. Ini penting untuk _cancellation_ dan _deadline propagation_.
*   Tangani error dengan benar, terutama `sql.ErrNoRows` untuk kueri `:one` yang mungkin tidak menemukan baris. Jika menggunakan `pgx/v5` langsung, error yang setara adalah `pgx.ErrNoRows`.

### Penanganan Error

Penanganan error yang baik sangat krusial.
*   **`sql.ErrNoRows` / `pgx.ErrNoRows`**: Untuk kueri `:one`, jika tidak ada baris yang cocok, error ini akan dikembalikan. Anda biasanya ingin menangani ini secara spesifik (misalnya, mengembalikan HTTP 404 Not Found).
    ```go
    import "errors" // Untuk errors.Is

    user, err := queries.GetUser(ctx, id)
    if err != nil {
        if errors.Is(err, sql.ErrNoRows) { // Atau pgx.ErrNoRows
            // Pengguna tidak ditemukan, tangani secara spesifik
            return nil, ErrUserNotFound // Error kustom Anda
        }
        // Error lain dari basis data atau koneksi
        return nil, fmt.Errorf("gagal GetUser: %w", err)
    }
    ```
*   **Error Constraint**: Seperti _unique constraint violation_ (misalnya, mencoba memasukkan email yang sudah ada). Driver basis data akan mengembalikan error spesifik.
    *   Untuk **PostgreSQL (`lib/pq`)**: Error akan bertipe `*pq.Error`. Anda dapat memeriksa `Code` (misalnya, `Code == "23505"` untuk _unique violation_).
        ```go
        import "github.com/lib/pq"

        _, err := queries.CreateUser(ctx, params)
        if err != nil {
            var pqErr *pq.Error
            if errors.As(err, &pqErr) {
                if pqErr.Code == "23505" { // Unique violation
                    // Periksa pqErr.ConstraintName jika perlu
                    return ErrDuplicateEmail // Error kustom Anda
                }
            }
            return fmt.Errorf("gagal CreateUser: %w", err)
        }
        ```
    *   Untuk **MySQL (`go-sql-driver/mysql`)**: Error akan bertipe `*mysql.MySQLError`. Anda dapat memeriksa `Number` (misalnya, `1062` untuk _duplicate entry_).
        ```go
        import "github.com/go-sql-driver/mysql"

        // ... sama, tapi periksa *mysql.MySQLError dan Number == 1062
        ```
    *   Untuk **SQLite (`mattn/go-sqlite3`)**: Error akan bertipe `sqlite3.Error`. Periksa `ExtendedCode` (misalnya, `sqlite3.ErrConstraintUnique`).
        ```go
        import "github.com/mattn/go-sqlite3"

        // ... sama, tapi periksa sqlite3.Error dan ExtendedCode
        ```
    *   Untuk **`pgx/v5`**: Error akan bertipe `*pgconn.PgError`. Periksa `Code`.
        ```go
        import "github.com/jackc/pgx/v5/pgconn"

        // ... sama, tapi periksa *pgconn.PgError dan Code == "23505"
        ```
    Menulis _helper function_ untuk memeriksa jenis error spesifik ini bisa sangat berguna.

### Bekerja dengan Transaksi

Seringkali Anda perlu menjalankan beberapa operasi basis data secara atomik dalam satu transaksi. `sqlc` membuat ini mudah dengan metode `WithTx(tx *sql.Tx)` atau dengan cara Anda menginisialisasi `*Queries` jika Anda menggunakan `sql_package: "pgx/vN"`.

#### Pola Transaksi Umum (`database/sql`)

```go
// store adalah *db.Queries yang diinisialisasi dengan *sql.DB

// Fungsi yang melakukan beberapa operasi dalam transaksi
func transferMoney(ctx context.Context, dbConn *sql.DB, store *db.Queries, fromAccountID, toAccountID int64, amount int64) error {
	// 1. Mulai transaksi
	tx, err := dbConn.BeginTx(ctx, nil) // nil untuk opsi transaksi default
	if err != nil {
		return fmt.Errorf("gagal memulai transaksi: %w", err)
	}
	// Defer rollback. Jika Commit berhasil, Rollback tidak akan melakukan apa-apa.
	// Ini adalah pola aman untuk memastikan Rollback selalu dipanggil jika terjadi error.
	defer tx.Rollback()

	// 2. Buat Querier baru yang menggunakan transaksi ini
	qtx := store.WithTx(tx)

	// 3. Lakukan operasi basis data menggunakan qtx
	// Misalkan ada kueri UpdateAccountBalance :one
	// Kurangi saldo dari akun sumber
	sourceAccount, err := qtx.UpdateAccountBalance(ctx, db.UpdateAccountBalanceParams{
		Amount: -amount, // Kurangi
		ID:     fromAccountID,
	})
	if err != nil {
		// Rollback sudah di-defer, jadi kita bisa langsung return error
		return fmt.Errorf("gagal update saldo akun sumber: %w", err)
	}
	if sourceAccount.Balance < 0 { // Misalkan Balance adalah field di struct Account
	    return fmt.Errorf("saldo akun sumber tidak mencukupi") // Rollback akan terjadi
	}


	// Tambah saldo ke akun tujuan
	_, err = qtx.UpdateAccountBalance(ctx, db.UpdateAccountBalanceParams{
		Amount: amount, // Tambah
		ID:     toAccountID,
	})
	if err != nil {
		return fmt.Errorf("gagal update saldo akun tujuan: %w", err)
	}

	// ... operasi lain dalam transaksi ...

	// 4. Jika semua operasi berhasil, commit transaksi
	if err := tx.Commit(); err != nil {
		return fmt.Errorf("gagal commit transaksi: %w", err)
	}

	return nil // Transaksi berhasil
}
```

**Poin Kunci Transaksi:**
1.  **`BeginTx`**: Memulai transaksi dari `*sql.DB`.
2.  **`defer tx.Rollback()`**: Praktik penting. Jika `tx.Commit()` berhasil, panggilan `tx.Rollback()` berikutnya tidak akan berpengaruh. Jika terjadi `panic` atau `return` error sebelum `Commit`, `Rollback` akan memastikan transaksi dibatalkan.
3.  **`store.WithTx(tx)`**: Mendapatkan instance `*Queries` baru yang semua operasinya akan dijalankan dalam konteks `tx`. Jika `emit_prepared_queries: true`, `sqlc` menangani penggunaan kembali atau pembuatan ulang _prepared statements_ untuk transaksi.
4.  **`tx.Commit()`**: Jika semua operasi dalam transaksi berhasil, panggil `Commit` untuk menyimpan perubahan.

#### Pola Transaksi Umum (`sql_package: "pgx/v5"` dengan `pgxpool`)

```go
// pool adalah *pgxpool.Pool
// storePgx adalah *db.Queries yang diinisialisasi dengan pool

func transferMoneyPgx(ctx context.Context, pool *pgxpool.Pool, storePgx *db.Queries, fromAccountID, toAccountID int64, amount int64) error {
	// 1. Mulai transaksi dari pool
	tx, err := pool.Begin(ctx) // pgx.TxOptions bisa diberikan di sini
	if err != nil {
		return fmt.Errorf("gagal memulai transaksi pgx: %w", err)
	}
	defer tx.Rollback(ctx) // Rollback juga butuh context

	// 2. Buat Querier baru dengan transaksi pgx
	qtx := storePgx.WithTx(tx) // WithTx akan menerima pgx.Tx

	// 3. Lakukan operasi basis data menggunakan qtx (sama seperti contoh database/sql)
	// ... (operasi menggunakan qtx.UpdateAccountBalance, dll.) ...
    // Pastikan error ditangani dan di-return agar defer tx.Rollback(ctx) berjalan jika perlu.
    // Contoh:
    // _, err = qtx.SomeOperation(ctx, ...)
    // if err != nil {
    //     return fmt.Errorf("SomeOperation gagal: %w", err)
    // }


	// 4. Jika semua berhasil, commit
	if err := tx.Commit(ctx); err != nil { // Commit juga butuh context
		return fmt.Errorf("gagal commit transaksi pgx: %w", err)
	}
	return nil
}
```
Konsepnya sangat mirip, hanya API `pgx` yang sedikit berbeda (misalnya, `Commit` dan `Rollback` memerlukan `context`). `store.WithTx()` akan secara otomatis bekerja dengan tipe transaksi yang benar (`*sql.Tx` atau `pgx.Tx`) tergantung pada `sql_package` yang dikonfigurasi.

### Contoh Aplikasi Lengkap Sederhana (Revisi Tutorial Cepat)

Mari kita revisi `main.go` dari tutorial cepat dengan pemahaman yang lebih baik:

```go
// main.go
package main

import (
	"context"
	"database/sql"
	"errors"
	"fmt"
	"log"
	"os"
	"time"

	// Impor paket yang dihasilkan sqlc
	"github.com/yourusername/sqlc-tutorial/db/sqlc" // Ganti dengan path modul Anda

	_ "github.com/lib/pq" // Driver PostgreSQL
)

// Fungsi helper untuk transaksi, agar kode utama lebih bersih
func executeInTransaction(ctx context.Context, dbConn *sql.DB, fn func(queries *tutorial.Queries) error) error {
	tx, err := dbConn.BeginTx(ctx, nil)
	if err != nil {
		return fmt.Errorf("begin tx failed: %w", err)
	}
	defer tx.Rollback() // Rollback jika terjadi error atau fn panic

	q := tutorial.New(tx) // Gunakan New(tx) karena emit_prepared_queries mungkin false, jika true, pakai WithTx

	err = fn(q) // Jalankan operasi dalam fungsi anonim
	if err != nil {
		return fmt.Errorf("transaction function failed: %w", err)
	}

	return tx.Commit() // Commit jika tidak ada error
}


func main() {
	dbSource := os.Getenv("DB_SOURCE")
	if dbSource == "" {
		dbSource = "postgresql://user:password@localhost:5432/mydatabase?sslmode=disable"
		log.Printf("Warning: DB_SOURCE environment variable not set, using default DSN")
	}

	conn, err := sql.Open("postgres", dbSource)
	if err != nil {
		log.Fatalf("Cannot connect to db: %v", err)
	}
	defer conn.Close()

	conn.SetMaxOpenConns(10)
	conn.SetMaxIdleConns(5)
	conn.SetConnMaxLifetime(5 * time.Minute)

	if err := conn.Ping(); err != nil {
		log.Fatalf("Cannot ping db: %v", err)
	}
	log.Println("Successfully connected to the database!")

	// Instance Queries global menggunakan koneksi pool (conn)
	// Jika emit_prepared_queries: true, New(conn) akan mengembalikan (Queries, error)
	// Untuk kesederhanaan, kita asumsikan emit_prepared_queries: false atau error ditangani
	store := tutorial.New(conn)
	ctx := context.Background()

	// Contoh operasi dalam transaksi
	err = executeInTransaction(ctx, conn, func(qtx *tutorial.Queries) error {
		log.Println("--- Memulai Operasi Transaksional ---")
		// 1. Buat penulis baru
		authorBio := "A renowned philosopher and poet."
		newAuthor, err := qtx.CreateAuthor(ctx, tutorial.CreateAuthorParams{
			Name: "Plato",
			Bio:  sql.NullString{String: authorBio, Valid: authorBio != ""},
		})
		if err != nil {
			// Cek error spesifik jika perlu, misal duplikasi
			var pqErr *pq.Error
            if errors.As(err, &pqErr) && pqErr.Code == "23505" {
                 return fmt.Errorf("penulis dengan nama %s sudah ada: %w", "Plato", err)
            }
			return fmt.Errorf("failed to create author: %w", err)
		}
		log.Printf("Penulis baru: %+v\n", newAuthor)

		// 2. Buat buku untuk penulis
		_, err = qtx.CreateBook(ctx, tutorial.CreateBookParams{
			AuthorID:      newAuthor.ID,
			Title:         "The Republic",
			PublishedYear: -380, // Tahun 380 SM
		})
		if err != nil {
			return fmt.Errorf("failed to create book 'The Republic': %w", err)
		}
		log.Println("Buku 'The Republic' berhasil dibuat.")

		// Jika ada kesalahan di sini, misalnya:
		// return errors.New("sesuatu yang salah terjadi di tengah transaksi")
		// Maka defer tx.Rollback() akan dijalankan.

		log.Println("--- Operasi Transaksional Selesai ---")
		return nil // Jika ini dikembalikan, tx.Commit() akan dijalankan
	})

	if err != nil {
		log.Fatalf("Error dalam transaksi: %v", err)
	}
	log.Println("Transaksi berhasil.")


	// Contoh operasi di luar transaksi (read-only)
	log.Println("--- Membaca Data ---")
	allAuthors, err := store.ListAuthors(ctx)
	if err != nil {
		log.Fatalf("Gagal mendaftar penulis: %v", err)
	}
	if len(allAuthors) == 0 {
		log.Println("Tidak ada penulis ditemukan.")
	}
	for _, author := range allAuthors {
		log.Printf("Penulis: ID=%d, Name=%s", author.ID, author.Name)
		books, err := store.ListBooksByAuthor(ctx, author.ID)
		if err != nil {
			log.Printf("  Gagal mengambil buku untuk penulis ID %d: %v", author.ID, err)
			continue
		}
		if len(books) == 0 {
			log.Println("  Tidak ada buku oleh penulis ini.")
		}
		for _, book := range books {
			log.Printf("  - Buku: %s (%d)", book.Title, book.PublishedYear)
		}
	}
	log.Println("--- Pembacaan Data Selesai ---")
    log.Println("Aplikasi selesai.")
}
```
Pastikan Anda telah menjalankan `go mod tidy` atau `go get` untuk semua dependensi, termasuk driver basis data dan paket `sqlc` Anda. Dan jangan lupa untuk menjalankan `sqlc generate` setelah setiap perubahan pada file `.sql` atau `sqlc.yaml`.

Menggunakan kode yang dihasilkan `sqlc` pada dasarnya adalah tentang:
1.  Menyiapkan koneksi DB.
2.  Menginstansiasi `*Queries`.
3.  Memanggil metode kueri dengan `context` dan parameter yang benar.
4.  Menangani hasil dan error, termasuk `sql.ErrNoRows` dan error spesifik basis data.
5.  Menggunakan transaksi untuk operasi atomik.

Dengan praktik ini, Anda dapat membangun lapisan akses data yang kuat, aman secara tipe, dan efisien dengan `sqlc`.

---

## 10. Fitur Lanjutan sqlc

`sqlc` menawarkan berbagai fitur lanjutan untuk menangani kasus penggunaan yang lebih kompleks dan untuk memberikan kontrol lebih besar atas kode yang dihasilkan.

### Nullable Types (Tipe Data Null)

Kolom basis data yang dapat berisi nilai `NULL` memerlukan penanganan khusus di Go.
*   **`sql_package: "database/sql"` (default)**:
    *   `sqlc` akan menggunakan tipe dari paket `database/sql` seperti `sql.NullString`, `sql.NullBool`, `sql.NullInt32`, `sql.NullInt64`, `sql.NullFloat64`, `sql.NullTime`.
    *   Struct ini memiliki dua field: satu untuk nilai (misalnya, `String` untuk `sql.NullString`) dan satu field `Valid` (boolean) yang menunjukkan apakah nilainya `NULL`.
    ```go
    // Skema: bio TEXT -- bisa NULL
    // models.go:
    type Author struct {
        // ...
        Bio sql.NullString `json:"bio"`
    }

    // Penggunaan:
    if author.Bio.Valid {
        fmt.Println("Bio:", author.Bio.String)
    } else {
        fmt.Println("Bio: (tidak ada)")
    }

    // Saat memasukkan data:
    params := CreateAuthorParams{
        Name: "Anonymous",
        Bio:  sql.NullString{String: "", Valid: false}, // Untuk memasukkan NULL
    }
    // atau
    bioValue := "Seorang penulis misterius"
    params.Bio = sql.NullString{String: bioValue, Valid: true}
    ```

*   **`sql_package: "pgx/v5"` (atau `v4`)**:
    *   `sqlc` akan menggunakan tipe dari paket `github.com/jackc/pgx/v5/pgtype` (misalnya, `pgtype.Text`, `pgtype.Bool`, `pgtype.Int8`, `pgtype.Timestamptz`).
    *   Tipe `pgtype` ini memiliki field `Valid` yang serupa, dan juga metode untuk konversi. Mereka seringkali lebih ergonomis untuk digunakan dalam ekosistem `pgx`.
    ```go
    // Skema (PG): bio TEXT
    // models.go (dengan sql_package: "pgx/v5"):
    type Author struct {
        // ...
        Bio pgtype.Text `json:"bio"`
    }

    // Penggunaan:
    if author.Bio.Valid {
        fmt.Println("Bio:", author.Bio.String)
    } else {
        fmt.Println("Bio: (tidak ada)")
    }

    // Saat memasukkan data:
    var authorBio pgtype.Text
    err := authorBio.Scan("Seorang penulis pgx") // Scan bisa dari string, atau dari nil untuk NULL
    // if err != nil { ... }
    params := CreateAuthorParams{
        Name: "Pgx Author",
        Bio:  authorBio,
    }
    ```
    Atau, Anda bisa menggunakan `pgtype.Text{String: "...", Valid: true}`.

*   **Menggunakan Pointer dengan `overrides`**: Anda juga dapat meng-override tipe nullable menjadi tipe pointer Go (misalnya, `*string`, `*int64`, `*time.Time`).
    ```yaml
    # sqlc.yaml
    # ...
    gen:
      go:
        overrides:
          - db_type: "text" # Atau nama kolom spesifik: "authors.bio"
            nullable: true
            go_type: # Ini akan menghasilkan *string untuk kolom TEXT nullable
              type: "string" # Tipe dasar
              pointer: true # Menjadikannya pointer
    ```
    Maka di `models.go`:
    ```go
    type Author struct {
        // ...
        Bio *string `json:"bio"`
    }

    // Penggunaan:
    if author.Bio != nil {
        fmt.Println("Bio:", *author.Bio)
    } else {
        fmt.Println("Bio: (tidak ada)")
    }
    ```
    Ini bisa lebih bersih dalam beberapa kasus, tetapi pastikan Anda mengerti implikasinya (misalnya, dereferencing pointer `nil` akan panic).

### Tipe JSON dan JSONB (PostgreSQL)

Kolom `JSON` atau `JSONB` di PostgreSQL secara default dipetakan ke `[]byte` oleh `sqlc`.
```go
// Skema (PG): metadata JSONB
// models.go:
type User struct {
    // ...
    Metadata []byte `json:"metadata"`
}

// Penggunaan:
var userPrefs MyPreferencesStruct
if user.Metadata != nil {
    err := json.Unmarshal(user.Metadata, &userPrefs)
    // ...
}

// Saat menyimpan:
prefsData, err := json.Marshal(MyPreferencesStruct{Theme: "dark"})
// ...
params := CreateUserParams{
    // ...
    Metadata: prefsData,
}
```

**Override ke `json.RawMessage`**: Lebih nyaman karena `json.RawMessage` adalah alias untuk `[]byte` tetapi berperilaku lebih baik dengan paket `encoding/json`.
```yaml
# sqlc.yaml
overrides:
  - db_type: "jsonb" # atau "json"
    go_type: "encoding/json.RawMessage"
  # Atau untuk kolom spesifik
  # - column: "users.preferences"
  #   go_type: "encoding/json.RawMessage"
```
Maka `models.go`: `Metadata json.RawMessage`.

**Override ke Struct Kustom**: Jika Anda selalu mem-parsing JSON ke struct Go tertentu.
```yaml
# sqlc.yaml
overrides:
  - column: "users.preferences" # Lebih baik spesifik per kolom
    db_type: "jsonb" # Pastikan ini cocok dengan tipe kolom di DB
    go_type: # Tipe struct kustom Anda
      import: "github.com/yourorg/yourproject/internal/types"
      type: "UserPreferences"
    # Anda mungkin perlu implementasi Scanner/Valuer di UserPreferences jika tidak sederhana
    # Atau jika UserPreferences adalah struct dan bisa di-marshal/unmarshal JSON
    # maka sqlc dan driver akan sering menanganinya jika go_type adalah struct.
    # Jika tipe kustom Anda adalah pointer ke struct, Anda perlu nullable: true
    # nullable: true # Jika UserPreferences adalah *types.UserPreferences
```
Struct `UserPreferences` Anda harus bisa di-marshal/unmarshal dari/ke JSON. Jika `UserPreferences` sendiri adalah struct (bukan pointer), dan kolom DB-nya `NOT NULL`, ini akan berfungsi. Jika kolom DB bisa `NULL`, Anda mungkin perlu `*types.UserPreferences` atau `types.UserPreferences` yang mengimplementasikan `sql.Scanner` dan `driver.Valuer`.

```go
// internal/types/preferences.go
package types

import (
	"database/sql/driver"
	"encoding/json"
	"fmt"
)

type UserPreferences struct {
	Theme string `json:"theme"`
	Lang  string `json:"lang"`
	Notifications bool `json:"notifications_enabled"`
}

// Untuk memungkinkan sqlc menggunakan UserPreferences secara langsung untuk JSON/JSONB
// Implementasi sql.Scanner
func (p *UserPreferences) Scan(src interface{}) error {
	if src == nil {
		// Biarkan p sebagai nilai zero default jika kolom DB adalah NULL
		// atau Anda bisa memutuskan untuk error jika tidak boleh NULL
		// *p = UserPreferences{} // atau biarkan apa adanya
		return nil
	}
	source, ok := src.([]byte)
	if !ok {
		return fmt.Errorf("tipe tidak didukung untuk UserPreferences.Scan: %T", src)
	}
	if len(source) == 0 { // Atau jika string "null"
		// *p = UserPreferences{}
		return nil
	}
	return json.Unmarshal(source, p)
}

// Implementasi driver.Valuer
func (p UserPreferences) Value() (driver.Value, error) {
	// Jika UserPreferences adalah zero value, Anda mungkin ingin menyimpan NULL atau JSON '{}'
	// if p.Theme == "" && p.Lang == "" && !p.Notifications {
	//  return nil, nil // Menyimpan NULL jika struct kosong
	// }
	return json.Marshal(p)
}
```
Dengan `Scanner` dan `Valuer`, `sqlc` dan driver basis data dapat dengan mulus mengkonversi antara `JSONB` dan `UserPreferences`.

### Array Types (Tipe Data Array - PostgreSQL)

PostgreSQL mendukung tipe array (misalnya, `TEXT[]`, `INTEGER[]`).
*   **`sql_package: "database/sql"`**:
    *   Biasanya menggunakan `github.com/lib/pq.Array` (untuk `lib/pq`) atau tipe serupa dari driver lain.
    *   Contoh: `Tags TEXT[]` -> `pq.StringArray`.
    ```go
    // Skema (PG): tags TEXT[]
    // models.go:
    type Post struct {
        // ...
        Tags pq.StringArray `json:"tags"` // pq.StringArray, pq.Int64Array, dll.
    }

    // Penggunaan:
    var postTags []string = post.Tags // pq.StringArray bisa di-assign ke []string

    // Saat menyimpan:
    params := CreatePostParams{
        // ...
        Tags: []string{"go", "sqlc", "postgres"}, // Akan dikonversi ke pq.StringArray oleh driver
    }
    ```
*   **`sql_package: "pgx/v5"`**:
    *   `pgx` menangani array secara lebih native. `TEXT[]` akan menjadi `[]string`, `INTEGER[]` menjadi `[]int32`, `BIGINT[]` menjadi `[]int64`.
    *   Jika array bisa `NULL` di DB, maka slice Go bisa `nil`. Jika elemen array bisa `NULL` (misalnya, `_TEXT` yang elemennya bisa null), `pgx` akan menggunakan slice dari pointer atau tipe `pgtype` yang sesuai. `sqlc` biasanya akan memetakannya ke slice tipe dasar (misalnya `[]string`), dengan asumsi elemen array tidak `NULL`.
    ```go
    // Skema (PG): tags TEXT[]
    // models.go (dengan sql_package: "pgx/v5"):
    type Post struct {
        // ...
        Tags []string `json:"tags"`
    }
    ```

### Enum Types (PostgreSQL dan MySQL)

*   **PostgreSQL (`CREATE TYPE ... AS ENUM`)**:
    *   Secara default, `sqlc` akan membuat tipe string Go kustom untuk enum tersebut dan konstanta untuk setiap nilai enum. Tipe ini juga akan memiliki metode `Scan` dan `Value` agar kompatibel dengan `database/sql`.
    ```sql
    -- Skema (PG):
    CREATE TYPE mood AS ENUM ('sad', 'ok', 'happy');
    CREATE TABLE daily_entries (
        entry_date DATE PRIMARY KEY,
        current_mood mood NOT NULL
    );
    ```
    ```go
    // models.go:
    type Mood string

    const (
        MoodSad   Mood = "sad"
        MoodOk    Mood = "ok"
        MoodHappy Mood = "happy"
    )

    func (e *Mood) Scan(src interface{}) error { /* ... implementasi ... */ }
    func (e Mood) Value() (driver.Value, error) { /* ... implementasi ... */ }

    type DailyEntry struct {
        EntryDate    time.Time `json:"entry_date"`
        CurrentMood  Mood      `json:"current_mood"`
    }
    ```
    *   Anda bisa meng-override ini ke `string` sederhana jika tidak ingin tipe kustom:
    ```yaml
    overrides:
      - db_type: "mood" # Nama tipe enum PostgreSQL
        go_type: "string"
    ```

*   **MySQL (`ENUM('val1', 'val2')`)**:
    *   Secara default, `sqlc` akan memetakannya ke `string` di Go.
    *   Anda bisa membuat tipe Go kustom dan meng-override seperti pada PostgreSQL jika ingin perilaku serupa (tipe Go kustom + konstanta). Anda perlu menyediakan implementasi `Scan`/`Value`.
    ```sql
    -- Skema (MySQL):
    -- status ENUM('active', 'inactive', 'pending') NOT NULL
    ```
    ```go
    // models.go:
    type User struct {
        // ...
        Status string `json:"status"` // Dipetakan ke string
    }
    ```
    Jika Anda ingin tipe kustom `UserStatus` di Go:
    ```yaml
    # sqlc.yaml
    overrides:
      - column: "users.status" # Atau db_type jika Anda tahu nama internalnya
        go_type:
          import: "github.com/yourorg/yourproject/internal/enums"
          type: "UserStatus" # Tipe enum kustom Anda
    ```
    Dan Anda perlu mendefinisikan `UserStatus` dengan `Scan`/`Value` di paket `enums`.

### UUID Types

*   **PostgreSQL (`UUID`)**:
    *   `sqlc` versi baru (sekitar v1.15.0+) defaultnya memetakan `UUID` ke `github.com/google/uuid.UUID`.
    *   Versi lama atau jika ada override mungkin memetakan ke `[16]byte` atau string.
    *   Jika `sql_package: "pgx/v5"`, ia akan menggunakan `pgtype.UUID`.
    ```go
    // Skema (PG): id UUID PRIMARY KEY
    // models.go (default baru atau dengan override):
    import "github.com/google/uuid"
    type Item struct {
        ID uuid.UUID `json:"id"`
    }
    // Untuk kolom UUID nullable:
    // NullableID uuid.NullUUID `json:"nullable_id"` (dari paket google/uuid)
    // atau jika sql_package:"pgx/v5", maka pgtype.UUID yang menangani null
    ```
    Jika Anda lebih suka string atau `[16]byte`, Anda bisa menggunakan `overrides`.
    ```yaml
    overrides:
      - db_type: "uuid"
        go_type: "string" # Atau "[16]byte"
    ```
*   **MySQL**: Tidak ada tipe UUID native. Biasanya disimpan sebagai `BINARY(16)` atau `VARCHAR(36)`.
    *   `BINARY(16)`: `sqlc` akan memetakannya ke `[]byte`. Anda perlu override ke `uuid.UUID` (dan `uuid.UUID` mengimplementasikan `Scanner`/`Valuer` untuk `[]byte`).
        ```yaml
        overrides:
          - db_type: "binary" # Atau lebih spesifik jika ada tipe binary lain
            # column: "<table>.uuid_column"
            go_type: "github.com/google/uuid.UUID"
        ```
    *   `VARCHAR(36)`: `sqlc` akan memetakannya ke `string`. Override ke `uuid.UUID` (dan `uuid.UUID` juga mengimplementasikan `Scanner`/`Valuer` untuk `string`).
        ```yaml
        overrides:
          - db_type: "varchar" # Perlu lebih spesifik, misal column: "<table>.uuid_column"
            # column: "<table>.uuid_column"
            go_type: "github.com/google/uuid.UUID"
        ```
*   **SQLite**: Mirip MySQL, simpan sebagai `TEXT` (string) atau `BLOB` (`[]byte`). Gunakan `overrides` yang sesuai.

### Overrides Tipe Data (`overrides`)

Ini adalah fitur yang sangat kuat untuk menyesuaikan pemetaan tipe SQL ke Go. Dibahas sekilas sebelumnya, mari perdalam.

Lokasi: `sqlc.yaml` -> `sql` -> `[item]` -> `gen` -> `go` -> `overrides`.
Ini adalah array objek, di mana setiap objek mendefinisikan satu aturan override.

**Kapan dan Mengapa Menggunakannya:**
*   Menggunakan tipe data kustom dari pustaka pihak ketiga (misalnya, `decimal.Decimal`, `null.String` dari `guregu/null`).
*   Menggunakan tipe domain kustom Anda sendiri (misalnya, `EmailAddress string` daripada hanya `string`).
*   Memaksa tipe tertentu untuk kolom `JSON/JSONB` atau `NUMERIC`.
*   Mengubah tipe `NULLABLE` default (misalnya, dari `sql.NullString` ke `*string` atau `guregu/null.String`).
*   Memetakan tipe non-standar atau tipe yang `sqlc` tidak kenali dengan baik.

**Sintaks Objek Override:**
```yaml
overrides:
  - # Opsi 1: Berdasarkan tipe basis data global
    db_type: "pg_catalog.numeric" # Nama tipe PostgreSQL (gunakan pg_catalog untuk tipe bawaan)
    go_type: "github.com/shopspring/decimal.Decimal"
    # nullable: true # Jika decimal.Decimal Anda adalah pointer atau implementasi khusus untuk null

  - # Opsi 2: Berdasarkan nama kolom spesifik (lebih aman)
    column: "products.price" # Format: <table>.<column> atau <table.view>.<column>
    # db_type: "numeric" # Opsional, sebagai sanity check
    go_type: "github.com/shopspring/decimal.Decimal"
    # nullable: true # Jika kolom 'price' bisa NULL dan decimal.Decimal Anda menangani ini (misal, *decimal.Decimal)

  - # Opsi 3: Menggunakan tipe Go yang sudah ada di paket output atau tipe dasar
    db_type: "text"
    nullable: true # Hanya berlaku untuk kolom TEXT yang memang nullable
    go_type:
      type: "string" # Tipe Go dasar
      pointer: true  # Menjadikannya *string

  - # Opsi 4: Menggunakan tipe Go kustom dari paket lain
    db_type: "user_status_enum" # Misal, tipe ENUM kustom Anda di PostgreSQL
    go_type:
      import: "github.com/myorg/myapp/pkg/customtypes"
      type: "UserStatus" # Nama tipe di paket tersebut
      # nullable: true # Jika UserStatus adalah pointer atau custom nullable type

  - # Opsi 5: Menambahkan tag struct Go kustom
    column: "users.full_name"
    go_struct_tag: 'validate:"required" custom:"tag"' # Akan ditambahkan ke tag field struct

  - # Opsi 6: Jika go_type adalah tipe yang Anda definisikan di paket yang sama dengan output sqlc.
    # dan ingin sqlc menganggapnya sebagai tipe bernama, bukan alias.
    # db_type: "special_code"
    # go_type:
    #   type_name: "MySpecialCode" # Definisikan type MySpecialCode string di tempat lain
```

**Penting tentang `nullable` dalam `go_type` object:**
*   Jika `go_type` adalah string (misalnya, `"time.Time"`), maka `nullable` di level atas override (`- db_type: ..., nullable: true, go_type: "time.Time"`) akan membuat `sqlc` mencari tipe `NullTime` yang sesuai atau menggunakan `*time.Time` jika `go_type.pointer` juga ada.
*   Jika `go_type` adalah objek:
    ```yaml
    go_type:
      type: "MyType"
      import: "..."
      # nullable: true # Jika MyType adalah tipe yang sudah nullable (pointer, interface, atau mengimplementasikan Scanner/Valuer yang benar)
      # ATAU
      # pointer: true # Untuk membuat *MyType
    ```
    Opsi `nullable` di sini memberitahu `sqlc` bahwa `MyType` itu sendiri sudah menangani nullability (misalnya, `*MyType`, `sql.NullString`, `pgtype.Text`). Jika `MyType` adalah struct biasa dan Anda set `nullable: true` di sini, `sqlc` mungkin akan tetap menghasilkan tipe pointer atau `sql.NullX` jika tidak ada `pointer: true` yang eksplisit atau jika `MyType` tidak terlihat seperti tipe nullable.

**Contoh: `pgtype` dari `jackc/pgx`**
Jika Anda tidak menggunakan `sql_package: "pgx/vN"` tetapi masih ingin menggunakan beberapa tipe `pgtype` untuk kolom tertentu:
```yaml
# sqlc.yaml
# ... sql_package: "database/sql" ...
    overrides:
      - db_type: "pg_catalog.timestamptz"
        nullable: true # Penting karena pgtype.Timestamptz menangani null
        go_type: "github.com/jackc/pgx/v5/pgtype.Timestamptz"
      - db_type: "pg_catalog.text"
        nullable: true
        go_type: "github.com/jackc/pgx/v5/pgtype.Text"
```
Tipe `pgtype` ini mengimplementasikan `sql.Scanner` dan `driver.Valuer`, sehingga kompatibel dengan `database/sql`.

**Contoh: Tipe Kustom untuk Data Domain Spesifik**
Misalkan Anda punya kolom `email TEXT` dan ingin tipe `EmailString` di Go.
```go
// myapp/types/email.go
package types

import (
    "database/sql/driver"
    "errors"
    "strings"
)

type EmailString string

func (e *EmailString) Scan(src interface{}) error {
    if src == nil {
        *e = "" // Atau error jika email tidak boleh null
        return nil
    }
    s, ok := src.(string)
    if !ok {
        // Bisa juga src.([]byte) jika driver mengembalikan byte slice
        b, okByte := src.([]byte)
        if !okByte {
            return errors.New("tipe tidak kompatibel untuk EmailString")
        }
        s = string(b)
    }
    // Validasi sederhana (bisa lebih kompleks)
    if !strings.Contains(s, "@") {
        return errors.New("format email tidak valid")
    }
    *e = EmailString(s)
    return nil
}

func (e EmailString) Value() (driver.Value, error) {
    if string(e) == "" { // Mungkin ingin menyimpan NULL jika string kosong
        return nil, nil
    }
    // Validasi bisa juga di sini
    return string(e), nil
}
```
```yaml
# sqlc.yaml
# ...
    overrides:
      - column: "users.email" # Terapkan hanya pada kolom email di tabel users
        go_type:
          import: "github.com/myorg/myapp/types"
          type: "EmailString"
        # nullable: true # Jika EmailString adalah pointer atau menangani null dengan baik (Scan bisa menerima nil)
```

### Mengganti Nama Struct dan Field (`rename`)

Fitur `rename` memungkinkan Anda mengganti nama default yang akan digunakan `sqlc` untuk struct dan field Go.
Lokasi: `sqlc.yaml` -> `sql` -> `[item]` -> `gen` -> `go` -> `rename`.
Ini adalah objek map `string` ke `string`.

```yaml
rename:
  # Mengganti nama tabel 'app_configurations' menjadi struct 'AppConfig'
  app_configurations: "AppConfig"

  # Mengganti nama kolom 'usr_api_token' di tabel 'users' menjadi field 'APIToken' di struct 'User'
  # (Jika 'users' sendiri tidak di-rename, sqlc akan menggunakan 'User' atau 'Users' tergantung emit_exact_table_names)
  users.usr_api_token: "APIToken"

  # Mengganti nama kolom 'created_at_ts' secara global (di semua struct) menjadi 'CreatedAt'
  # Hati-hati dengan rename global, bisa ada konflik. Lebih aman per tabel.
  # created_at_ts: "CreatedAt"
```
`sqlc` sudah memiliki logika _inflection_ yang cukup baik (misalnya, `user_accounts` -> `UserAccount`), jadi `rename` biasanya diperlukan untuk kasus-kasus khusus atau preferensi gaya yang kuat.

### Query dengan `IN` Clause dan `sqlc.slice`

Telah disinggung sebelumnya, `sqlc.slice()` digunakan untuk parameter yang merupakan slice Go, biasanya untuk klausa `IN` SQL atau `ANY` (PostgreSQL).
```sql
-- name: GetUsersByIDs :many
-- Untuk PostgreSQL:
SELECT * FROM users WHERE id = ANY(sqlc.slice(user_ids));
-- Untuk MySQL/SQLite:
-- SELECT * FROM users WHERE id IN (sqlc.slice(user_ids));
-- Perhatian: Dukungan IN (sqlc.slice(...)) untuk MySQL/SQLite bergantung pada driver.
```
Fungsi Go yang dihasilkan akan menerima `user_ids []int64` (atau tipe slice yang sesuai).
*   Untuk PostgreSQL, `lib/pq` dan `pgx` menangani `pq.Array` atau ekspansi `ANY($1)` dengan baik.
*   Untuk MySQL/SQLite, jika driver tidak mendukung ekspansi `?` menjadi `?,?,?` untuk slice, ini tidak akan berfungsi. Anda mungkin perlu alternatif:
    1.  Alat _query builder_ untuk bagian dinamis ini.
    2.  Jika menggunakan `sqlx` (bukan `sqlc`), ia memiliki `sqlx.In` yang melakukan ekspansi kueri.
    3.  Untuk MySQL, `FIND_IN_SET(id, ?)` dengan string ID yang dipisahkan koma (tidak ideal).

### Perintah `:copyfrom` (PostgreSQL)

Untuk _bulk insert_ data dalam jumlah besar ke PostgreSQL, perintah `COPY FROM STDIN` jauh lebih efisien daripada banyak `INSERT` individual. `sqlc` mendukung ini melalui jenis kueri `:copyfrom`.

```sql
-- name: BulkInsertAuthors :copyfrom
COPY authors (name, bio) FROM STDIN;
```
`sqlc` akan menghasilkan:
1.  Sebuah struct `BulkInsertAuthorsParams` (atau mirip) di `models.go` yang field-nya cocok dengan kolom di `COPY` (yaitu, `Name string`, `Bio sql.NullString`).
2.  Sebuah metode `BulkInsertAuthors(ctx context.Context, arg []BulkInsertAuthorsParams) (BulkInsertAuthorsResults, error)` pada `*Queries`.
    `BulkInsertAuthorsResults` akan berisi `RowsAffected()`.

File `copyfrom.go` (atau nama kustom) akan berisi implementasi helper yang menggunakan API `pgx.CopyFrom` (jika `sql_package: "pgx/vN"`) atau `pq.CopyIn` (jika `sql_package: "database/sql"` dengan `lib/pq`).

**Penggunaan:**
```go
var authorsToInsert []tutorial.BulkInsertAuthorsParams // tutorial adalah nama paket Anda
authorsToInsert = append(authorsToInsert, tutorial.BulkInsertAuthorsParams{
	Name: "J.K. Rowling",
	Bio:  sql.NullString{String: "Author of Harry Potter", Valid: true},
})
authorsToInsert = append(authorsToInsert, tutorial.BulkInsertAuthorsParams{
	Name: "George R.R. Martin",
	Bio:  sql.NullString{String: "Author of A Song of Ice and Fire", Valid: true},
})
// ... tambahkan lebih banyak penulis

results, err := store.BulkInsertAuthors(ctx, authorsToInsert)
if err != nil {
    log.Fatalf("Gagal bulk insert: %v", err)
}
log.Printf("%d baris berhasil dimasukkan via COPY.", results.RowsAffected())
```
Ini sangat efisien untuk impor data besar.

### Menggunakan `sqlc.embed()`

Terkadang Anda memiliki kueri `JOIN` dan ingin hasil dari tabel yang di-join disematkan sebagai struct di dalam struct hasil utama.
```sql
-- Misal ada tabel 'authors' dan 'books' dengan relasi one-to-many
-- (satu penulis bisa punya banyak buku, tapi kueri ini mungkin mengambil satu buku dan penulisnya)

-- name: GetBookWithAuthor :one
SELECT
    b.id as book_id,
    b.title,
    b.published_year,
    -- Kolom untuk author disematkan
    sqlc.embed(authors) -- Mengacu pada tabel 'authors'
FROM books b
JOIN authors ON authors.id = b.author_id
WHERE b.id = $1;
```
`sqlc` akan menghasilkan struct `GetBookWithAuthorRow` seperti:
```go
type GetBookWithAuthorRow struct {
    BookID         int64
    Title          string
    PublishedYear  int32
    Author         Author // Struct Author disematkan
}
```
Ini memerlukan `SELECT *` implisit untuk tabel yang di-embed atau semua kolomnya harus ada di `SELECT` list dengan nama yang benar. Jika ada konflik nama kolom antara tabel utama dan tabel yang di-embed, Anda perlu memberi alias pada kolom. `sqlc` akan mencoba memetakan kolom `authors.*` ke field dari `Author`.

Jika Anda tidak memilih semua kolom dari `authors`, `sqlc.embed()` mungkin tidak berfungsi seperti yang diharapkan, atau `Author` yang disematkan hanya akan memiliki field yang dipilih.

### Fungsi `left_join` (Eksperimental/Sintaks Alternatif)

`sqlc` memiliki dukungan untuk `LEFT JOIN` di mana tabel sisi kanan mungkin tidak ada, yang menghasilkan field _nullable_ atau struct pointer.
Biasanya, `sqlc` menangani ini dengan membuat field dari tabel yang di-join menjadi _nullable_ (misalnya, `sql.NullString` untuk `authors.name` jika `authors` di-`LEFT JOIN` dan mungkin tidak ada).

```sql
-- name: GetPostWithOptionalAuthor :one
SELECT
    p.id as post_id,
    p.title,
    -- Kolom dari author, bisa NULL jika LEFT JOIN tidak menemukan match
    a.id as author_id,
    a.name as author_name
FROM posts p
LEFT JOIN authors a ON p.author_id = a.id
WHERE p.id = $1;
```
Ini akan menghasilkan `GetPostWithOptionalAuthorRow` di mana `AuthorID` dan `AuthorName` akan menjadi tipe _nullable_ (misalnya, `sql.NullInt64`, `sql.NullString`).

Jika Anda menggunakan `sqlc.embed()` dengan `LEFT JOIN`:
```sql
-- name: GetPostWithOptionalEmbeddedAuthor :one
SELECT
    p.id as post_id,
    p.title,
    sqlc.embed(a) -- Alias 'a' untuk tabel authors
FROM posts p
LEFT JOIN authors a ON p.author_id = a.id
WHERE p.id = $1;
```
`sqlc` mungkin menghasilkan `Author *Author` (pointer ke struct `Author`) atau struct `Author` di mana semua field-nya adalah tipe _nullable_. Perilaku ini telah berkembang, jadi uji dengan versi `sqlc` Anda. Idealnya, ia menghasilkan `Author *Author` agar Anda bisa memeriksa `if row.Author != nil`.

Opsi konfigurasi `emit_result_struct_pointers` juga dapat memengaruhi ini. Jika `true`, semua struct hasil `:one` adalah pointer, yang bisa membantu membedakan "tidak ada baris" dari "baris ada, tapi struct yang di-embed adalah NULL".

### Menggunakan `db:*` untuk memilih semua kolom dari tabel dalam JOIN

Alih-alih `SELECT t1.*, t2.*`, Anda bisa menggunakan `SELECT db:t1, db:t2` dalam beberapa konteks untuk `sqlc` agar lebih eksplisit tentang asal kolom, terutama saat menggunakan `sqlc.embed`.
```sql
-- name: GetAuthorAndBooks :many
-- Tidak benar-benar query yang bagus, hanya untuk demo sintaks
-- (akan menghasilkan duplikasi data author jika author punya banyak buku)
SELECT
    sqlc.embed(authors), -- Semua kolom dari authors
    sqlc.embed(books)    -- Semua kolom dari books
FROM authors
JOIN books ON authors.id = books.author_id
WHERE authors.id = $1;
```
`sqlc` akan mencoba menghasilkan struct `GetAuthorAndBooksRow` yang menyematkan `Author` dan `Book`.
Ini adalah fitur yang lebih baru dan sintaksnya mungkin masih berkembang. Rujuk dokumentasi `sqlc` terbaru. Lebih umum dan stabil adalah `SELECT authors.*, books.*` lalu `sqlc.embed(authors)` dan `sqlc.embed(books)`.

### Global Overrides vs Package Overrides

Anda dapat mendefinisikan `overrides` di level global `sqlc.yaml` (jarang, dan mungkin tidak didukung di v2) atau, yang lebih umum, di dalam setiap definisi paket `sql:`:
```yaml
version: "2"
sql:
  - engine: "postgresql"
    # ...
    gen:
      go:
        package: "pgdb"
        out: "db/pg"
        overrides: # Override khusus untuk paket pgdb
          - db_type: "uuid"
            go_type: "github.com/gofrs/uuid.UUID"
    # ...
  - engine: "mysql"
    # ...
    gen:
      go:
        package: "mysqldb"
        out: "db/mysql"
        overrides: # Override khusus untuk paket mysqldb
          - db_type: "binary" # Asumsi BINARY(16) untuk UUID di MySQL
            go_type: "github.com/google/uuid.UUID"
    # ...
```
Ini memungkinkan Anda memiliki pemetaan tipe yang berbeda untuk paket yang berbeda, yang sangat berguna jika Anda bekerja dengan beberapa jenis basis data atau beberapa skema yang memiliki tipe dengan nama sama tetapi makna berbeda.

Fitur-fitur canggih ini memberikan fleksibilitas besar. Namun, gunakan dengan bijak. `overrides` dan `rename` yang berlebihan bisa membuat konfigurasi sulit dikelola. Selalu prioritaskan kejelasan dan pemeliharaan.

---

## 11. Basis Data yang Didukung

`sqlc` dirancang untuk bekerja dengan beberapa sistem manajemen basis data (DBMS) SQL populer. Dukungan untuk setiap engine bisa bervariasi dalam hal fitur spesifik dan cara `sqlc` menginterpretasikan skema dan kueri.

Saat ini, engine utama yang didukung adalah:
*   PostgreSQL
*   MySQL
*   SQLite

Dukungan untuk Microsoft SQL Server sedang dalam tahap beta.

### PostgreSQL

*   **Engine**: `postgresql`
*   **Fitur Unggulan yang Didukung**:
    *   Berbagai tipe data kaya (JSON/JSONB, UUID, ENUM, ARRAY, HSTORE, geometris, dll.). `sqlc` memiliki pemetaan default yang baik dan bisa dikustomisasi dengan `overrides`.
    *   `RETURNING` clause pada DML (`INSERT`, `UPDATE`, `DELETE`), yang sangat berguna untuk mendapatkan kembali data yang dimodifikasi.
    *   `COPY FROM STDIN` melalui kueri `:copyfrom` untuk _bulk insert_ yang sangat efisien.
    *   Common Table Expressions (CTEs), termasuk CTE rekursif.
    *   Window functions.
    *   Skema dan pencarian path (`search_path`).
    *   Operator dan fungsi spesifik PostgreSQL.
    *   Parsing DDL yang kuat: `sqlc` biasanya dapat mem-parsing `CREATE TABLE`, `CREATE TYPE`, dll., tanpa perlu koneksi ke basis data yang sedang berjalan.

*   **Driver Go Populer**:
    *   `github.com/lib/pq` (standar `database/sql`)
    *   `github.com/jackc/pgx/v5` (bisa sebagai driver `database/sql` via `stdlib` atau digunakan secara native dengan `sql_package: "pgx/v5"`)

*   **Catatan Khusus**:
    *   Untuk tipe bawaan PostgreSQL, seringkali lebih baik merujuknya dengan prefix `pg_catalog` dalam `overrides` `db_type` (misalnya, `pg_catalog.timestamptz`, `pg_catalog.numeric`) untuk menghindari ambiguitas.
    *   Saat menggunakan `sql_package: "pgx/v5"`, `sqlc` menghasilkan kode yang menggunakan tipe `pgtype` dari `pgx`, yang memiliki penanganan _nullability_ dan tipe data PostgreSQL yang lebih baik.

### MySQL

*   **Engine**: `mysql`
*   **Fitur Unggulan yang Didukung**:
    *   Tipe data standar MySQL (INT, VARCHAR, TEXT, DATE, DATETIME, JSON, ENUM, dll.).
    *   Sintaks DML standar. MySQL 8+ mendukung CTEs.
    *   `LAST_INSERT_ID()` dapat digunakan (meskipun `sqlc` tidak secara eksplisit menghasilkannya, Anda bisa menulis kueri yang menggunakannya).
    *   `ROW_COUNT()` untuk mendapatkan jumlah baris yang terpengaruh (ekuivalen dengan hasil dari `:execrows`).

*   **Driver Go Populer**:
    *   `github.com/go-sql-driver/mysql`

*   **Catatan Khusus**:
    *   **Parsing Skema**: `sqlc` mungkin memerlukan koneksi ke basis data MySQL yang sedang berjalan untuk dapat sepenuhnya menginspeksi skema. Ini karena parser DDL MySQL `sqlc` mungkin tidak selengkap parser PostgreSQL. Anda dapat menyediakan URI koneksi di `sqlc.yaml` di bawah `database: uri: "..."`. `sqlc` akan menggunakan koneksi ini hanya selama fase `generate` untuk `DESCRIBE <table>` atau kueri introspeksi serupa.
    *   **UUID**: MySQL tidak memiliki tipe UUID native. Gunakan `BINARY(16)` atau `VARCHAR(36)` dan override tipenya di `sqlc.yaml` jika Anda ingin menggunakan `github.com/google/uuid.UUID` di Go.
    *   **ENUM**: Tipe `ENUM` MySQL dipetakan ke `string` Go secara default.
    *   **Parameter**: Gunakan `?` sebagai placeholder parameter dalam kueri.
    *   **`RETURNING` Clause**: MySQL tidak mendukung `RETURNING` seperti PostgreSQL. Untuk mendapatkan ID setelah `INSERT`, Anda perlu melakukan kueri terpisah untuk `LAST_INSERT_ID()`, atau jika primary key bukan auto-increment (misalnya, UUID yang Anda generate di aplikasi), Anda sudah memilikinya.

### SQLite

*   **Engine**: `sqlite`
*   **Fitur Unggulan yang Didukung**:
    *   Sistem tipe dinamis SQLite (INTEGER, REAL, TEXT, BLOB, NUMERIC). `sqlc` melakukan yang terbaik untuk memetakannya ke tipe Go statis.
    *   Sintaks DML dan DDL standar SQLite. SQLite modern (3.35.0+) mendukung `RETURNING`.
    *   CTEs dan window functions (di versi SQLite yang lebih baru).
    *   Parsing DDL yang kuat: Mirip PostgreSQL, `sqlc` biasanya bisa mem-parsing skema SQLite tanpa koneksi DB aktif.

*   **Driver Go Populer**:
    *   `github.com/mattn/go-sqlite3`
    *   `modernc.org/sqlite`

*   **Catatan Khusus**:
    *   **Tipe Data**:
        *   `BOOLEAN`: Disimulasikan dengan `INTEGER` (0 atau 1). Override ke `bool` jika perlu.
        *   `DATETIME`: Disimpan sebagai `TEXT` (ISO8601), `REAL` (Julian day numbers), atau `INTEGER` (Unix timestamp). `sqlc` defaultnya mungkin ke `interface{}` atau `string`/`int64`. Override ke `time.Time` jika diperlukan, pastikan driver Anda menangani konversinya.
        *   `JSON`: Disimpan sebagai `TEXT`. Gunakan `overrides` untuk memetakan ke `json.RawMessage` atau struct kustom.
        *   `UUID`: Disimpan sebagai `TEXT` atau `BLOB`. Override ke `uuid.UUID`.
    *   **Parameter**: Mendukung `?`, `$N`, `:name`, `@name`. Disarankan menggunakan `?` atau `$N` untuk konsistensi dengan driver Go.
    *   **`RETURNING` Clause**: Jika versi SQLite Anda mendukungnya, `sqlc` juga akan mendukungnya. Periksa versi SQLite yang digunakan oleh driver Go Anda.
    *   **Foreign Keys**: Secara default mungkin tidak aktif di SQLite. Pastikan untuk mengaktifkannya (`PRAGMA foreign_keys = ON;`) saat membuka koneksi jika Anda mengandalkan _foreign key constraints_. `sqlc` tetap akan mengenali relasi dari DDL untuk analisis.

### Microsoft SQL Server (Dukungan Beta)

*   **Engine**: `mssql`
*   **Status**: Dukungan untuk MS SQL Server (T-SQL) masih dalam tahap beta atau pengembangan aktif. Fitur dan kestabilan mungkin belum setara dengan PostgreSQL, MySQL, atau SQLite.
*   **Driver Go Populer**:
    *   `github.com/denisenkom/go-mssqldb`
    *   `github.com/microsoft/go-mssqldb` (pengembangan lebih baru, mungkin menjadi standar)
*   **Catatan Khusus**:
    *   Periksa [dokumentasi resmi sqlc](https://docs.sqlc.dev/en/latest/tutorials/getting-started-sql-server.html) dan [isu GitHub](https://github.com/sqlc-dev/sqlc/issues) untuk status dukungan terbaru, batasan, dan cara penggunaan yang direkomendasikan.
