# 🚀 Laravel API + Next.js + MySQL + Docker Setup

## 📦 Tech Stack

* ⚙️ Laravel 12 (REST API)
* ⚛️ Next.js 16 (Frontend)
* 🐬 MySQL 8
* 🐳 Docker & Docker Compose
* 🔐 Laravel Sanctum Authentication
* 🎨 Tailwind CSS

---

# 🏗️ Project Structure

```text
fullstack-app/
│
├── backend/                 # Laravel API
│   ├── app/
│   ├── routes/
│   ├── database/
│   ├── Dockerfile
│   └── .env
│
├── frontend/                # Next.js App
│   ├── src/
│   ├── public/
│   ├── Dockerfile
│   └── package.json
│
└── docker-compose.yml
```

---

# 🔄 System Architecture

```text
┌─────────────┐
│   Browser   │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Next.js    │
│  Port 3000  │
└──────┬──────┘
       │ API Request
       ▼
┌─────────────┐
│ Laravel API │
│ Port 8000   │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   MySQL     │
│ Port 3306   │
└─────────────┘
```

---

# 🐳 Docker Compose

Create `docker-compose.yml`

```yaml
services:

  backend:
    build:
      context: ./backend
    container_name: laravel_api
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/var/www
    depends_on:
      - mysql

  frontend:
    build:
      context: ./frontend
    container_name: nextjs_app
    ports:
      - "3000:3000"
    volumes:
      - ./frontend:/app
      - /app/node_modules
    depends_on:
      - backend

  mysql:
    image: mysql:8.4
    container_name: mysql_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: laravel_api
      MYSQL_USER: laravel
      MYSQL_PASSWORD: secret
    ports:
      - "3306:3306"
```

---

# ⚙️ Backend Setup (Laravel)

## Create Laravel Project

```bash
composer create-project laravel/laravel backend
```

## Install Sanctum

```bash
composer require laravel/sanctum
```

## Publish Sanctum

```bash
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

## Run Migration

```bash
php artisan migrate
```

---

# 🗄️ Database Configuration

Update `.env`

```env
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=laravel_api
DB_USERNAME=laravel
DB_PASSWORD=secret
```

---

# 👤 User Model

`app/Models/User.php`

```php
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens;
}
```

---

# 🔐 Authentication Routes

`routes/api.php`

```php
use App\Http\Controllers\Api\AuthController;

Route::post('/register', [AuthController::class, 'register']);
Route::post('/login', [AuthController::class, 'login']);

Route::middleware('auth:sanctum')->group(function () {

    Route::get('/me', [AuthController::class, 'me']);

    Route::post('/logout', [AuthController::class, 'logout']);

});
```

---

# ⚛️ Frontend Setup (Next.js)

## Create Project

```bash
npx create-next-app@latest frontend --typescript
```

## Start Development Server

```bash
npm run dev
```

Frontend URL:

```text
http://localhost:3000
```

---

# 🌐 API Helper

`src/lib/api.ts`

```ts
const API_URL = "http://localhost:8000/api";

export async function login(
  email: string,
  password: string
) {
  const response = await fetch(
    `${API_URL}/login`,
    {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        email,
        password,
      }),
    }
  );

  return response.json();
}
```

---

# 🔑 Login Flow

```text
User Login
     │
     ▼
Next.js Form
     │
     ▼
POST /api/login
     │
     ▼
Laravel Validate
     │
     ▼
Generate Sanctum Token
     │
     ▼
Return Token
     │
     ▼
Store Token
     │
     ▼
Protected API Access
```

---

# 🧪 API Testing

## Register

```http
POST /api/register
```

```json
{
  "name": "Tola",
  "email": "tola@example.com",
  "password": "password123"
}
```

---

## Login

```http
POST /api/login
```

```json
{
  "email": "tola@example.com",
  "password": "password123"
}
```

---

## Get Current User

```http
GET /api/me
```

Header:

```text
Authorization: Bearer TOKEN
```

---

## Logout

```http
POST /api/logout
```

Header:

```text
Authorization: Bearer TOKEN
```

---

# 🚀 Run Everything

Build Containers

```bash
docker compose up -d --build
```

Check Containers

```bash
docker ps
```

Stop Containers

```bash
docker compose down
```

---

# 📚 Recommended Production Stack

```text
Next.js
   │
   ▼
Nginx
   │
   ▼
Laravel API
   │
   ├── MySQL
   ├── Redis
   └── Queue Worker
```

## Production Features

* ✅ Docker
* ✅ Laravel Sanctum
* ✅ Redis Cache
* ✅ Queue Workers
* ✅ Nginx Reverse Proxy
* ✅ CI/CD Pipeline
* ✅ HTTPS SSL
* ✅ Environment Variables
* ✅ Database Backups

---

# 🎯 Development Commands

```bash
# Start Containers
docker compose up -d

# Stop Containers
docker compose down

# Laravel Migration
docker compose exec backend php artisan migrate

# Laravel Tinker
docker compose exec backend php artisan tinker

# Composer Install
docker compose exec backend composer install

# NPM Install
docker compose exec frontend npm install

# Next.js Dev Server
docker compose exec frontend npm run dev
```
