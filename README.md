# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

# Reflection Subscriber-1

## 1. Penggunaan RwLock<> vs Mutex<>

### Alasan Menggunakan RwLock<>
`RwLock<>` (Read-Write Lock) dipilih dalam kasus ini karena memiliki beberapa keunggulan spesifik:

1. **Konkurensi Baca Tinggi**
   - Memperbolehkan banyak pembaca mengakses data secara bersamaan
   - Ideal untuk skenario dengan lebih banyak operasi baca dibandingkan tulis
   - Meningkatkan performa dalam operasi yang didominasi oleh pembacaan data

2. **Kontrol Akses yang Lebih Detail**
   - Memisahkan antara kunci baca (`.read()`) dan kunci tulis (`.write()`)
   - Mencegah modifikasi data selama proses pembacaan berlangsung

### Mengapa Tidak Menggunakan Mutex<>
Mutex<> memiliki karakteristik berbeda:
- Hanya mengizinkan satu thread mengakses data pada satu waktu
- Membatasi konkurensi lebih ketat dibandingkan RwLock<>
- Kurang efisien untuk operasi dengan banyak pembacaan

## 2. Batasan Mutasi Variabel Statis di Rust

Rust membatasi mutasi variabel statis karena alasan keamanan:

1. **Keamanan Thread (Thread Safety)**
   - Mencegah data race dan kondisi balapan (race conditions)
   - Menjamin bahwa perubahan variabel statis tidak mengakibatkan perilaku tidak terduga

2. **Desain Bahasa yang Aman**
   -Rust menekankan keamanan memori dan pencegahan kesalahan konkuren
   - Variabel statis dianggap sebagai wilayah kritis yang memerlukan kontrol ketat

3. **Alternatif yang Disediakan**
   - Penggunaan `lazy_static!` dengan wrapper seperti `RwLock` atau `Mutex`
   - Memungkinkan mutasi terkontrol melalui mekanisme penguncian
   - Mendorong pengembang untuk berpikir eksplisit tentang konkurensi

### Perbedaan dengan Java
Di Java:
- Variabel statis dapat diubah langsung melalui fungsi statis
- Kurang ketat dalam kontrol konkurensi
- Lebih rentan terhadap kondisi balapan

Dalam Rust, pendekatan ini digantikan dengan mekanisme yang lebih aman dan eksplisit, seperti penggunaan `lazy_static!` dengan wrapper sinkronisasi.    


#### Reflection Subscriber-2

# Reflection Subscriber-2

## 1. Eksplorasi di Luar Langkah Tutorial: `src/lib.rs`

Dalam file `lib.rs`, terdapat beberapa konsep menarik yang dapat dipelajari:

1. **Konfigurasi Aplikasi Dinamis**
   - Penggunaan `lazy_static!` untuk inisialisasi konfigurasi statis
   - Implementasi `AppConfig` dengan mekanisme konfigurasi lingkungan menggunakan `dotenvy` dan `rocket::figment`
   - Dukungan untuk konfigurasi default dan override melalui variabel lingkungan

2. **Manajemen Error yang Terstruktur**
   - Tipe alias `Result` dan `Error` untuk penanganan kesalahan yang konsisten
   - Fungsi `compose_error_response` untuk membuat respons error yang seragam
   - Penggunaan `Custom` dan `Json` untuk serialisasi error

3. **Konfigurasi Jaringan**
   - Inisialisasi global `REQWEST_CLIENT` untuk manajemen koneksi HTTP
   - Konfigurasi URL publisher dan receiver dalam satu struktur

## 2. Kemudahan Penambahan Subscriber dengan Pola Observer

Pola Observer memudahkan penambahan subscriber karena:
- Tidak memerlukan modifikasi kode inti publisher
- Setiap subscriber dapat didaftarkan secara independen
- Sistem notifikasi bersifat longgar (loosely coupled)

### Penambahan Multi-Instance
- Menambah instance Main App relatif mudah
- Setiap instance dapat memiliki subscriber berbeda
- Tidak memerlukan perubahan signifikan pada arsitektur

## 3. Postman Testing and Documentation

### Test and Documentation Efforts

Dalam proses pengembangan Receiver app, saya telah melakukan beberapa upaya untuk meningkatkan pengujian dan dokumentasi:

1. **Postman Collection Testing**
   - Saya menemukan bahwa koleksi Postman awalnya hanya memiliki satu test case untuk endpoint `/receive`
   - Test case tersebut hanya mencakup skenario notifikasi dengan status "DELETED"
   - Saya menambahkan test case baru untuk skenario notifikasi dengan status "CREATED"

2. **Test Case Improvement**
   - Menambahkan payload baru: Smart Watch X1 dengan status "CREATED"
   - Memverifikasi bahwa notifikasi berhasil ditambahkan ke daftar pesan
   - Memastikan format pesan notifikasi sesuai dengan yang diharapkan

3. **Dokumentasi**
   - Walaupun dokumentasi Postman belum komprehensif, saya mulai mendokumentasikan struktur payload
   - Menjelaskan berbagai status notifikasi yang mungkin
   - Memberikan contoh konkret penggunaan endpoint

### Manfaat untuk Pekerjaan

Upaya ini sangat berguna karena:
- Meningkatkan kejelasan cara kerja sistem notifikasi
- Memudahkan developer lain memahami API
- Membantu dalam proses debugging dan pengembangan
- Menyediakan dokumentasi yang dapat digunakan kembali

### Kesimpulan

Meskipun masih sederhana, proses menambahkan test dan dokumentasi memberikan wawasan yang lebih baik tentang fungsionalitas sistem dan mempersiapkan dasar untuk pengembangan lebih lanjut.

