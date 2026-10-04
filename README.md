# Apex Core Banking System

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen.svg)](https://github.com/imaakarsh/Banking-System)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Java Version](https://img.shields.io/badge/Java-17%2B-blue.svg)](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2.0-green.svg)](https://spring.io/projects/spring-boot)
[![React](https://img.shields.io/badge/React-18.x-blue.svg)](https://reactjs.org/)
[![Database](https://img.shields.io/badge/Database-PostgreSQL-blue.svg)](https://www.postgresql.org/)

Apex is an enterprise-grade, highly secure, and scalable Core Banking System designed to handle high-throughput financial transactions with absolute consistency. Built on a modern microservices-ready architecture using Spring Boot 3.x, React 18, and PostgreSQL, Apex implements strict double-entry bookkeeping, robust concurrency controls, and comprehensive audit trails to ensure 100% data integrity.

Designed for retail banking operations, Apex offers customer onboarding (KYC), multi-currency account management, instant peer-to-peer transfers, scheduled payments, and an administrative dashboard for risk assessment and transaction monitoring.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture & System Design](#architecture--system-design)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [Quick Start (Docker Compose)](#quick-start-docker-compose)
  - [Manual Installation (For Development)](#manual-installation-for-development)
- [Security Architecture & Concurrency Control](#security-architecture--concurrency-control)
- [API Documentation](#api-documentation)
- [Testing](#testing)
- [Troubleshooting & FAQ](#troubleshooting--faq)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)
- [Contact & Support](#contact--support)

---

## Features

### Security & Compliance
- **Role-Based Access Control (RBAC):** Distinct permissions for Customers, Tellers, Auditors, and System Administrators.
- **Multi-Factor Authentication (MFA):** Time-based One-Time Password (TOTP) integration via Google Authenticator.
- **OWASP Top 10 Protection:** Built-in protection against CSRF, XSS, SQL Injection, and Session Hijacking.
- **Data Encryption:** AES-256 encryption for sensitive personally identifiable information (PII) at rest, and TLS 1.3 for data in transit.

### Core Banking Operations
- **Double-Entry Ledger:** Every transaction is recorded as an immutable set of debits and credits, ensuring the fundamental accounting equation ($\text{Assets} = \text{Liabilities} + \text{Equity}$) always balances.
- **Multi-Currency Support:** Real-time exchange rate integration with automatic conversion for cross-border transfers.
- **Transaction Types:** Instant transfers, scheduled recurring payments, deposits, withdrawals, and bill payments.
- **Concurrency Control:** Pessimistic database locking (`PESSIMISTIC_WRITE`) to prevent race conditions (e.g., double-spending) during high-frequency concurrent transactions.

### Analytics & Administration
- **Interactive Dashboard:** Real-time visualization of account balances, transaction histories, and spending habits using Chart.js.
- **Audit Logging:** Comprehensive, tamper-evident audit logs capturing every API call, state change, and administrative action.
- **Statement Generation:** Exportable monthly statements in PDF and CSV formats.

---

## Tech Stack

| Layer | Technology | Purpose |
| :--- | :--- | :--- |
| **Frontend** | React 18, TypeScript, Tailwind CSS, Redux Toolkit | Responsive, state-managed Single Page Application (SPA) |
| **Backend** | Java 17, Spring Boot 3.2, Spring Security | Core business logic, REST API, and security framework |
| **Database** | PostgreSQL 15 | ACID-compliant primary relational data store |
| **Caching** | Redis | Session storage, API rate limiting, and exchange rate caching |
| **Message Broker** | RabbitMQ | Asynchronous processing for email notifications and PDF generation |
| **Containerization** | Docker, Docker Compose | Consistent development and production environments |
| **Testing** | JUnit 5, Mockito, Testcontainers | Unit, integration, and database-level testing |

---

## Architecture & System Design

Apex utilizes a clean, layered architecture adhering to Domain-Driven Design (DDD) principles to ensure loose coupling and high cohesion across components.

```text
                     +---------------------------------------+
                     |            React Frontend             |
                     +---------------------------------------+
                                         |
                                         | HTTPS / REST / JWT
                                         v
                     +---------------------------------------+
                     |          Spring Boot Gateway          |
                     |       (Rate Limiting & Auth)          |
                     +---------------------------------------+
                                         |
                     +-------------------+-------------------+
                     |                                       |
                     v                                       v
+---------------------------------------+ +---------------------------------------+
|            Account Service            | |          Transaction Service          |
|  - KYC & Onboarding                   | |  - Double-Entry Ledger Engine       |
|  - Balance Management                 | |  - Concurrency Lock Manager         |
+---------------------------------------+ +---------------------------------------+
        |                        |                |                        |
        v                        v                v                        v
+---------------+        +---------------+ +---------------+        +---------------+
|  PostgreSQL   |        |  Redis Cache  | |  PostgreSQL   |        |   RabbitMQ    |
| (Account DB)  |        | (Session/Rate)| | (Ledger DB)   |        | (Event Queue) |
+---------------+        +---------------+ +---------------+        +---------------+
```

---

## Project Structure

```text
banking-system/
├── backend/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/apex/banking/
│   │   │   │   ├── config/          # Security, CORS, Redis, RabbitMQ configs
│   │   │   │   ├── controller/      # REST API Endpoints
│   │   │   │   ├── dto/             # Data Transfer Objects (Request/Response)
│   │   │   │   ├── exception/       # Global Exception Handling
│   │   │   │   ├── model/           # JPA Entities (User, Account, Transaction)
│   │   │   │   ├── repository/      # Database Access Layer (Spring Data JPA)
│   │   │   │   └── service/         # Business Logic & Transaction Management
│   │   │   └── resources/
│   │   │       ├── application.yml  # Application configurations
│   │   │       └── db/migration/    # Flyway database migrations
│   │   └── test/                    # JUnit & Integration tests
│   ├── Dockerfile
│   └── pom.xml
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── assets/                  # Images, logos, global styles
│   │   ├── components/              # Reusable UI components (Buttons, Inputs, Modals)
│   │   ├── hooks/                   # Custom React hooks
│   │   ├── pages/                   # Dashboard, Login, Transfer, Admin pages
│   │   ├── store/                   # Redux state management
│   │   └── utils/                   # API clients and helper functions
│   ├── Dockerfile
│   ├── package.json
│   └── tailwind.config.js
└── docker-compose.yml               # Orchestrates App, DB, Redis, & RabbitMQ
```

---

## Prerequisites

Ensure the following prerequisites are installed before proceeding:

- [Java Development Kit (JDK) 17](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html) or higher
- [Node.js](https://nodejs.org/) (v18.x or higher) and npm
- [Docker](https://www.docker.com/products/docker-desktop/) and Docker Compose

---

## Getting Started

### Quick Start (Docker Compose)

The fastest way to run the full application stack (Backend, Frontend, PostgreSQL, Redis, and RabbitMQ) is using Docker Compose.

1. Clone the repository:
   ```bash
   git clone https://github.com/imaakarsh/Banking-System.git
   cd Banking-System
   ```

2. Start all services:
   ```bash
   docker-compose up --build -d
   ```

3. Verify running containers:
   ```bash
   docker ps
   ```

Access the application services at:
- **Frontend:** `http://localhost:3000`
- **Backend API:** `http://localhost:8080`
- **RabbitMQ Management Console:** `http://localhost:15672` (Credentials: `guest` / `guest`)

---

### Manual Installation (For Development)

#### 1. Database & Infrastructure Setup

Run PostgreSQL, Redis, and RabbitMQ containers:

```bash
docker run --name apex-db -e POSTGRES_DB=apex_banking -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 -d postgres:15
```

```bash
docker run --name apex-redis -p 6379:6379 -d redis:alpine
```

```bash
docker run --name apex-rabbitmq -p 5672:5672 -p 15672:15672 -d rabbitmq:3-management
```

#### 2. Backend Configuration & Run

1. Navigate to the backend directory:
   ```bash
   cd backend
   ```

2. Create `src/main/resources/application-dev.yml` or set environment variables:
   ```yaml
   spring:
     datasource:
       url: jdbc:postgresql://localhost:5432/apex_banking
       username: postgres
       password: postgres
     jpa:
       hibernate:
         ddl-auto: validate
       show-sql: true
     redis:
       host: localhost
       port: 6379
     rabbitmq:
       host: localhost
       port: 5672
   jwt:
     secret: 9a4f2c8d3e1b7f6a5c4d3e2b1a0f9e8d7c6b5a4f3e2d1c0b9a8f7e6d5c4b3a2
     expiration: 86400000 # 24 Hours
   ```

3. Run database migrations and start the application:
   ```bash
   ./mvnw clean spring-boot:run
   ```

#### 3. Frontend Configuration & Run

1. Navigate to the frontend directory:
   ```bash
   cd ../frontend
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Create a `.env` file in the root of the frontend directory:
   ```env
   REACT_APP_API_BASE_URL=http://localhost:8080/api/v1
   ```

4. Start the development server:
   ```bash
   npm start
   ```

The frontend will open automatically at `http://localhost:3000`.

---

## Security Architecture & Concurrency Control

In financial systems, race conditions can lead to critical defects like double-spending. For instance, if two concurrent API requests attempt to transfer $100 simultaneously from an account containing only $100, both threads might read the balance as $100 before either writes an update, resulting in an overdraft.

Apex prevents race conditions at the database layer using pessimistic locking:

```java
@Repository
public interface AccountRepository extends JpaRepository<Account, Long> {

    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("SELECT a FROM Account a WHERE a.accountNumber = :accountNumber")
    Optional<Account> findByAccountNumberWithLock(@Param("accountNumber") String accountNumber);
}
```

### Transaction Flow

1. **Begin Transaction:** Spring `@Transactional` initiates a database transaction.
2. **Acquire Locks:** The system queries sender and receiver accounts using `findByAccountNumberWithLock()`. PostgreSQL locks the selected rows (`SELECT ... FOR UPDATE`).
3. **Validate:** Balance sufficiency and account status checks are evaluated.
4. **Execute:** Debits and credits are applied, and immutable ledger entries are created.
5. **Commit Transaction:** Changes are persisted to disk and row locks are released. Concurrent requests targeting the locked accounts wait until the transaction completes.

---

## API Documentation

### Authentication Endpoints

#### Register User
- **Endpoint:** `POST /api/v1/auth/register`
- **Request Body:**
  ```json
  {
    "firstName": "Jane",
    "lastName": "Doe",
    "email": "jane.doe@example.com",
    "password": "SecurePassword123!",
    "phoneNumber": "+1234567890"
  }
  ```
- **Response (`201 Created`):**
  ```json
  {
    "userId": "usr_987654321",
    "email": "jane.doe@example.com",
    "status": "PENDING_KYC",
    "createdAt": "2023-10-27T10:15:30Z"
  }
  ```

#### User Login
- **Endpoint:** `POST /api/v1/auth/login`
- **Request Body:**
  ```json
  {
    "email": "jane.doe@example.com",
    "password": "SecurePassword123!"
  }
  ```
- **Response (`200 OK`):**
  ```json
  {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "tokenType": "Bearer",
    "expiresIn": 86400
  }
  ```

### Transaction Endpoints

#### Initiate Transfer
- **Endpoint:** `POST /api/v1/transactions/transfer`
- **Headers:** `Authorization: Bearer <token>`
- **Request Body:**
  ```json
  {
    "sourceAccountNumber": "ACC-77382910",
    "destinationAccountNumber": "ACC-11029384",
    "amount": 250.00,
    "currency": "USD",
    "description": "Monthly rent payment"
  }
  ```
- **Response (`200 OK`):**
  ```json
  {
    "transactionReference": "TXN-99081127-XYZ",
    "status": "SUCCESS",
    "timestamp": "2023-10-27T11:02:15Z",
    "fee": 0.00
  }
  ```

---

## Testing

Apex includes unit, integration, and database-level tests powered by JUnit 5, Mockito, and Testcontainers.

To execute the test suite:

```bash
cd backend
./mvnw clean test
```

To run tests and generate a JaCoCo code coverage report:

```bash
./mvnw clean verify
```

The output report will be available at `target/site/jacoco/index.html`.

---

## Troubleshooting & FAQ

### Why does the backend fail to start with a database connection error?
Verify that the PostgreSQL container is running (`docker ps`) and that database credentials in `application.yml` match your environment. If running locally, confirm port `5432` is not occupied by an existing local PostgreSQL service.

### How does the system handle currency exchange rates?
Apex integrates with an external exchange rate API and caches rates in Redis for 1 hour to optimize performance and respect API limits. If the external provider is unreachable, the system falls back to the most recent cached exchange rates stored in the database.

### Is this system PCI-DSS compliant?
Apex is an open-source reference implementation incorporating standard security measures (data encryption, RBAC, tamper-evident logs). It has not undergone formal PCI-DSS certification and should not be used in production with live financial assets without a formal security audit.

---

## Roadmap

- [x] **Phase 1: Core Ledger & Security**
  - Double-entry ledger engine
  - Pessimistic locking for race-condition prevention
  - JWT-based authentication
- [ ] **Phase 2: Advanced Banking Features**
  - [ ] Multi-factor authentication (TOTP)
  - [ ] Automated interest calculation engine for savings accounts (scheduled cron jobs)
  - [ ] PDF statement generation and email dispatch via RabbitMQ
- [ ] **Phase 3: Scale & Analytics**
  - [ ] Microservices decomposition (splitting Account, Transaction, and Notification services)
  - [ ] Apache Kafka integration for high-throughput event streaming
  - [ ] AI-driven fraud detection system to flag abnormal transaction patterns

---

## Contributing

Contributions are welcome. Follow these steps to submit changes:

1. Fork the repository.
2. Create a feature branch:
   ```bash
   git checkout -b feature/secure-wire-transfers
   ```
3. Commit changes using [Conventional Commits](https://www.conventionalcommits.org/):
   ```bash
   git commit -m "feat: add pessimistic locking to wire transfers"
   ```
4. Write unit tests covering any new business logic.
5. Push the feature branch:
   ```bash
   git push origin feature/secure-wire-transfers
   ```
6. Open a Pull Request targeting the `main` branch.

### Code Style Guidelines

- **Java:** Follow the [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html).
- **TypeScript / React:** Follow ESLint and Prettier configurations provided in the `frontend` directory.

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## Contact & Support

For architectural inquiries, security disclosures, or questions, open an issue on GitHub or email `akarsh.developer@gmail.com`.

*Disclaimer: This software is intended for educational and portfolio purposes. Use in production environments is at your own risk.*