# Phase C: Application Architecture (AA)

## 1. Component Overview

### Backend (Spring Boot 3.x)

- **Root Package**: `com.bosch.shopfloor.materialsupply`
- **Architecture Pattern**: Controller-Service-Repository (Layered Architecture)

**Naming Conventions**:
- Controllers: `OrderController.java`, `AdminController.java`
- Services: `OrderService.java`, `UserService.java`
- Repositories: `OrderRepository.java`, `UserRepository.java`

**Rules**:
- All business logic, including state transition validation as defined in the Business Architecture (Phase B), MUST reside exclusively in the Service layer.
- Role-Based Access Control (RBAC) MUST be enforced at the entry point of each Service layer method using annotations (e.g., `@PreAuthorize`). Controllers are responsible for request/response mapping only.

---

### Frontend Standards (Angular 17+)

- **App Prefix**: `app-shopfloor`
- **Styling**: Tailwind CSS for a utility-first styling approach.

**Naming Conventions**:
- Components: `order-list.component.ts`, `create-order-form.component.ts`
- Services: `order-api.service.ts`

**Rules**:
- The UI MUST be reactive to the authenticated user's role. Components and actions (e.g., buttons) that are not permitted for a given role must be hidden or disabled.
- The frontend must not contain any business logic for state transitions or permissions; it is purely a presentation and user interaction layer that consumes the backend API.

---

## 2. Integration Contract

- **API Style**: RESTful
- **Base Path**: `/api`
- **Format**: JSON (`application/json`) over HTTP/S.
- **Authentication**: JWT (JSON Web Tokens) passed in the `Authorization` header.
- **Status Codes**:
  - `200 OK`: Successful retrieval or update.
  - `201 Created`: Successful creation of a new resource.
  - `400 Bad Request`: The request violates a business rule (e.g., invalid state transition). The response body must contain a clear error message.
  - `403 Forbidden`: The user is authenticated but does not have the required role for the action.
  - `404 Not Found`: The requested resource (e.g., an order) does not exist.

---

## 3. Endpoints (Derived from Business Architecture)

These endpoints directly map to the business processes and rules defined in Phase B.

| Method | Endpoint | Description | Permitted Roles | Business Rule Mapping |
| :--- | :--- | :--- | :--- | :--- |
| `GET` | `/api/orders` | Get a list of orders. Operators see their own; Warehouse/Admins see all. | Production Operator, Warehouse Staff, Admin | - |
| `POST` | `/api/orders` | Create a new material supply order. | Production Operator | BR-04 |
| `PATCH`| `/api/orders/{id}/status`| Update the status of an order. The request body must contain the new status (`"status": "ACCEPTED"`).| Warehouse Staff, Production Operator, Admin | BR-01, BR-02, BR-03, BR-05 |
| `DELETE`| `/api/orders/{id}`| Soft-delete an order. | Admin | BR-06 |

---

## 4. Application Pattern

- **Decoupled Client-Server Architecture**: The Angular Single-Page Application (SPA) is the client, and the Spring Boot application is the server. They communicate exclusively through the RESTful API defined above. This enforces the **API-First Design** principle from Phase A.
- **Stateless Services**: The Spring Boot backend will be fully stateless, adhering to the principle defined in Phase A. All state is persisted in the database.

**Coding Standards**:
- Java package: `com.bosch.shopfloor.materialsupply`
- Angular prefix: `app`

---

## 5. Interface Contract (API)

- **Base Path**: `/api`
- **Format**: JSON

**Example Request Body for `PATCH /api/orders/{id}/status`**:
```json
{
  "status": "ACCEPTED"
}
```

**Example Error Response (`400 Bad Request`)**:
```json
{
  "timestamp": "2023-10-27T10:00:00Z",
  "status": 400,
  "error": "Bad Request",
  "message": "Invalid state transition: Cannot accept an order that is already IN_TRANSIT.",
  "path": "/api/orders/123/status"
}
```

---

## 6. Security Implementation

- RBAC will be implemented in the Spring Boot backend using Spring Security.
- Each service method corresponding to an action will be annotated (e.g., `@PreAuthorize("hasRole('ADMIN')")`) to ensure that the business logic is protected.
- The frontend will receive the user's role upon login and will use this information for UI presentation only. The frontend is **not** a security boundary and must not be trusted. All authorization decisions are made by the backend on every request.
