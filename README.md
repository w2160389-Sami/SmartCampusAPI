# Smart Campus API

## Overview
This project is a RESTful API built using JAX-RS for a smart campus system. The API is designed to manage rooms, sensors, and sensor readings. It allows users to create rooms, assign sensors to those rooms, and store historical data collected from sensors.

The system uses in-memory data structures such as HashMaps and ArrayLists instead of a database, as required in the coursework.

---

## How to Run the Project
1. Open the project in NetBeans  
2. Build the project using Maven  
3. Run the project on a server like GlassFish or Tomcat  
4. Access the API using the following base URL:  

http://localhost:8080/SmartCampusAPI/api/v1  

## Sample CURL Commands

1. Get API info
curl -X GET http://localhost:8080/SmartCampusAPI/api/v1

2. Create a Room
curl -X POST http://localhost:8080/SmartCampusAPI/api/v1/rooms \
-H "Content-Type: application/json" \
-d '{"id":"R1","name":"Library Room","capacity":50}'

3. Get all Rooms
curl -X GET http://localhost:8080/SmartCampusAPI/api/v1/rooms

4. Create a Sensor
curl -X POST http://localhost:8080/SmartCampusAPI/api/v1/sensors \
-H "Content-Type: application/json" \
-d '{"id":"S1","type":"Temperature","status":"ACTIVE","currentValue":0,"roomId":"R1"}'

5. Get Sensors by Type
curl -X GET "http://localhost:8080/SmartCampusAPI/api/v1/sensors?type=Temperature"
---

## Part 1: Service Architecture & Setup

### JAX-RS Resource Lifecycle
In JAX-RS, resource classes are usually created per request, meaning a new instance is made every time a request is sent. This helps avoid conflicts between different users accessing the system at the same time.

Because of this, shared data should not be stored directly inside resource classes. In this project, a separate DataStore class is used to store all data using HashMaps. This ensures that the data is accessible across multiple requests.

However, since multiple requests can access the same data, there is a risk of race conditions. This means developers need to be careful when handling shared data.

---

### HATEOAS
HATEOAS allows an API to include links in its responses so that clients can easily find available actions. For example, the main endpoint returns links to rooms and sensors.

This makes the API easier to use because clients don’t need to rely on fixed documentation. It also allows the API to change over time without breaking existing clients.

---

## Part 2: Room Management

### IDs vs Full Objects
Returning only IDs reduces the amount of data sent over the network, which improves performance. However, the client would need to make additional requests to get full details.

Returning full objects gives more complete information in one request, which is easier for the client to use. In this project, full objects are returned to keep things simple.

---

### DELETE Idempotent
The DELETE operation is considered idempotent because sending the same request multiple times does not change the result after the first successful deletion.

For example, if a room is deleted once, trying to delete it again will not affect the system further. This ensures consistent behaviour even if duplicate requests are sent.

---

## Part 3: Sensor Operations

### @Consumes JSON
The @Consumes(MediaType.APPLICATION_JSON) annotation ensures that the API only accepts JSON data. If a client sends data in a different format, such as plain text or XML, the request will be rejected.

This helps maintain consistency and prevents errors caused by unexpected data formats.

---

### Query Parameters vs Path Parameters
Query parameters are more suitable for filtering because they are optional and flexible. For example, sensors can be filtered by type using a query parameter.

Using path parameters for filtering would make the API less flexible and require multiple different endpoints. Query parameters allow easier expansion and better design.

---

## Part 4: Sub-resource Locator

The sub-resource locator pattern allows nested resources to be handled in separate classes. In this project, sensor readings are managed in a different class instead of being included in the main sensor resource.

This improves code organisation and makes the system easier to maintain. It also reduces complexity in larger applications.

---

## Part 5: Error Handling and Logging

### 422 vs 404
HTTP 422 is used when the request is valid but contains incorrect data, such as referencing a room that does not exist. A 404 error is used when the endpoint itself cannot be found.

Using 422 provides a clearer explanation of the problem.

---

### Stack Trace Risks
Returning full stack traces can expose sensitive information such as file paths and internal logic. This can be used by attackers to find vulnerabilities in the system.

Instead, this project returns simple error messages to protect the system.

---

### Logging Filters
Logging is handled using JAX-RS filters instead of adding logging code inside every method. This makes the code cleaner and avoids repetition.

It also ensures that all requests and responses are logged consistently, which helps with debugging.

---

## Conclusion
This project demonstrates the implementation of a RESTful API using JAX-RS, including resource management, nested resources, filtering, and error handling. The system follows REST principles and provides a structured way to manage smart campus data.
