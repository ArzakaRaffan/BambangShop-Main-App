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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
>In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

Dalam BambangShop main app, subscriber hanya memiliki satu kelas sehingga penggunaan single model struct saja sudah cukup dalam pengimplementasian berdasarkan observer pattern karena tidak ada perbedaan perilaku pada subscriber. Namun jika subscriber memiliki lebih dari satu kelas dengan perilaku yang berbeda, alangkah baiknya untuk mengimplementasikan interface (atau trait di Rust) agar kode lebih modular dan memungkinkan penambahan subscriber lain tanpa mengubah kode inti

>id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Menurut saya, penggunaan DashMap seperti yang digunakan sekarang sudah cukup karena app ini kemungkinan memiliki data yang sedikit dan jarang dicari. DashMap juga cocok karena dalam segi kompleksitas, pencarian dan penghapusan memerlukan kompleksitas O(1) dibandingkan dengan Vec yang memiliki kompleksitas pencarian O(n) (worst case) dan juga kompleksitas penghapusan O(n) karena harus menggeser elemen setelahnya.

>When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

Dalam `repository/subscriber.rs` sudah diimplementasikan `lazy_static!` yang membuat satu static instance dari DashMap yang mana merupakan cara pengimplementasian singleton patter sehingga dalam App ini, DashMap dan Singleton pattern diimplementasikan secara bersamaan. Lalu DashMap juga sudah cukup untuk menangani concurrency secara aman dan efisien karena sudah Thread-safe sehingga kinerja thread sudah dijamin aman.

#### Reflection Publisher-2
>In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Adanya pemisahan antara Repository dan Service berkaitan erat dengan salah satu SOLID Principle yakni Single Responsibility Principle (SRP) dimana terdapat pemisahan yang jelas untuk perilaku yang berbeda. Model hanya berfungsi dalam representasi data saja sedangkan Repository menangani interaksi dengan menyimpanan dan modifikasi data. Service berfokus kepada logika bisnis tanpa mengubah data yang ada. Dengan pendekatan ini, kode menjadi lebih modular dan lebih mudah di-maintain.

>What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Jika hanya menggunakan Model, berarti Model harus menangani logika bisnis, pengambilan data, dan juga pengiriman notifikasi dalam satu tempat. Hal ini menjadikan kode sangat kompleks dan sulit dipahami. Jika terdapat _update_ ke salah satu method, maka akan sangat banyak yang harus di-fix karena banyak code yang saling terkait (_shotgun surgery_). 

>Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Sebelumnya, saya sudah pernah sedikit mencoba dan mengeksplorasi Postman. Postman sangat esensial karena dapat memudahkan saya dalam mengirim HTTP Request dan melihat reponse dari web dengan sangat mudah. Postman dalam App ini dapat digunakan untuk mengetes API Subscriber yang dapat memvalidasi flow dari data dan juga response nya. Fitur collections adalah salah satu fitur yang paling membantu saya karena dapat membantu saya dalam mengelola API Request dan mengatur variabel-variabel seperti base urls, auth tokens, dan lain-lain.

#### Reflection Publisher-3
