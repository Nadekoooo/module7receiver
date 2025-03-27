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

## Refleksi Subscriber-1

### 1. Mengapa kita menggunakan `RwLock<Vec<Notification>>`, dan bukan `Mutex<Vec<Notification>>`?

Dalam tutorial ini, kita menggunakan `RwLock<>` untuk menyinkronkan akses ke `Vec<Notification>` karena kita ingin memungkinkan **banyak thread membaca secara bersamaan**, namun tetap membatasi akses tulis hanya satu thread pada satu waktu. `RwLock` (Read-Write Lock) memberi fleksibilitas untuk mengoptimalkan performa saat operasi baca lebih sering dilakukan dibanding tulis. Sementara itu, `Mutex<>` mengunci secara eksklusif bahkan untuk operasi baca, artinya hanya satu thread yang bisa mengakses data, baik untuk membaca maupun menulis. Ini kurang efisien untuk kasus kita, karena pembacaan notifikasi bisa dilakukan oleh banyak subscriber sekaligus tanpa saling mengganggu. Maka dari itu, `RwLock` lebih cocok digunakan pada situasi seperti ini.

### 2. Mengapa Rust tidak memperbolehkan kita memodifikasi `static` variable seperti di Java?

Rust tidak mengizinkan kita langsung memodifikasi `static` variable karena alasan keamanan memory dan **thread safety**. Berbeda dengan Java, Rust menerapkan sistem kepemilikan (*ownership*) dan pengecekan borrow di waktu kompilasi untuk mencegah data race. Variable `static` bersifat global dan bisa diakses dari berbagai tempat sekaligus, yang berarti rentan terhadap kondisi tidak sinkron (race condition) jika tidak dijaga dengan benar. Oleh karena itu, Rust mendorong penggunaan `lazy_static` dikombinasikan dengan `Mutex` atau `RwLock` untuk memastikan bahwa setiap akses terhadap data global bersifat aman dan terkontrol. Ini membuat program kita lebih stabil, aman, dan bebas dari bug yang sulit dilacak akibat akses data global yang sembarangan.

## Refleksi Subscriber-2

### 1. Apakah saya mengeksplorasi hal di luar langkah tutorial (seperti `src/lib.rs`)?

Ya, saya sempat mengeksplorasi bagian `src/lib.rs` untuk memahami bagaimana struktur aplikasi Rust disusun secara modular. Dari situ saya belajar bahwa `lib.rs` berfungsi sebagai tempat utama untuk mendeklarasikan module-module yang ada, sehingga file ini punya peran penting untuk membuat semua bagian program saling terhubung. Meskipun tidak diminta di tutorial, saya penasaran karena sering melihat `mod` dideklarasikan di berbagai tempat, dan ternyata semuanya biasanya dilaporkan ke `lib.rs`. Saya juga jadi lebih paham bagaimana konsep visibility (`pub mod`) bekerja dalam skala crate. Walaupun eksplorasi saya masih terbatas dan belum sampai mengubah apa-apa di file tersebut, saya merasa ini membantu membentuk pemahaman yang lebih menyeluruh tentang bagaimana project Rust disusun dan bagaimana setiap komponen bisa saling dipanggil.

### 2. Bagaimana Observer Pattern mempermudah penambahan subscriber baru? Bagaimana jika Main app-nya lebih dari satu?

Observer pattern membuat penambahan subscriber menjadi sangat mudah karena setiap subscriber bisa langsung mendaftar (subscribe) tanpa perlu konfigurasi tambahan di sisi publisher. Saya sudah mencoba menjalankan tiga instance Receiver berbeda dan semuanya bisa menerima notifikasi tanpa perlu mengubah satu baris pun di Main app. Cukup jalankan instance baru dan kirim request subscribe, maka sistem langsung mengenali subscriber baru tersebut. Ini tentunya membuat sistem sangat fleksibel dan mendukung skalabilitas. Kalau misalnya kita menambah lebih dari satu instance Main app, sistem masih bisa tetap bekerja selama semua instance Main app mengakses data subscriber yang sama, misalnya melalui shared database atau state manager. Dengan kata lain, Observer pattern membuat publisher dan subscriber tidak perlu tahu detail satu sama lain, cukup melalui kontrak endpoint yang sudah disepakati.

### 3. Apakah saya sudah mencoba fitur test atau dokumentasi tambahan di Postman?

Saya sudah mencoba menambahkan dokumentasi di setiap endpoint Postman untuk menjelaskan tujuan, input, dan contoh output-nya. Ini membantu saya dan teman satu kelompok untuk lebih mudah memahami alur API saat testing. Saya juga mencoba fitur test sederhana di Postman, misalnya untuk mengecek apakah response mengandung status code 200 dan apakah field tertentu ada dalam response JSON. Walaupun belum mendalam atau kompleks, fitur test ini cukup membantu untuk memastikan tidak ada endpoint yang error setelah melakukan perubahan di backend. Fitur dokumentasi dan test di Postman juga sangat membantu dalam kolaborasi, karena orang lain yang mengakses collection-nya bisa langsung paham cara pakainya tanpa harus banyak bertanya. Ke depannya saya ingin eksplorasi lebih lanjut fitur seperti environment dan pre-request scripts agar testing bisa lebih otomatis dan efisien.

