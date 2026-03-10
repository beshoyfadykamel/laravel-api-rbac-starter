# Laravel 12 API — Authentication & Authorization System

A production-ready **RESTful API** built with **Laravel 12** that provides a complete authentication and role-based access control (RBAC) system. This project serves as a standard, scalable foundation for any application requiring secure user management, token-based authentication, and granular permission control.

> **Author:** Beshoy Fady  
> **GitHub:** [github.com/beshoyfadykamel](https://github.com/beshoyfadykamel)

---

## Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Features](#features)
- [Project Structure](#project-structure)
- [Installation & Setup](#installation--setup)
- [Database Schema](#database-schema)
- [Roles & Permissions](#roles--permissions)
- [API Endpoints](#api-endpoints)
    - [Authentication](#authentication-endpoints)
    - [Profile](#profile-endpoints)
    - [Admin — Users](#admin-users-endpoints)
    - [Admin — Roles](#admin-roles-endpoints)
    - [Admin — Permissions](#admin-permissions-endpoints)
- [Request Validation](#request-validation)
- [API Response Format](#api-response-format)
- [API Resources](#api-resources)
- [Policies & Authorization Logic](#policies--authorization-logic)
- [Middleware](#middleware)
- [Notifications](#notifications)
- [Error Handling](#error-handling)
- [Authentication Flow](#authentication-flow)
- [Postman Collection](#postman-collection)
- [Running Tests](#running-tests)
- [Author](#author)

---

## Overview

This project is a **standard Laravel 12 API** that implements:

- **Token-based authentication** using Laravel Sanctum (register, login, logout, password reset, email verification)
- **Role-Based Access Control (RBAC)** using Spatie Laravel Permission with 3 system roles and 15 granular permissions
- **Full user management** (CRUD, soft deletes, restore, force delete, role/permission assignment)
- **Role & permission management** (create custom roles, assign permissions)
- **Policy-based authorization** with guard abilities to prevent privilege escalation
- **Queued email notifications** for email verification and password reset
- **Standardized JSON API responses** with consistent error handling
- **Advanced filtering, search, sorting, and pagination** on user listings

---

## Tech Stack

| Technology                    | Purpose                        |
| ----------------------------- | ------------------------------ |
| **Laravel 12**                | PHP Framework                  |
| **Laravel Sanctum**           | Token-based API Authentication |
| **Spatie Laravel Permission** | Role & Permission Management   |
| **MySQL**                     | Database                       |
| **Laravel Queues**            | Async Email Notifications      |
| **PHP 8.2+**                  | Runtime                        |

---

## Features

- Token-based authentication with 30-day token expiry
- Email verification (queued)
- Password reset flow (queued)
- User activation/deactivation (status management)
- Soft deletes with restore & force delete
- Role assignment (single role per user)
- Direct permission assignment to users
- System roles protection (cannot modify/delete `super_admin`, `admin`, `user`)
- Self-action prevention (cannot delete/modify own account via admin endpoints)
- Super admin protection (non-super-admin users cannot modify super admins)
- Standardized JSON responses across all endpoints
- Rate limiting (throttle) on all endpoints
- Force JSON response middleware
- Advanced user filtering: status, email verified, search, date range, sorting, pagination

---

## Project Structure

```
app/
├── Http/
│   ├── Controllers/
│   │   └── Api/
│   │       ├── Admin/
│   │       │   ├── PermissionController.php   # List permissions
│   │       │   ├── RoleController.php         # CRUD roles
│   │       │   └── UserController.php         # Full user management
│   │       ├── Auth/
│   │       │   ├── EmailVerificationController.php
│   │       │   ├── ForgotPasswordController.php
│   │       │   ├── LoginController.php
│   │       │   ├── LogoutController.php
│   │       │   └── RegisterController.php
│   │       └── User/
│   │           └── ProfileController.php      # User own profile
│   ├── Middleware/
│   │   ├── CheckUserStatus.php                # Block suspended users
│   │   └── ForceJsonResponse.php              # Force Accept: application/json
│   ├── Requests/
│   │   └── Api/
│   │       ├── Admin/
│   │       │   ├── ChangeRoleRequest.php
│   │       │   ├── GivePermissionsRequest.php
│   │       │   ├── RevokePermissionsRequest.php
│   │       │   ├── StoreRoleRequest.php
│   │       │   ├── StoreUserRequest.php
│   │       │   ├── UpdateRoleRequest.php
│   │       │   ├── UsersFilterRequest.php
│   │       │   └── UsersUpdateRequest.php
│   │       ├── Auth/
│   │       │   ├── ForgotPasswordRequest.php
│   │       │   ├── LoginRequest.php
│   │       │   ├── RegisterRequest.php
│   │       │   └── ResetPasswordRequest.php
│   │       └── User/
│   │           └── ProfileRequest.php
│   └── Resources/
│       └── Api/
│           ├── Admin/
│           │   ├── PermissionResource.php
│           │   ├── RoleResource.php
│           │   └── UsersResource.php
│           ├── Auth/
│           │   └── UserResource.php
│           └── User/
│               └── ProfileResource.php
├── Models/
│   └── User.php
├── Notifications/
│   └── Api/Auth/
│       ├── ResetPasswordQueued.php
│       └── VerifyEmailQueued.php
├── Policies/
│   └── Api/Admin/
│       ├── PermissionPolicy.php
│       ├── RolePolicy.php
│       └── UserPolicy.php
├── Providers/
│   └── AppServiceProvider.php
└── Traits/
    └── Api/
        └── ApiResponse.php

routes/
└── Api/
    ├── admin.php     # Admin routes (users, roles, permissions)
    ├── api.php       # Main API routes (includes auth + admin)
    └── auth.php      # Authentication routes

database/
├── migrations/
│   ├── create_users_table.php
│   ├── create_cache_table.php
│   ├── create_jobs_table.php
│   ├── create_personal_access_tokens_table.php
│   └── create_permission_tables.php
└── seeders/
    ├── DatabaseSeeder.php
    └── RolePermissionSeeder.php
```

---

## Installation & Setup

### Prerequisites

- PHP 8.2+
- Composer
- MySQL
- Node.js & npm

### Steps

```bash
# 1. Clone the repository
git clone https://github.com/beshoyfadykamel/Laravel-12-API-Auth-AuthZ.git
cd Laravel-12-API-Auth-AuthZ

# 2. Install dependencies
composer install
npm install

# 3. Configure environment
cp .env.example .env
php artisan key:generate

# 4. Set up your database in .env
# DB_CONNECTION=mysql
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=your_database
# DB_USERNAME=your_username
# DB_PASSWORD=your_password

# 5. Run migrations and seed roles/permissions
php artisan migrate
php artisan db:seed

# 6. Build frontend assets
npm run build

# 7. Start the development server
php artisan serve
```

Or use the Composer setup script:

```bash
composer setup
```

For development with queue listener and Vite:

```bash
composer dev
```

---

## Database Schema

### Users Table

| Column              | Type         | Details                     |
| ------------------- | ------------ | --------------------------- |
| `id`                | BIGINT       | Primary Key, Auto Increment |
| `name`              | VARCHAR(255) | Required                    |
| `email`             | VARCHAR(255) | Unique                      |
| `status`            | BOOLEAN      | Default: `true`, Indexed    |
| `email_verified_at` | TIMESTAMP    | Nullable, Indexed           |
| `password`          | VARCHAR(255) | Hashed                      |
| `deleted_at`        | TIMESTAMP    | Soft Deletes                |
| `created_at`        | TIMESTAMP    | Auto                        |
| `updated_at`        | TIMESTAMP    | Auto                        |

### Personal Access Tokens Table (Sanctum)

| Column           | Type         | Details             |
| ---------------- | ------------ | ------------------- |
| `id`             | BIGINT       | Primary Key         |
| `tokenable_type` | VARCHAR(255) | Polymorphic type    |
| `tokenable_id`   | BIGINT       | Polymorphic ID      |
| `name`           | TEXT         | Token name          |
| `token`          | VARCHAR(64)  | Unique hashed token |
| `abilities`      | TEXT         | Token abilities     |
| `expires_at`     | TIMESTAMP    | Nullable, Indexed   |

### Spatie Permission Tables

- **`roles`** — `id`, `name` (unique with guard), `guard_name`
- **`permissions`** — `id`, `name` (unique with guard), `guard_name`
- **`model_has_roles`** — Links users to roles
- **`model_has_permissions`** — Links users to direct permissions
- **`role_has_permissions`** — Links roles to permissions

### Other Tables

- **`password_reset_tokens`** — Email-based password reset tokens (60-min expiry)
- **`cache`** / **`cache_locks`** — Application cache
- **`jobs`** / **`job_batches`** / **`failed_jobs`** — Queue system
- **`sessions`** — Session storage

---

## Roles & Permissions

### System Roles

| Role            | Description                               | Permissions                                                          |
| --------------- | ----------------------------------------- | -------------------------------------------------------------------- |
| **super_admin** | Full system access, bypasses all policies | All 15 permissions                                                   |
| **admin**       | Read-only access to admin panel           | `view users`, `view trashed users`, `view roles`, `view permissions` |
| **user**        | Basic authenticated user                  | No admin permissions (profile access only)                           |

> System roles (`super_admin`, `admin`, `user`) cannot be modified or deleted through the API.

### All Permissions

#### User Management (10)

| Permission           | Description                          |
| -------------------- | ------------------------------------ |
| `view users`         | List all users                       |
| `view trashed users` | List soft-deleted users              |
| `create users`       | Create new users                     |
| `update users`       | Update user data                     |
| `delete users`       | Soft delete users                    |
| `restore users`      | Restore soft-deleted users           |
| `force delete users` | Permanently delete users             |
| `change roles`       | Change a user's role                 |
| `give permissions`   | Grant direct permissions to users    |
| `revoke permissions` | Revoke direct permissions from users |

#### Role Management (4)

| Permission     | Description         |
| -------------- | ------------------- |
| `view roles`   | List all roles      |
| `create roles` | Create custom roles |
| `update roles` | Update custom roles |
| `delete roles` | Delete custom roles |

#### Permission Management (1)

| Permission         | Description          |
| ------------------ | -------------------- |
| `view permissions` | List all permissions |

---

## API Endpoints

**Base URL:** `/api`

All admin and profile routes require: `auth:sanctum`, `verified`, `active`, `throttle:60,1`

### Authentication Endpoints

| Method | Endpoint                         | Middleware                               | Description               |
| ------ | -------------------------------- | ---------------------------------------- | ------------------------- |
| `POST` | `/auth/register`                 | `guest:sanctum`, `throttle:5,1`          | Register a new user       |
| `POST` | `/auth/login`                    | `guest:sanctum`, `throttle:5,1`          | Login and get token       |
| `POST` | `/auth/logout`                   | `auth:sanctum`, `active`                 | Logout current device     |
| `POST` | `/auth/logout-all`               | `auth:sanctum`, `active`                 | Logout all devices        |
| `POST` | `/auth/forgot-password`          | `guest:sanctum`, `throttle:5,1`          | Send password reset email |
| `POST` | `/auth/reset-password`           | `guest:sanctum`, `throttle:5,1`          | Reset password with token |
| `POST` | `/auth/email/resend`             | `auth:sanctum`, `active`, `throttle:6,1` | Resend verification email |
| `GET`  | `/auth/email/verify/{id}/{hash}` | `signed`, `throttle:6,1`                 | Verify email address      |

### Profile Endpoints

| Method | Endpoint          | Description                      |
| ------ | ----------------- | -------------------------------- |
| `GET`  | `/profile`        | Get authenticated user's profile |
| `POST` | `/profile/update` | Update name, email, or password  |

> Changing email resets verification. Changing password logs out other devices.

### Admin Users Endpoints

| Method   | Endpoint                                 | Policy             | Description                                   |
| -------- | ---------------------------------------- | ------------------ | --------------------------------------------- |
| `GET`    | `/admin/users`                           | `viewAny`          | List users (with filters, search, pagination) |
| `GET`    | `/admin/users/trashed`                   | `viewTrashed`      | List soft-deleted users                       |
| `POST`   | `/admin/users`                           | `create`           | Create a new user                             |
| `GET`    | `/admin/users/{user}`                    | `view`             | Get user details with roles & permissions     |
| `PUT`    | `/admin/users/{user}`                    | `update`           | Update user data                              |
| `DELETE` | `/admin/users/{user}`                    | `delete`           | Soft delete a user                            |
| `POST`   | `/admin/users/{user}/restore`            | `restore`          | Restore soft-deleted user                     |
| `DELETE` | `/admin/users/{user}/force-delete`       | `forceDelete`      | Permanently delete user                       |
| `PUT`    | `/admin/users/{user}/role`               | `changeRole`       | Change user's role                            |
| `POST`   | `/admin/users/{user}/permissions/give`   | `givePermission`   | Grant direct permissions                      |
| `POST`   | `/admin/users/{user}/permissions/revoke` | `revokePermission` | Revoke direct permissions                     |

#### User Filtering Parameters

| Parameter        | Type    | Description                               |
| ---------------- | ------- | ----------------------------------------- |
| `status`         | boolean | Filter by active/inactive                 |
| `created_from`   | date    | Filter users created after date (`Y-m-d`) |
| `email_verified` | boolean | Filter by verification status             |
| `search`         | string  | Search by id, name, or email              |
| `sort`           | string  | Sort order: `asc` or `desc`               |
| `per_page`       | integer | Results per page (1-100)                  |

### Admin Roles Endpoints

| Method   | Endpoint              | Policy    | Description                       |
| -------- | --------------------- | --------- | --------------------------------- |
| `GET`    | `/admin/roles`        | `viewAny` | List all roles with permissions   |
| `POST`   | `/admin/roles`        | `create`  | Create a new custom role          |
| `GET`    | `/admin/roles/{role}` | `view`    | Get role details with permissions |
| `PUT`    | `/admin/roles/{role}` | `update`  | Update custom role                |
| `DELETE` | `/admin/roles/{role}` | `delete`  | Delete custom role                |

### Admin Permissions Endpoints

| Method | Endpoint             | Policy    | Description          |
| ------ | -------------------- | --------- | -------------------- |
| `GET`  | `/admin/permissions` | `viewAny` | List all permissions |

---

## Request Validation

### Register

```json
{
    "name": "required|string|max:255",
    "email": "required|string|email|max:255|unique:users",
    "password": "required|string|min:8|confirmed"
}
```

### Login

```json
{
    "email": "required|string|email",
    "password": "required|string"
}
```

### Forgot Password

```json
{
    "email": "required|email"
}
```

### Reset Password

```json
{
    "token": "required|string",
    "email": "required|email",
    "password": "required|string|min:8|confirmed"
}
```

### Update Profile

```json
{
    "current_password": "required|current_password",
    "name": "sometimes|string|max:255",
    "email": "sometimes|string|email|max:255|unique:users",
    "password": "sometimes|string|min:8|confirmed"
}
```

### Create User (Admin)

```json
{
    "name": "required|string|max:255",
    "email": "required|string|email|max:255|unique:users",
    "password": "required|string|min:8|confirmed",
    "role": "sometimes|string|exists:roles,name (default: user)"
}
```

### Update User (Admin)

```json
{
    "name": "sometimes|string|max:255",
    "email": "sometimes|email|max:255|unique:users",
    "status": "sometimes|boolean"
}
```

### Change Role

```json
{
    "role": "required|string|exists:roles,name"
}
```

### Give / Revoke Permissions

```json
{
    "permissions": "required|array|min:1",
    "permissions.*": "required|string|exists:permissions,name"
}
```

### Create / Update Role

```json
{
    "name": "required|string|max:255|unique:roles|not_in:user,admin,super_admin",
    "permissions": "required|array",
    "permissions.*": "exists:permissions,name"
}
```

### User Filters

```json
{
    "status": "nullable|boolean",
    "created_from": "nullable|date",
    "email_verified": "nullable|boolean",
    "search": "nullable|string|max:255",
    "sort": "nullable|in:asc,desc",
    "per_page": "nullable|integer|min:1|max:100"
}
```

---

## API Response Format

All responses follow a consistent JSON structure:

### Success Response

```json
{
  "success": true,
  "code": 200,
  "message": "Operation successful",
  "data": { ... },
  "errors": null
}
```

### Paginated Response

```json
{
  "success": true,
  "code": 200,
  "message": "Users retrieved successfully",
  "data": {
    "users": [ ... ],
    "pagination": {
      "total": 50,
      "per_page": 10,
      "current_page": 1,
      "last_page": 5,
      "from": 1,
      "to": 10
    }
  },
  "errors": null
}
```

### Error Response

```json
{
    "success": false,
    "code": 422,
    "message": "Validation failed",
    "data": null,
    "errors": {
        "email": ["The email has already been taken."]
    }
}
```

---

## API Resources

### UserResource (Auth)

```json
{
    "id": 1,
    "name": "Beshoy Fady",
    "email": "beshoy@example.com"
}
```

### ProfileResource

```json
{
    "name": "Beshoy Fady",
    "email": "beshoy@example.com",
    "status": true,
    "email_verified_at": "2026-03-10 14:30:00",
    "updated_at": "2026-03-10 14:30:00",
    "created_at": "2026-03-08 14:30:00"
}
```

### UsersResource (Admin)

```json
{
    "id": 1,
    "name": "Beshoy Fady",
    "email": "beshoy@example.com",
    "email_verified_at": "2026-03-10 14:30:00",
    "status": true,
    "deleted_at": null,
    "role": "super_admin",
    "permissions": ["view users", "create users"],
    "created_at": "2026-03-08 14:30:00",
    "updated_at": "2026-03-10 14:30:00"
}
```

### RoleResource

```json
{
    "id": 1,
    "name": "admin",
    "permissions": [
        { "id": 1, "name": "view users" },
        { "id": 2, "name": "view trashed users" }
    ]
}
```

### PermissionResource

```json
{
    "id": 1,
    "name": "view users"
}
```

---

## Policies & Authorization Logic

### UserPolicy

| Method             | Permission Required  | Guard Checks                                   |
| ------------------ | -------------------- | ---------------------------------------------- |
| `viewAny`          | `view users`         | —                                              |
| `viewTrashed`      | `view trashed users` | —                                              |
| `view`             | `view users`         | —                                              |
| `create`           | `create users`       | —                                              |
| `update`           | `update users`       | `notSelf`, `notSuperAdmin`                     |
| `delete`           | `delete users`       | `notSelf`, `notSuperAdmin`                     |
| `restore`          | `restore users`      | `notSuperAdmin`                                |
| `forceDelete`      | `force delete users` | `notSelf`, `notSuperAdmin`                     |
| `changeRole`       | `change roles`       | `notSelf`, `notSuperAdmin`, `assignSuperAdmin` |
| `givePermission`   | `give permissions`   | `notSelf`, `notSuperAdmin`                     |
| `revokePermission` | `revoke permissions` | `notSelf`, `notSuperAdmin`                     |

**Guard Abilities (always enforced, even for super_admin):**

- **`notSelf`** — Prevents users from performing actions on their own account
- **`notSuperAdmin`** — Prevents non-super-admin users from modifying super admin accounts
- **`assignSuperAdmin`** — Only super_admin can assign the super_admin role

### RolePolicy

| Method    | Permission Required | Notes                          |
| --------- | ------------------- | ------------------------------ |
| `viewAny` | `view roles`        | —                              |
| `view`    | `view roles`        | —                              |
| `create`  | `create roles`      | —                              |
| `update`  | `update roles`      | System roles cannot be updated |
| `delete`  | `delete roles`      | System roles cannot be deleted |

### PermissionPolicy

| Method    | Permission Required |
| --------- | ------------------- |
| `viewAny` | `view permissions`  |

### Gate Configuration

`super_admin` role bypasses all policy checks **except** the guard abilities (`notSelf`, `notSuperAdmin`, `assignSuperAdmin`).

---

## Middleware

| Middleware          | Alias    | Scope                       | Description                                                                         |
| ------------------- | -------- | --------------------------- | ----------------------------------------------------------------------------------- |
| `ForceJsonResponse` | —        | Prepended to all API routes | Sets `Accept: application/json` header on all requests to ensure JSON responses     |
| `CheckUserStatus`   | `active` | Applied to protected routes | Returns `403 Forbidden` if the authenticated user's `status` is `false` (suspended) |

---

## Notifications

| Notification          | Type  | Queue | Triggered By                               |
| --------------------- | ----- | ----- | ------------------------------------------ |
| `VerifyEmailQueued`   | Email | Yes   | Registration, email change (profile/admin) |
| `ResetPasswordQueued` | Email | Yes   | Forgot password request                    |

Both notifications extend Laravel's built-in notification classes and implement `ShouldQueue` for async processing.

---

## Error Handling

All exceptions are caught and returned as standardized JSON responses:

| Exception                       | Status Code | Message                                         |
| ------------------------------- | ----------- | ----------------------------------------------- |
| `ValidationException`           | 422         | Validation errors with field details            |
| `AuthenticationException`       | 401         | Unauthenticated                                 |
| `AuthorizationException`        | 403         | Forbidden                                       |
| `ModelNotFoundException`        | 404         | Resource not found                              |
| `NotFoundHttpException`         | 404         | Route not found                                 |
| `MethodNotAllowedHttpException` | 405         | Method not allowed                              |
| Generic `Throwable`             | 500         | Server error (debug info when `APP_DEBUG=true`) |

---

## Authentication Flow

```
1. Register
   POST /auth/register → Create user (role: user) → Generate 30-day token → Send verification email (queued)

2. Login
   POST /auth/login → Verify credentials → Check status → Generate 30-day token → Return user + token

3. Email Verification
   GET /auth/email/verify/{id}/{hash} → Verify signed URL → Mark email as verified

4. Resend Verification
   POST /auth/email/resend → Check if already verified → Resend email (queued)

5. Forgot Password
   POST /auth/forgot-password → Send reset email (prevents user enumeration)

6. Reset Password
   POST /auth/reset-password → Validate token → Update password → Revoke all tokens

7. Logout
   POST /auth/logout → Delete current token
   POST /auth/logout-all → Delete all tokens

8. Profile Update
   POST /profile/update → Requires current_password
   → If email changes: reset email_verified_at, send new verification
   → If password changes: revoke all other device tokens
```

---

## Postman Collection

A full Postman collection is included in the repository covering all API endpoints with example requests and environment variables.

**Location:** `Postman/laravel_api_rbac_starter.postman_collection.json`

To use it:

1. Open Postman
2. Click **Import**
3. Select the file from the `Postman/` folder
4. Set the `base_url` environment variable to `http://127.0.0.1:8000/api`
5. After login, set the `token` variable with the returned bearer token

---

## Running Tests

```bash
# Run all tests
php artisan test

# Or via Composer
composer test
```

---

## Author

**Beshoy Fady**

- GitHub: [github.com/beshoyfadykamel](https://github.com/beshoyfadykamel)

---

## License

This project is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).
