# Authentication API — Express.js + MySQL

A standalone **Authentication API** built with **Express.js** and **MySQL**, implementing secure authentication using **JWT (JSON Web Tokens)**, **bcrypt password hashing**, and **middleware for route protection**.

This API supports:

* **Signup** (store bcrypt-hashed passwords)
* **Login** (verify credentials, issue JWT)
* **Logout** (token revocation via DB)
* **Protected Profile Endpoint** (JWT required)

---

## 🚀 Project Overview

The project demonstrates:

* Secure user authentication workflows.
* Use of **bcrypt** for password hashing (never storing plain text).
* Use of **JWT with unique jti** for token verification and revocation.
* Organized code into **controllers, routes, middleware, and config**.
* Testing with **Postman** for both success and error cases.

---

## ⚙️ Setup Instructions

### 1. Clone and Install

```bash
git clone <your_repo_url>
cd lab-auth-api
npm install
```

### 2. Database Setup

Create a MySQL database `lab_auth` with the following tables:

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  full_name VARCHAR(255),
  role VARCHAR(50) DEFAULT 'student',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE revoked_tokens (
  id INT AUTO_INCREMENT PRIMARY KEY,
  jti VARCHAR(32) NOT NULL UNIQUE,
  expires_at DATETIME NOT NULL
);
```

### 3. Environment Variables

Create a `.env` file in the root directory:

```ini
DB_HOST=localhost
DB_USER=root
DB_PASS=
DB_NAME=lab_auth
DB_PORT=3306
SERVER_PORT=3000

JWT_SECRET=replace_with_a_long_random_string
JWT_EXPIRES=1h
```

### 4. Run Server

```bash
# development mode
npm run dev

# production mode
npm start
```

Check health:

```
GET http://localhost:3000/api/health
```

Expected response:

```json
{
  "status": "ok",
  "db": "connected",
  "time": "2025-09-11T09:45:00.123Z"
}
```

---

## 📌 API Endpoints

Base URL: `http://localhost:3000/api`

### Public Routes

* **POST** `/auth/signup`
  Register a new user.
  **Body:**

  ```json
  {
    "email": "user@example.com",
    "password": "Pass@1234",
    "full_name": "User One",
    "role": "student"
  }
  ```

* **POST** `/auth/login`
  Authenticate user and receive JWT.
  **Body:**

  ```json
  {
    "email": "user@example.com",
    "password": "Pass@1234"
  }
  ```

  **Response:**

  ```json
  {
    "message": "login successful",
    "token": "<jwt_token>",
    "profile": { "id": 1, "email": "user@example.com", "role": "student" }
  }
  ```

### Protected Routes (JWT Required)

Set Header:

```
Authorization: Bearer <jwt_token>
```

* **GET** `/profile`
  Get authenticated user profile.

* **POST** `/auth/logout`
  Revoke current token. After logout, the same token cannot be used again.

---

## 🧪 Testing with Postman

1. **Signup** → `POST /auth/signup`
2. **Login** → `POST /auth/login` (save token to environment variable `{{token}}`)
3. **Profile** → `GET /profile` with `Authorization: Bearer {{token}}`
4. **Logout** → `POST /auth/logout` with token
5. **Negative Cases**:

   * Duplicate signup → `409 Conflict`
   * Wrong password → `401 Unauthorized`
   * No token on `/profile` → `401 Unauthorized`
   * Revoked/expired token → `401 Unauthorized`

---

## 🏗 Project Structure

```
lab-auth-api/
├─ controllers/
│  └─ authController.js
├─ routes/
│  └─ authRoutes.js
├─ middleware/
│  └─ auth.js
├─ config/
│  └─ db.js
├─ .env
├─ .gitignore
└─ server.js
```

---

## 📖 Notes

* Store your **MySQL credentials and JWT secret in `.env` only**.
* Always hash passwords with **bcrypt**.
* Tokens are revoked via the `revoked_tokens` table for logout safety.
* Test thoroughly with **Postman** before submission.

---

## ✅ Objectives Achieved

* Implemented secure authentication API with Express.js and MySQL.
* JWT signing, verification, expiration, and revocation.
* Password hashing with bcrypt.
* Organized code into modules with middleware protection.
* Successfully tested with Postman.
