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

1. **Observer Interface vs. Model Struct:** 
    In traditional Observer patterns, defining a Subscriber interface (or trait) allows different kinds of subscribers to receive notifications. In this BambangShop case, if all subscribers behave uniformly and no polymorphic behavior is needed, a single model struct may be sufficient. The trade-off is flexibility versus simplicity—if you plan to extend subscriber behavior in the future, a trait would help.

2. **Vec vs. DashMap for Unique Identifiers:** 
    Using a DashMap (or a similar dictionary) is appropriate here because both the product's id and a subscriber's url are unique. A map provides efficient lookup, insertion, and deletion by key. If you used a Vec, you would need to iterate over the entire list to check uniqueness or remove an element, which is inefficient for large collections.

3. **DashMap vs. Singleton for Thread Safety:** 
    Although the Singleton pattern can ensure a single instance, it does not inherently solve concurrent access issues in a multi-threaded environment. In Rust, using DashMap directly addresses thread-safety by ensuring that concurrent reads/writes are managed correctly. Hence, for our subscribers static storage, a thread-safe container like DashMap is necessary rather than a plain Singleton pattern.

#### Reflection Publisher-2

1. **Separation of Service and Repository from Model:**  
   In the classic MVC pattern, the “Model” is responsible for holding the data. However, when you mix in business logic and database access within the model itself, you risk overloading a single component with multiple responsibilities. Separating the Service and Repository layers brings clear boundaries:
   - The **Repository** handles data storage and retrieval, isolating the data access logic.
   - The **Service** encapsulates business logic, which may involve processing data from multiple repositories or applying specific rules before interacting with the model.
   This division adheres to the Single Responsibility Principle, improves testability, and makes the code more maintainable and scalable.

2. **Implications of Using Only the Model:**  
   If we were to use only the Model to cover data, business logic, and storage, each model (e.g., Product, Subscriber, Notification) would become overly complex. The interactions between these objects would be tightly coupled:
   - A change in the data storage mechanism would force modifications in business logic and vice versa.
   - Business rules might end up scattered across different models, causing duplicated logic and making debugging or enhancements more challenging.
   This tight coupling increases overall code complexity and decreases the modularity of the system, making the system harder to extend or maintain.

3. **Exploring Postman for Testing:**  
   Postman has been incredibly useful for testing our REST API endpoints. It allows us to:
   - **Manually send HTTP requests** to verify that endpoints behave as expected.
   - **Organize requests into collections**, which helps when testing multiple related endpoints during development.
   - **Automate tests** using scripting, which is beneficial when validating responses and ensuring regressions are caught early.
   - **Leverage environment variables** to easily switch between different deployment settings (e.g., local development versus production).
   These features not only streamline testing for this project but are invaluable for future software engineering projects or group work, as they help ensure the API’s reliability and simplify troubleshooting.

#### Reflection Publisher-3

1. **Observer Pattern Variation in This Tutorial:**  
   In this tutorial, we are using the **Push** variation of the Observer Pattern. In our implementation, when a product is created, deleted, or promoted, the publisher immediately pushes a notification payload to each subscriber via an HTTP POST request.

2. **Considering the Pull Variation:**  
   If we were to use the **Pull** variation instead, the subscribers would instead request updates from the publisher on demand.  
   - **Advantages of the Pull Model:**  
     - Decouples the publisher from the timing of notifications—subscribers retrieve information only when needed.  
     - Potentially reduces unnecessary network calls if subscribers check infrequently.  
   - **Disadvantages of the Pull Model:**  
     - Increases complexity, as subscribers must implement logic to repeatedly poll the publisher for updates.  
     - May lead to delays in receiving notifications and increased load due to frequent polling, especially if updates are infrequent.
  
3. **Impact of Not Using Multi-threading in the Notification Process:**  
   Our current implementation spawns a separate thread for each notification sent to a subscriber. Without multi-threading, the notification process would operate sequentially. This means that if one subscriber takes a long time to process the request or encounters a delay, it would block the notification of subsequent subscribers. Overall, this would slow down the notification process and potentially decrease the responsiveness of the entire application.