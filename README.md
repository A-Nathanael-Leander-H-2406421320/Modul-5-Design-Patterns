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
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or `trait` in Rust) in this BambangShop case, or a single Model `struct` is enough?

    **Answer:**

    In a traditional, local Observer pattern, the Subscriber is defined as an interface (or trait) because the Publisher needs to interact with various different concrete subscriber classes without knowing their specific implementations. The interface provides a common contract to ensure loose coupling.

    However, in the case of BambangShop, we are implementing a distributed Observer pattern where the subscribers are external entities (other Rocket instances) that receive notifications via HTTP requests. In this scenario, we can represent each subscriber as a single Model struct that contains the necessary information (such as the subscriber's URL and the product types they are interested in). The Publisher can then use this information to send notifications without needing a complex interface or trait. Therefore, a single Model struct for Subscriber is sufficient in this case, as the interaction is based on HTTP communication rather than direct method calls.


2. `id` in `Program` and `url` in `Subscriber` is intended to be unique. Explain based on your understanding, is using `Vec` (list) sufficient or using `DashMap` (map/dictionary) like we currently use is necessary for this case?
   
   **Answer:**

   Technically, we could use a `Vec` (list) to store the subscribers and programs, and then iterate through the list to find the unique `id` or `url` when needed. However, this approach would be inefficient, especially as the number of subscribers and programs grows, because it would require linear time complexity (O(n)) for lookups. Also ity would be a nightmare when dealing with unique constraints, as we would need to check for duplicates every time we add a new subscriber or program.

   To solve this, we use a map/dictionary structure like `DashMap`, which allows for constant time complexity (O(1)) for lookups, insertions, and deletions based on the unique key (in this case, `id` for Program and `url` for Subscriber). This makes our implementation more efficient and scalable, especially as the number of subscribers and programs increases. Therefore, using `DashMap` is necessary for this case to ensure efficient management of unique identifiers.

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (`SUBSCRIBERS`) static variable, we used the `DashMap` external library for thread safe `HashMap`. Explain based on your understanding of design patterns, do we still need `DashMap` or we can implement Singleton pattern instead?
   
    **Answer:**
    
    The Singleton pattern is a design pattern that restricts the instantiation of a class to a single instance and provides a global point of access to that instance. While it can be used to ensure that there is only one instance of a class, it does not inherently provide thread safety. In a multi-threaded environment, if multiple threads try to access the Singleton instance simultaneously, it can lead to race conditions and other concurrency issues. `DashMap` solves this specific problem by providing a concurrent, thread-safe hash map that allows multiple threads to read and write safely.


#### Reflection Publisher-2

1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

    **Answer:**
    
    Separation of Service and Repository from the Model ensures separation of concerns, which is a fundamental design principle. The Repository is responsible for data access and storage, while the Service contains the business logic that operates on the data. This separation allows for better maintainability, testability, and scalability of the code. It also promotes a cleaner architecture where each component has a single responsibility, making it easier to manage and evolve the application over time.

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

    **Answer:**
    
    If we only use the Model without separating the Service and Repository, the Model would become bloated with both data representation and business logic. This violates the SRP (Single Responsibility Principle) and makes the code more complex and harder to maintain. Each Model would need to handle its own data storage, retrieval, and business logic, which can lead to tightly coupled code. For example, the Program model would need to know how to notify subscribers directly, and the Subscriber model would need to manage its own list of notifications. This can lead to a tangled web of dependencies between models, making it difficult to understand and modify the code without affecting other parts of the application. By using separations, we can keep the Model from becoming "too fat" and maintain a cleaner, more modular codebase.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

    **Answer:**
    
    Postman is a powerful tool for testing and developing APIs. It allows me to easily send HTTP requests to my endpoints and view the responses, which is crucial for testing the functionality of my REST API. A feature I find particularly helpful is the ability to create and save collections of requests, which allows me to organize my tests and quickly rerun them as I make changes to my code. Overall, Postman is an essential tool for API development and testing, and I plan to continue using it for my Group Project and future software engineering projects.

#### Reflection Publisher-3
