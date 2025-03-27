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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. In this tutorial, we used RwLock<> to synchronise the use of Vec of Notifications. Explain why it is necessary for this case, and explain why we do not use Mutex<> instead?

Disini kita menggunakan RwLock<> untuk sinkronisasi Vec<Notification> karena lebih efisien dalam skenario read-write yang tidak sering bersamaan. RwLock memungkinkan banyak thread membaca data secara bersamaan selama tidak ada thread yang menulis, sedangkan Mutex hanya mengizinkan satu thread aja dalam mengakses data sekaligus baik untuk baca atau tulis. Karena operasi baca (seperti list_all_as_string) akan lebih sering terjadi daripada operasi tulis (add), yang mana RwLock memberikan performa yang lebih baik. Selain itu, RwLock lebih cocok untuk data yang banyak dibaca karena mengurangi antrian yang mungkin terjadi antar thread.

2. In this tutorial, we used lazy_static external library to define Vec and DashMap as a “static” variable. Compared to Java where we can mutate the content of a static variable via a static function, why did not Rust allow us to do so?

Rust tidak membolehkan mutasi langsung pada variabel static karena alasan keamanan memory dan thread safety. Hal tersebut berbeda dengan Java yang memiliki JVM untuk mengelola memory dan thread, Rust harus menjamin safety pada compile time. Lalu, library lazy_static menyediakan solusi dengan melakukan inisialisasi lazy (hanya saat pertama kali diakses) dan thread-safe. Yang mana hal tersebut memastikan variabel static tetap immutable (alamat memorinya tidak berubah) sementara isinya bisa dimutasi dengan aman melalui mekanisme synchronisasi seperti RwLock. Pendekatan ini lebih eksplisit dan preventif terhadap race condition dibanding Java yang mengandalkan programmernya untuk mengelola synchronisasi secara manual.

#### Reflection Subscriber-2

1. Have you explored things outside of the steps in the tutorial, for example: src/lib.rs? If not, explain why you did not do so. If yes, explain things that you have learned from those other parts of code.

Saya coba beberapa eksplorasi, salah satunya file yang berisi inisialisasi penting seperti APP_CONFIG dan REQWEST_CLIENT yang digunakan di seluruh proyek. Dari sini saya belajar bagaimana Rocket framework mengintegrasikan konfigurasi environment dengan sistem, serta cara membuat HTTP client yang reusable. Namun, saya tidak melakukan modifikasi signifikan karena takut mengganggu fungsionalitas inti yang sudah diatur di tutorial. Dari hasil eksplorasi ini, saya sedikit lebih memahami backbone dari aplikasi sebelum fokus ke fitur spesifik.

2. Since you have completed the tutorial by now and have tried to test your notification system by spawning multiple instances of Receiver, explain how Observer pattern eases you to plug in more subscribers. How about spawning more than one instance of Main app, will it still be easy enough to add to the system?

Pola Observer membuat penambahan subscriber baru dengan mudah, hanya jalankan instance Receiver tambahan dengan port unik dan mereka otomatis menerima notifikasi yang relevan. Sistem ini tetap efisien karena Main App hanya perlu mengirim notifikasi sekali ke endpoint utama, lalu subscriber yang tertarik akan memprosesnya sendiri. Akan tetapi, jika ada multiple Main App instances, akan muncul kompleksitas baru seperti sinkronisasi state produk dan manajemen daftar subscriber. Mungkin Solusinya perlu traffic controller yang ngatur alur pembagian kerjanya atau ga message broker sebagai perantara yang mengantarkan notifikasi ke semua Receiver secara terorganisir

3. Have you tried to make your own Tests, or enhance documentation on your Postman collection? If you have tried those features, tell us whether it is useful for your work (it can be your tutorial work or your Group Project).

Saya sendiri belum mencoba untuk membuat test sendiri ataupun nambahin dokumentasi pada collectionnya. Namun, saya mencoba beberapa kombinasi test pada collection yang sudah disediakan. Hal tersebut dilakukan untuk mencek apakah API yang sudah dibuat sesuai dengan apa yang diinginkan dan notifikasi yang berkaitan. Dengan hal itu, cukup membantu saya dalam memahami alur yang terjadi dari pola desain ini.