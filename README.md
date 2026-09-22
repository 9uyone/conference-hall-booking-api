# Conference Hall Booking API

![.NET 10](https://img.shields.io/badge/.NET-10.0-512BD4?style=flat-square&logo=dotnet)
![C#](https://img.shields.io/badge/C%23-14-239120?style=flat-square&logo=csharp)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=flat-square&logo=postgresql)
![Tests](https://img.shields.io/badge/Tests-xUnit%20%7C%20FluentAssertions-blue?style=flat-square)

RESTful API for conference hall booking featuring dynamic pricing calculation, slot collision validation, and foundational business analytics. Built with Minimal APIs, EF Core, and PostgreSQL adhering to Clean Code and SOLID principles.

---

## 🛠 Tech Stack

* **Platform:** .NET 10, C# 14 (Minimal APIs)
* **Data Access:** EF Core, PostgreSQL (`Npgsql`)
* **Validation & Testing:** FluentValidation, xUnit, FluentAssertions
* **Documentation:** Swagger / OpenAPI

---

## 🚀 Key Features

* **Hall Management:** CRUD operations with cascade-delete protection using `DeleteBehavior.Restrict`.
* **Search & Reservation:** Filter available halls by date, time window, and required capacity. Slot availability checks translate directly into optimized SQL (`NOT EXISTS`) without in-memory evaluation.
* **Business Analytics (`/api/reports`):**
  * `GET /api/reports/revenue` — Period revenue breakdown (room rental vs. extra services).
  * `GET /api/reports/occupancy` — Hall utilization rate (% of booked hours within the operating window of 06:00–23:00).

---

## 💰 Dynamic Pricing Rules

Hourly rate adjustments based on time slots:

| Time Slot | Rate Multiplier | Description |
|---|---|---|
| **06:00 – 09:00** | `0.90x` | Early morning discount (-10%) |
| **09:00 – 12:00, 14:00 – 18:00** | `1.00x` | Standard daytime rate |
| **12:00 – 14:00** | `1.15x` | Peak hours surcharge (+15%) |
| **18:00 – 23:00** | `0.80x` | Evening discount (-20%) |

*Additional requested services (projector, sound, Wi-Fi) are added as flat-rate charges on top of the calculated room rental.*

---

## 📦 Seed Data

Pre-populated automatically on application startup:

| Hall | Capacity | Base Rate | Available Services |
|---|---|---|---|
| **Hall A** | 50 seats | 2,000 UAH/h | Projector (500 UAH), Wi-Fi (300 UAH), Sound (700 UAH) |
| **Hall B** | 100 seats | 3,500 UAH/h | Wi-Fi (300 UAH) |
| **Hall C** | 30 seats | 1,500 UAH/h | Sound (700 UAH) |

---

## 🏗 Architecture & Implementation Notes

* **Authorization Scope:** CRUD operations are currently open to facilitate evaluation and local testing. In a production environment, management endpoints are restricted via role-based access control (e.g., `[Authorize(Roles = "Admin")]`).
* **Connection String:** Default credentials are kept in `appsettings.json` strictly for local testing convenience. Production setups should inject configurations via Environment Variables or a Secret Manager.
* **Time Handling:** All timestamps are strictly converted and stored in UTC.
* **Resilience & Security:** Global exception handling is configured using `IExceptionHandler`, returning RFC 7807 compliant `ProblemDetails` to prevent leaking internal stack traces.

---

## ⚡ Quick Start

1. Run the database:
```bash
docker compose up -d
```

2. Run the application:
```bash
dotnet run
```
*(Database migrations and seed data apply automatically on startup).*

3. Open Swagger UI:
```text
https://localhost:7267/swagger/index.html
```

4. Run unit tests:
```bash
dotnet test
```