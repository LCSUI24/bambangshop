# BambangShop Publisher App
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
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. Menurut saya single model struct tidak cukup, harus dibuat interface/trait untuk mempermudah mengextend jenis jenis subscriber kedepannya
2. Penggunaan DashMap lebih baik daripada Vec. Karena url harus unik, DashMap dapat ngecek unik dan pencarian/penghapusan subscriber berdasarkan kunci (url) dengan lebih efisien. Jika menggunakan Vec, kita harus melakukan iterasi manual untuk memastikan tidak ada duplikasi yang kurang efisien.
3. Kita tetap membutuhkan DashMap karena singleton hanya design pattern untuk memastikan apakah hanya ada satu instance objek, tetapi tidak otomatis menyelesaikan masalah race condition di Rust. Karena variabel tersebut bersifat global/statis yang diakses oleh banyak thread, kita memerlukan struktur data seperti DashMap agar data tetap konsisten dan aman.

#### Reflection Publisher-2
1. Karena berdasarkan prinsip SRP satu class hanya boleh memiliki satu tujuan atau fungsionalitas. Maka dari itu kita memisah Repository yang bertujuan untuk logika data ke database. Sedangkan Service bertujuan untuk logika bisnis dari aplikasinya. Jadi fungsi model hanya untuk bentuk atau representasi dari data saja.
2. Maka kode akan menumppuk di model dan setiap model akan berinteraksi ke model lain dengan cara berbeda, misal model Notification harus tahu cara mengakses data di Subscriber, dan Subscriber mungkin harus tahu logika Product. Karena logika bisnis ada di model juga itu akan susah di reuse, jadi kita harus menulis logika yang sama berulang ulang dan jika kita mengubah suatu kode ada kemungkinan itu bisa merusak program dan merembet.
3. Postman sangat membantu saya dalam mengerjakan tugas kelompok ataupun proyek pribadi dalam memvalidasi API (memastikan memberikan response yang diinginkan/format json yang sesuai) secara instan tanpa harus membuat front-end nya terlebih dahulu. Salah satu fitur yang menarik menurut saya adalah Collections karena dapat mengelompokkan semua request API proyek agar bisa dibagikan ke anggota tim atau untuk berpindah device.
#### Reflection Publisher-3
1. Dalam tutorial ini, kita menggunakan Push Model. Karena NotificationService (Publisher) mengirimkan data (Notification struct) ke metode update milik Subscriber.
2. 
Advantages: 
Fleksibel, karena Subscriber bisa memilih data apa yang mereka butuhkan saja.
Memiliki Kontrol, karena Subscriber dapat menentukan kapan mereka ingin mengambil data tersebut.
Disadvantages:
Kurang Efisiesn, karena dibutuhkan dua kali komunikasi (satu untuk notifikasi, satu untuk pengambilan data).
3. 
- Efek Domino Jika Gagal: Jika ada satu subscriber yang URL-nya lambat merespons atau sedang down, proses utama aplikasi akan "hang" atau loading menunggu timeout HTTP request tersebut.
- Skalabilitas Buruk: Semakin banyak jumlah subscriber, semakin lama waktu yang dibutuhkan Shop Owner untuk mengupload satu produk, karena sistem harus menyelesaikan antrian pengiriman notifikasi secara berurutan.