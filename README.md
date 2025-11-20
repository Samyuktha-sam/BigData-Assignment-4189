# Kafka Order Processing System 

This project implements a **Kafka-based order processing system** for the Big Data assignment.  
It uses **Spring Boot** for the REST API and integrates with **Apache Kafka** using **Avro serialization**.

The system supports:

-  Producing order messages to Kafka (with Avro)
-  Real-time aggregation (running average of order prices)
-  Retry logic for temporary processing failures
- Dead Letter Queue (DLQ) for permanently failed messages
-  REST APIs to send orders, view stats, and inspect failed messages
-  Git repository for submission and live demonstration


## 1. Assignment Requirements Mapping

From the assignment:

> Students will build a Kafka-based system that produces and consumes order messages.  
> Each message must use Avro serialization, and the system must support:
> - Real-time aggregation (running average of prices)  
> - Retry logic for temporary failures  
> - Dead Letter Queue (DLQ) for permanently failed messages  

This project implements:

- **Order Producer**  
  - Sends Avro-encoded `Order` messages to the main Kafka topic (e.g. `orders`).

- **Order Consumer + Aggregation**  
  - Consumes `Order` messages.  
  - Maintains a **running average** of all processed prices using `PriceAggregationService`.

- **Retry Logic**  
  - On temporary errors, messages are retried and/or sent to a retry topic (e.g. `orders_retry`).

- **Dead Letter Queue (DLQ)**  
  - If a message still fails after maximum retries, it is sent to a DLQ topic (e.g. `orders_dlq`) and stored by `DLQConsumerService`.

- **REST Interface**  
  - `OrderController` exposes HTTP endpoints to:
    - Send single or batch orders
    - Get current aggregation stats
    - Reset stats
    - View failed messages
    - Check health status

---

## 2. Tech Stack

- **Language**: Java (17+ recommended)
- **Framework**: Spring Boot
- **Messaging**: Apache Kafka
- **Serialization**: Avro
- **Build Tool**: Maven or Gradle (depending on your setup)
- **Logging**: SLF4J + Logback

---

## 3. Order Schema (Avro)

The `Order` messages follow this Avro schema (`order.avsc`):

```json
{
  "type": "record",
  "name": "Order",
  "namespace": "com.assignment.avro",
  "fields": [
    { "name": "orderId", "type": "string" },
    { "name": "product", "type": "string" },
    { "name": "price", "type": "float" }
  ]
}
