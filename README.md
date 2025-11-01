# NearU - Backend API (ASP.NET Core)

This repository contains the backend source code for **NearU**, the University Lifestyle Hub for Sabaragamuwa University. This API is built with ASP.NET Core and is designed to serve as the secure, scalable, and high-performance backbone for the React web application and the future React Native mobile app.

This document provides instructions for setting up, running, and understanding the backend system.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Features (Backend Perspective)](#features-backend-perspective)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation & Setup](#installation--setup)
  - [Running the Application](#running-the-application)
- [API Endpoints & Documentation](#api-endpoints--documentation)
- [Security](#security)
- [Deployment](#deployment)

---

## Project Overview

The NearU backend is a cloud-native RESTful API built on the .NET platform. It handles all business logic, data persistence, and authentication for the NearU ecosystem. Its responsibilities include managing user accounts, service provider listings, job postings, delivery requests, and administrative moderation, all while serving data securely to client applications.

For a complete overview of the project's functional and non-functional requirements, please refer to the **Software Requirements Specification (SRS)**.

---

## Features (Backend Perspective)

- **Secure Authentication**: JWT-based authentication (Register, Login, Password Hashing).
- **Role-Based Access Control (RBAC)**: Differentiates permissions for Students, Service Providers, Riders, and Admins.
- **RESTful API**: Clean, well-structured endpoints for all data entities.
- **User Management**: Handles user profiles, roles, and preferences.
- **Service Listing Management**: Full CRUD operations for service provider profiles, including image/file uploads to Azure Blob Storage.
- **Review & Rating System**: Logic for submitting, calculating, and retrieving reviews.
- **Job Board Module**: API for posting, searching, and applying for jobs.
- **On-Demand Delivery Module**: Endpoints to manage the lifecycle of a delivery order.
- **Package Management Module**: Logic for creating and booking custom event/travel packages.
- **Admin & Moderation**: Secure endpoints for admins to verify providers, moderate content, and view system analytics.

---

## Technology Stack

| Component       | Technology                          | Rationale                                                                 |
|-----------------|-------------------------------------|---------------------------------------------------------------------------|
| Framework       | ASP.NET Core 7.0+                   | High-performance, cross-platform, modern .NET framework. (CO-1)          |
| Database        | Azure SQL Database                  | Fully managed, scalable, and reliable relational database. (OE-2)        |
| ORM             | Entity Framework Core (EF Core)     | Modern ORM for seamless data access. (SI-3)                               |
| Authentication  | JWT (JSON Web Tokens)               | Standard for stateless, secure API authentication.                        |
| File Storage    | Azure Blob Storage                  | Scalable object storage for user/service images and files. (OE-2)        |
| Cloud Hosting   | Microsoft Azure App Service         | PaaS for easy, scalable deployment. (CO-3)                                |
| API Docs        | Swagger (OpenAPI)                   | Automatically generated, interactive API documentation. (MA-2)            |

---

## Project Structure

This project follows a clean, **N-Tier architecture** to ensure a strong separation of concerns, maintainability, and testability.

```
/NearU.Backend
│
├── 📁 NearU.API/                 # Main project - Controllers, Program.cs, Middleware
│   ├── Controllers/             # API endpoints (e.g., AuthController.cs, ServicesController.cs)
│   ├── Middleware/              # Custom middleware (e.g., ErrorHandlingMiddleware.cs)
│   ├── appsettings.json         # Configuration
│   └── Program.cs               # Service registration and app pipeline
│
├── 📁 NearU.Application/         # Business logic - Services, DTOs, Interfaces
│   ├── DTOs/                    # Data Transfer Objects (e.g., UserLoginDto.cs, ServiceDto.cs)
│   ├── Services/                # Core business logic (e.g., AuthService.cs, IServiceService.cs)
│   └── Mappers/                 # AutoMapper profiles
│
├── 📁 NearU.Domain/              # Core entities and enums
│   ├── Entities/                # POCO classes (e.g., User.cs, ServiceProvider.cs, Review.cs)
│   └── Enums/                   # (e.g., UserRole.cs, OrderStatus.cs)
│
├── 📁 NearU.Infrastructure/      # Data access and external services
│   ├── Data/                    # EF Core DbContext and Migrations
│   ├── Repositories/            # Repository pattern implementation (e.g., UserRepository.cs)
│   └── Services/                # External service clients (e.g., AzureBlobStorageService.cs)
│
└──  solution.sln               # Visual Studio Solution File
```

---

## Getting Started

### Prerequisites

- **.NET 7.0 (or newer) SDK**
- **Visual Studio 2022** or **VS Code**
- **SQL Server** (LocalDB, Express, or Azure SQL Edge)
- **Azure Storage Emulator (Azurite)** or an Azure Storage Account.

### Installation & Setup

1. **Clone the repository:**

```bash
git clone https://github.com/your-username/nearu-backend.git
cd nearu-backend
```

2. **Configure local secrets:**

This project uses `appsettings.Development.json` for local development keys. **Do not commit sensitive keys to source control.**

Open `NearU.API/appsettings.Development.json` and provide your local configuration:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=NearU_Dev;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
  "Storage": {
    "ConnectionString": "UseDevelopmentStorage=true" // For Azurite
    // Or "DefaultEndpointsProtocol=https;AccountName=...;AccountKey=...;EndpointSuffix=core.windows.net"
  },
  "JWT": {
    "Key": "YOUR_SUPER_SECRET_JWT_SIGNING_KEY_GOES_HERE_REPLACE_ME",
    "Issuer": "https://localhost:7001", // Your local API address
    "Audience": "https://localhost:3000" // Your local React app address
  }
}
```

3. **Apply Database Migrations:**

This will create the database and tables based on the models in `NearU.Domain`.

```bash
cd NearU.Infrastructure/Data
dotnet ef database update
```

### Running the Application

You can run the API directly from **Visual Studio 2022** by pressing `F5`, or from the command line:

```bash
cd NearU.API
dotnet watch run
```

The API will start, typically on `https://localhost:7001` (or a similar port). The Swagger UI will be available at `/swagger` for testing all endpoints.

---

## API Endpoints & Documentation

This API uses **Swagger (OpenAPI)** for interactive documentation. Once the application is running, navigate to:

```
https://localhost:7001/swagger
```

This UI allows you to see all available endpoints, their required parameters, and test them directly from your browser.

### Main Resource Endpoints

- `/api/auth` (Register, Login)
- `/api/users` (Profile management)
- `/api/services` (Service listings, Search, Categories)
- `/api/reviews` (Submit/Get reviews for a service)
- `/api/jobs` (Job board listings, Applications)
- `/api/delivery` (Delivery order management)
- `/api/packages` (Package booking)
- `/api/admin` (Verification, Moderation)

---

## Security

This API implements security best practices as defined in the SRS (Section 6.2).

- **(SR-1) Password Hashing**: All user passwords are hashed using a strong, one-way algorithm (e.g., PBKDF2).
- **(SR-2) Role-Based Access Control (RBAC)**: Endpoints are protected using `[Authorize]` attributes, often with specific roles (e.g., `[Authorize(Roles = "Admin")]`).
- **(SR-3) Vulnerability Protection**:
  - **SQL Injection**: Prevented by using Entity Framework Core's parameterized queries.
  - **XSS/CSRF**: ASP.NET Core has built-in protections. Input is validated using DTOs.
  - **HTTPS**: Enforced by default (CO-5).

---

## Deployment

This application is designed for deployment on **Microsoft Azure App Service** (CO-3).

A **CI/CD (Continuous Integration / Continuous Deployment)** pipeline will be set up using **GitHub Actions** or **Azure DevOps**. The pipeline will:

1. Trigger on push to the `main` branch.
2. Build the .NET project.
3. Run any automated tests.
4. Publish the build artifacts.
5. Deploy the artifacts to the Azure App Service slot.

---

**© 2024 NearU Development Team - Sabaragamuwa University**