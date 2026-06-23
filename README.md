# 🔐 SecureFileX

A secure, web-based file management and encryption platform built with **Spring Boot**, **Java**, **MySQL**, and **Thymeleaf**. SecureFileX provides AES-256-GCM encrypted file storage with role-based access control and comprehensive audit logging.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Configuration](#configuration)
  - [Running the Application](#running-the-application)
- [Usage](#usage)
  - [User Guide](#user-guide)
  - [Admin Guide](#admin-guide)
- [Project Structure](#project-structure)
- [Database Schema](#database-schema)
- [API Endpoints](#api-endpoints)
- [Security](#security)
- [Testing](#testing)
- [Known Limitations](#known-limitations)
- [Future Enhancements](#future-enhancements)
- [Team](#team)
- [License](#license)

---

## Overview

SecureFileX addresses the growing need for secure digital file management by integrating modern cryptography with a clean, role-based web application. Files are automatically encrypted on upload using AES-256 in Galois/Counter Mode (GCM) and decrypted on demand — ensuring that even a compromised server cannot expose plaintext data.

---

## ✨ Features

### User Features
- Secure login with BCrypt-hashed credentials
- Upload files — automatically encrypted with AES-256-GCM
- Download files — decrypted in real time, only accessible to the owner
- Personal dashboard showing upload history with timestamps
- Retry logic with cooldown period on repeated upload failures

### Admin Features
- View all files uploaded by any user
- Search user file activity by username
- Download any user's file (with admin privileges)
- Generate and export user activity reports as `.csv`
- Configure platform settings:
  - Maximum file size (MB)
  - Allowed file types (e.g., `application/pdf`, `image/png`)
  - Maximum upload retry count
  - Block duration after exceeded retries
  - File verifier rules (e.g., block `.exe` files, set depth for verification)

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| Backend Framework | Spring Boot 3.x |
| Language | Java 17 / Java 25 |
| Frontend | Thymeleaf, HTML5, CSS3 |
| Database | MySQL 8.0 |
| Security | Spring Security, BCrypt |
| Encryption | AES-256-GCM |
| Build Tool | Maven |
| Server | Embedded Apache Tomcat |
| IDE | Visual Studio Code |
| API Testing | Postman |
| DB Management | XAMPP / phpMyAdmin |

---

## 🏗 Architecture

SecureFileX follows the **MVC (Model-View-Controller)** three-tier architecture:

```
┌────────────────────────────────────────────┐
│           Presentation Layer               │
│     (Thymeleaf HTML Templates)             │
└──────────────────┬─────────────────────────┘
                   │
┌──────────────────▼─────────────────────────┐
│           Controller Layer                 │
│  FileController | AdminController |        │
│  AuthController                            │
└──────────────────┬─────────────────────────┘
                   │
┌──────────────────▼─────────────────────────┐
│            Service Layer                   │
│  FileService (AES-256-GCM) |               │
│  AuthService (BCrypt) | AuditLogService    │
└──────────────────┬─────────────────────────┘
                   │
┌──────────────────▼─────────────────────────┐
│          Repository Layer                  │
│  UserRepository | FileRecordRepository |   │
│  AuditLogRepository                        │
└──────────────────┬─────────────────────────┘
                   │
┌──────────────────▼─────────────────────────┐
│            MySQL Database                  │
└────────────────────────────────────────────┘
```

---

## 🚀 Getting Started

### Prerequisites

Ensure the following are installed on your system:

- **Java JDK 17+** (or JDK 25 as used in this project)
- **Maven 3.8+**
- **MySQL 8.0**
- **XAMPP** (recommended for local MySQL + phpMyAdmin) or standalone MySQL
- **Visual Studio Code** with Java Extension Pack (or any Java IDE)
- **Git**

---

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-username/SecureFileX.git
   cd SecureFileX
   ```

2. **Create the MySQL database**

   Open phpMyAdmin or MySQL CLI and run:
   ```sql
   CREATE DATABASE securefilex;
   ```

3. **Install dependencies**
   ```bash
   mvn clean install
   ```

---

### Configuration

Edit `src/main/resources/application.properties`:

```properties
# Database Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/securefilex
spring.datasource.username=root
spring.datasource.password=your_password_here
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA / Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=false

# File Upload
spring.servlet.multipart.max-file-size=50MB
spring.servlet.multipart.max-request-size=50MB

# AES-256 Encryption Key (Base64-encoded 256-bit key)
app.file.encryption.key=YOUR_BASE64_ENCODED_256BIT_AES_KEY_HERE

# File Storage Path
app.file.upload-dir=uploads/
```

#### Generating an AES-256 Key

Run this one-liner to generate a secure key:

```bash
openssl rand -base64 32
```

Paste the output as the value of `app.file.encryption.key`.

> ⚠️ **Never commit your real AES key or database password to version control.** Use environment variables or a secrets manager in production.

---

### Running the Application

```bash
mvn spring-boot:run
```

The application starts on **http://localhost:8080** by default.

---

## 📖 Usage

### User Guide

1. Navigate to `http://localhost:8080/login`
2. Register a new account at `/register` (Role: User)
3. Log in to access your personal **Dashboard** (`/udashboard`)
4. **Upload a file** — drag and drop or click to browse; the file is encrypted automatically
5. **Download a file** — click the Download button next to any of your files
6. Log out securely when done

### Admin Guide

1. Log in with admin credentials → redirected to `/admin/dashboard`
2. Switch between **Settings** and **Monitoring** tabs
3. Under **Monitoring**, search a username to see all their uploaded files
4. Click **Generate Report** to export activity as a `.csv`
5. Under **Settings**, configure:
   - Max file size
   - Allowed file types
   - Max upload retries & block duration
   - File verifier rules (block executables, etc.)

---

## 📁 Project Structure

```
SecureFileX/
├── src/
│   └── main/
│       ├── java/com/securefilex/
│       │   ├── controller/
│       │   │   ├── FileController.java
│       │   │   ├── AdminController.java
│       │   │   ├── AuthController.java
│       │   │   └── DashboardController.java
│       │   ├── service/
│       │   │   ├── FileService.java          # AES-256-GCM encryption/decryption
│       │   │   ├── AuthService.java          # BCrypt auth
│       │   │   ├── AuditLogService.java
│       │   │   ├── AdminService.java
│       │   │   └── FileVerifierService.java
│       │   ├── repository/
│       │   │   ├── UserRepository.java
│       │   │   ├── FileRecordRepository.java
│       │   │   └── AuditLogRepository.java
│       │   ├── model/
│       │   │   ├── User.java
│       │   │   ├── FileRecord.java
│       │   │   └── AuditLog.java
│       │   └── config/
│       │       ├── SecurityConfig.java
│       │       └── AdminSettings.java
│       └── resources/
│           ├── templates/
│           │   ├── login.html
│           │   ├── register.html
│           │   ├── userdashboard.html
│           │   ├── dashboard.html            # Admin dashboard
│           │   └── upload-error.html
│           ├── static/
│           │   └── css/
│           └── application.properties
├── uploads/                                  # Encrypted files stored here
├── pom.xml
└── README.md


