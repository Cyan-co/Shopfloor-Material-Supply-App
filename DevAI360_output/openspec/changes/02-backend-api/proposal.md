# Backend API Proposal

This document outlines the design for the Backend API of the Shopfloor Material Supply App.

## 1. Authentication

- **Strategy**: JWT (JSON Web Tokens)
- **Login Endpoint**: `POST /api/auth/login`
  - **Request Body**: `{ "username": "user", "password": "password" }`
  - **Response**: `{ "token": "jwt_token" }`
- **Middleware**: All protected endpoints will require a valid JWT in the `Authorization` header (`Bearer <token>`). The middleware will decode the token to identify the user and their role.

## 2. API Endpoints

### Orders

- **`GET /api/orders`**
  - **Description**: Get a list of delivery orders.
  - **Role**:
    - `PRODUCTION`: Returns orders created by the current user.
    - `WAREHOUSE`: Returns orders with status `NEW` or assigned to the current user.
    - `ADMIN`: Returns all orders.
  - **Query Params**: `status`, `date_range`
  - **Response**: `200 OK` with a list of order objects.

- **`POST /api/orders`**
  - **Description**: Create a new delivery order.
  - **Role**: `PRODUCTION`
  - **Request Body**: `{ "material_name": "...", "quantity": 10, "delivery_location": "..." }`
  - **Response**: `201 Created` with the new order object.

- **`PUT /api/orders/{id}/status`**
  - **Description**: Update the status of an order.
  - **Role**:
    - `WAREHOUSE`: Can change status from `NEW` to `IN_PREPARATION` (pick up), and from `IN_PREPARATION` to `IN_TRANSIT`.
    - `PRODUCTION`: Can change status from `IN_TRANSIT` to `COMPLETED`.
    - `ADMIN`: Can change status to any valid state.
  - **Request Body**: `{ "status": "new_status", "reason": "(optional)" }`
  - **Response**: `200 OK` with the updated order object.

- **`DELETE /api/orders/{id}`**
  - **Description**: Delete an order.
  - **Role**: `ADMIN`
  - **Request Body**: `{ "reason": "reason_for_deletion" }`
  - **Response**: `204 No Content`.

### Users

- **`GET /api/users`**
  - **Description**: Get a list of users.
  - **Role**: `ADMIN`
  - **Response**: `200 OK` with a list of user objects.

- **`POST /api/users`**
  - **Description**: Create a new user.
  - **Role**: `ADMIN`
  - **Request Body**: `{ "username": "...", "password": "...", "role": "..." }`
  - **Response**: `201 Created` with the new user object.

## 3. Services

- **`AuthService`**: Handles user authentication, password hashing, and JWT generation/validation.
- **`OrderService`**: Contains the core business logic for managing the lifecycle of delivery orders. It will enforce state transition rules and handle authorization checks.
- **`UserService`**: Manages user creation and retrieval.
- **`AuditService`**: Logs all significant events to the `audit_logs` table.
```