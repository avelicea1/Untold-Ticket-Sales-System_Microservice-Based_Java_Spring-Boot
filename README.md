# Untold Ticket Sales System

## Overview
This repository contains a microservice-based ticket sales system for the Untold festival, split into three main assignments/projects:

- **Assignment 1:** Core Ticket Sales System (Monolithic Spring Boot application)
- **Assignment 2:** Microservice-based Ticket Sales System (Spring Boot, RabbitMQ integration, additional features)
- **Assignment 3:** Mail Microservice (Spring Boot, handles email notifications via RabbitMQ)

Each assignment is a standalone Maven project, but Assignment 2 and 3 together demonstrate a microservice architecture with asynchronous communication using RabbitMQ.

---

## Project Structure

- `PS2024_30238_Velicea_Andreea_Ioana_Assignment_1/`  
  Monolithic Spring Boot application for ticket sales.
- `PS2024_30238_Velicea_Andreea_Ioana_Assignment_2/`  
  Main microservice application (Ticket, Order, User, Sale, Payment, Cart, etc.)
- `PS2024_30238_Velicea_Andreea_Ioana_Assignment_3/`  
  Mail microservice for sending emails, decoupled from the main app via RabbitMQ.

---

## How It Works

### Assignment 1: Monolithic Application
Implements the basic ticket sales logic (CRUD for tickets, users, orders, etc.) in a single Spring Boot application. All business logic, persistence, and controllers are in one service.

### Assignment 2: Microservice Application
- **Domain:** Handles tickets, users, orders, sales, payments, categories, carts, and more.
- **Architecture:** Still a single deployable unit, but refactored for microservice readiness (DTOs, mappers, service separation, etc.).
- **RabbitMQ Integration:** When certain actions occur (e.g., order placed, payment completed), a message is sent to RabbitMQ for asynchronous processing by other services (like the mail microservice).
- **Key Classes:**
  - `RabbitMQConfig.java`: Configures RabbitMQ connection, exchange, queue, and binding.
  - `RabbitMQSender.java`: Sends messages (payloads) to RabbitMQ.
  - `Payload.java`: DTO for message content.

### Assignment 3: Mail Microservice
- **Domain:** Listens for messages on the RabbitMQ queue.
- **Function:** When a relevant event occurs (e.g., order placed), the mail microservice receives the message and sends an email notification using SMTP (Gmail).
- **Key Classes:**
  - `RabbitMQConfig.java`: Configures RabbitMQ listener and connection.
  - `QueueListener.java` / `RabbitMQReceiver.java`: Listens for messages and triggers email sending.
  - `EmailService.java`: Handles email composition and sending.

---

## Microservice Communication (RabbitMQ)

- **Exchange/Queue:** Both Assignment 2 and 3 use the same exchange, queue, and routing key (see `application.properties`).
- **Flow:**
  1. Main app (Assignment 2) sends a message to RabbitMQ when an event occurs.
  2. Mail microservice (Assignment 3) listens to the queue, receives the message, and sends an email.
- **Configuration:**
  - Host: `localhost`, Port: `5672`, Default credentials (`guest`/`guest`)
  - Exchange: `rabbitmq.exchange`
  - Queue: `rabbitmq.queue`
  - Routing Key: `rabbitmq.routingkey`

---

## How to Run

1. **Start RabbitMQ** (locally, Docker, or cloud)
2. **Start Assignment 3 (Mail Microservice)**
   - `cd PS2024_30238_Velicea_Andreea_Ioana_Assignment_3`
   - `./mvnw spring-boot:run`
3. **Start Assignment 2 (Main Microservice App)**
   - `cd PS2024_30238_Velicea_Andreea_Ioana_Assignment_2`
   - `./mvnw spring-boot:run`
4. **(Optional) Assignment 1** can be run independently for monolithic reference.

---

## How the Microservices Are Related
- Assignment 2 is the main business logic and user-facing API.
- Assignment 3 is a background worker for sending emails, triggered by events in Assignment 2.
- They communicate asynchronously via RabbitMQ, so the main app is not blocked by email sending.

---

## Technologies Used
- Java 17/21
- Spring Boot 3.x
- RabbitMQ (AMQP)
- PostgreSQL (Assignment 2)
- Maven
- SMTP (Gmail) for email

---

## Security & Configuration
- Sensitive credentials (DB, SMTP, RabbitMQ) are in `application.properties`. For production, use environment variables or a secrets manager.
- Default RabbitMQ and Gmail credentials are for local development only.

---

## Extending the System
- Add more microservices (e.g., analytics, notifications, reporting) by subscribing to the same RabbitMQ exchange.
- Refactor Assignment 2 into multiple deployable microservices for true distributed architecture.


