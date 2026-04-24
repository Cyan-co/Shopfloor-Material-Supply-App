# Phase C: Application Architecture (AA)

## 1. Component Overview

### Backend (Spring Boot 3.x)

- **Root Package**: `com.bosch.shopfloor.materialsupply`
- **Architecture Pattern**: Controller-Service-Repository
- **Language**: Java 21

**Naming Conventions**:
- **Controllers**: Suffix with `Controller` (e.g., `OrderController.java`)
- **Services**: Suffix with `Service` (e.g., `OrderService.java`)
- **Repositories**: Suffix with `Repository` (e.g., `OrderRepository.java`)
- **DTOs**: Suffix with `Dto` (e.g., `CreateOrderDto.java`)

**Rules**:
- All business logic, including state transition validation and security checks, MUST reside exclusively in the **Service layer**.
- Controllers are responsible only for handling HTTP requests, validating input (DTOs), and delegating to the Service layer.
- Repositories are responsible only for database interactions.
- RBAC will be enforced using Spring Security annotations (e.g., `@PreAuthorize`) on Service methods.

---

### Frontend (Angular 17+)

- **App Prefix**: `app-shopfloor`
- **Styling**: Tailwind CSS
- **State Management**: Angular Services with RxJS for reactive state.

**Naming Conventions**:
- **Components**: Suffix with `.component.ts` (e.g., `order-dashboard.component.ts`)
- **Services**: Suffix with `.service.ts` (e.g., `order-api.service.ts`)
- **Modules**: Grouped by feature (e.g., `orders`, `admin`).

**Rules**:
- The UI MUST dynamically show/hide action buttons (e.g., "Pick Up," "Receive") based on the current user's role and the order's state.
- No business logic (e.g., checking if a state transition is valid) is allowed in the frontend. The frontend's role is to make API calls and render the results.
- All API interactions must be centralized in dedicated services (`*.service.ts`).

---

## 2. Integration Contract

- **API Style**: RESTful
- **Base Path**: `/api`
- **Authentication**: Stateless, using JWT (JSON Web Tokens) passed in the `Authorization` header.
- **Format**: JSON (`application/json`) for all request and response bodies.
- **Status Codes**:
  - `200 OK`: Successful `GET` or `PATCH` request.
  - `201 Created`: Successful `POST` request.
  - `204 No Content`: Successful `DELETE` request.
  - `400 Bad Request`: The request is malformed or violates a business rule (e.g., invalid state transition). The response body MUST contain an error code and message.
  - `401 Unauthorized`: The user is not authenticated (e.g., missing or invalid JWT).
  - `403 Forbidden`: The user is authenticated but does not have the required role for the action.
  - `404 Not Found`: The requested resource (e.g., an order with a specific ID) does not exist.

---

## 3. Endpoints (Derived from Business Architecture)

These endpoints are the minimum required to implement the business process defined in Phase B.

| Method | Endpoint | Description | Required Role(s) |
|---|---|---|---|
| `POST` | `/api/auth/login` | Authenticates a user and returns a JWT. | Public |
| `POST` | `/api/orders` | Creates a new Delivery Order. (BR-002) | `Production Line User` |
| `GET` | `/api/orders` | Retrieves a list of orders. Filters by role will be applied by the backend. | `Production Line User`, `Warehouse User`, `Admin` |
| `GET` | `/api/orders/{id}` | Retrieves a single Delivery Order by its ID. | `Production Line User`, `Warehouse User`, `Admin` |
| `PATCH` | `/api/orders/{id}/status` | Updates the status of an order. The request body will contain the new status and any required metadata (e.g., reason for admin change). This single endpoint handles all state transitions. (BR-001, BR-002, BR-004) | `Production Line User`, `Warehouse User`, `Admin` |
| `DELETE` | `/api/orders/{id}` | Performs a soft delete on an order. (BR-006) | `Admin` |

---

## 4. Application Pattern

The system will be a **decoupled client-server application**.
- The **Angular SPA** is the client, responsible for all user interface rendering and interaction.
- The **Spring Boot application** is the server, providing a RESTful API that exposes all business logic and data persistence.
- Communication between the two is strictly via the REST API defined in the integration contract.

---

## 5. Interface Contract (API)

- **Base Path**: `/api`
- **Format**: All data exchange will be in JSON format.
- **Error Handling**: The API will use standard HTTP status codes to indicate the outcome of a request. Error responses will follow a consistent JSON structure:
  ```json
  {
    "timestamp": "2023-10-27T10:00:00Z",
    "status": 400,
    "error": "Bad Request",
    "message": "Invalid state transition from NEW to IN_TRANSIT.",
    "path": "/api/orders/123/status"
  }
  ```

---

## 6. Security Implementation

- **Authentication**: A dedicated `/api/auth/login` endpoint will validate user credentials and issue a signed JWT.
- **Authorization (RBAC)**: Role-based access control will be implemented in the **backend Service layer** using Spring Security. Each method in the `*Service.java` files that performs a restricted action will be annotated (e.g., `@PreAuthorize("hasRole('ADMIN')")`).
- The frontend is **never trusted**. All authorization checks MUST be performed on the backend for every API request. The frontend will receive the user's role after login and use it for UI purposes only.

---
