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

> 1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

In the classical Observer pattern, the Subscriber is often defined as an interface (or trait in Rust) to provide flexibility and allow for multiple implementations. However, in our BambangShop design, every subscriber follows the same behavior by simply receiving notifications via HTTP POST. This uniformity implies that a single Subscriber model struct is enough to cover the application's requirements. While a trait might be useful for future extensions that require different behaviors, it would add unnecessary complexity at this stage. Overall, the simplicity of a model struct makes it the preferred choice for our current implementation.

> 2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

In our design, ensuring that the id in Program and the url in Subscriber are unique is essential for reliable data operations. If we stored subscribers in a Vec, every lookup or deletion would require a full scan of the list, which can become inefficient as the data grows. On the other hand, DashMap allows constant-time access by using a key-based lookup, making operations much more efficient. Additionally, DashMap is thread-safe, a critical feature for an application that may handle concurrent access. Thus, for both performance and safety reasons, using a DashMap is necessary instead of a simple Vec.

> 3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

Although the Singleton pattern can guarantee a single global instance of our Subscriber repository, it does not inherently resolve concurrency issues. In Rust, where thread safety is enforced by the compiler, any mutable shared state must be protected by thread-safe data structures. DashMap is specifically designed to handle concurrent access safely, preventing race conditions and ensuring efficient multi-threaded operations. Even if we wrap our repository in a Singleton, we still need DashMap as the underlying mechanism to manage concurrent modifications. Therefore, using DashMap remains indispensable, even in a Singleton-based design.

#### Reflection Publisher-2

> 1. In the Model-View Controller (MVC) compound pattern, there is no "Service" and "Repository". Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate "Service" and "Repository" from a Model?

Separating Service and Repository from the Model improves the design by adhering to the Single Responsibility Principle and Clean Architecture. The Model is primarily responsible for representing the domain data, while the Repository abstracts the data persistence logic, and the Service contains the business logic that orchestrates how data is processed and interacted with. This separation makes the codebase more modular, easier to test, and maintain because changes in one layer (like data storage) do not directly impact the business logic or domain representation. It also provides better scalability, as each component can evolve independently, and potential integrations with other systems can occur with minimal changes. Overall, isolating responsibilities leads to a cleaner and more manageable code structure.

> 2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

If we only use the Model to handle data storage and business logic, the code becomes tightly coupled and more complex over time. Each model—whether for Program, Subscriber, or Notification—would need to manage its data and logic, leading to duplicated code and increased maintenance overhead. The resulting intermingling of concerns might make it difficult to isolate and fix issues, complicate debugging, and hinder scalability. Moreover, any enhancements or new feature integrations could potentially introduce unintended side effects across models. This tightly coupled design would negatively impact the overall robustness and flexibility of the application.

> 3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

I have extensively used Postman in previous courses such as PBP and PKPL for testing backend services and find it extremely useful in the current project as well. Postman simplifies the process of sending HTTP requests, enabling me to quickly validate API endpoints and inspect responses in real-time. Its features, like the use of environment variables and the ability to create organized collections of tests, streamline both the development and debugging process. I particularly appreciate the support for automated testing through scripting, which helps in verifying that the backend behaves as expected after changes. These functionalities not only increase my productivity but also ensure a higher quality of code, which is beneficial for group projects and my future software engineering endeavors.

#### Reflection Publisher-3