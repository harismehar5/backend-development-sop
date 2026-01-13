# Backend Engineering SOP (NestJS & Prisma)

This document outlines the standards for building scalable, maintainable, and secure server-side applications using the NestJS framework and Prisma ORM.

## Table of Contents

1. [Directory Structure & Module Isolation](#1-directory-structure--module-isolation)
2. [Naming Conventions & File Standards](#2-naming-conventions--file-standards)
3. [Data Transfer Objects (DTOs) & Validation](#3-data-transfer-objects-dtos--validations)
4. [Service Layer & Business Logic](#4-service-layer--business-logic)
5. [Database Management (Prisma)](#5-database-management-prisma)
6. [Error Handling & Logging](#6-error-handling--logging)
7. [Security & Authentication](#7-security--authentication)

---

### 1. Directory Structure & Module Isolation

* **Domain-Driven Modules:** Every feature (e.g., `Users`, `Payments`) must reside in its own module folder containing its controller, service, and DTOs.
* **Common Shared Module:** Logic used by 3 or more modules (e.g., Date formatters, common types) must be placed in `src/common`.
* **Configuration:** Use a dedicated `ConfigModule` to wrap `@nestjs/config` for type-safe environment variable access.

### 2. Naming Conventions & File Standards

* **File Naming:** Use `kebab-case` for all filenames with a descriptive suffix (e.g., `user-profile.service.ts`).
* **Class Naming:** Use `PascalCase` for all class names (e.g., `ExportDataService`).
* **Variable/Method Naming:** Use `camelCase` for variables and function names.
* **Constants:** Use `UPPER_SNAKE_CASE` for global constants or configuration keys.

### 3. Data Transfer Objects (DTOs) & Validation

* **Input Integrity:** Every POST/PUT/PATCH request must have a defined DTO class.
* **Decorator Validation:** Use `class-validator` (e.g., `@IsEmail()`, `@IsString()`) to enforce data constraints before they reach the controller.
* **Response Masking:** Never return the raw database object; use a response DTO or `@Exclude()` to hide sensitive fields like `password` or `internal_notes`.

### 4. Service Layer & Business Logic

* **Thin Controllers, Fat Services:** Controllers should only handle routing and status codes; 100% of business logic stays in Services.
* **Dependency Injection:** Always use Constructor Injection to keep services testable and decoupled.
* **Single Responsibility:** If a service method exceeds 50 lines, evaluate if it needs to be broken down into smaller helper functions.

### 5. Database Management (Prisma)

* **Schema Consistency:** Use `@map("database_column_name")` in Prisma to keep TS code `camelCase` and DB columns `snake_case`.
* **Transaction Safety:** Use `this.prisma.$transaction([...])` for operations involving multiple table writes to prevent data corruption.
* **Indices:** Every field used in a `.findUnique` or `.findMany` filter must be indexed in the schema.

### 6. Error Handling & Logging

* **Standardized Exceptions:** Use built-in NestJS exceptions (e.g., `NotFoundException`) rather than generic `Error` objects.
* **Global Interceptors:** Implement a global `LoggingInterceptor` to track the execution time and status of every incoming request.
* **Production Logs:** Use a structured logger like `Pino` to output JSON logs for easy parsing by ELK or CloudWatch.

### 7. Security & Authentication

* **Guards:** Protect routes using `@UseGuards(JwtAuthGuard)` at the class level rather than individual methods where possible.
* **Password Hashing:** Always use `bcrypt` with a salt round of 10-12; never store plain text or MD5 hashes.
* **Rate Limiting:** Implement `@nestjs/throttler` on public endpoints (Login/Register) to prevent brute-force attacks.

---
