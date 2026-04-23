# Backend API Implementation Tasks

This document lists the tasks required to implement the backend API for the Shopfloor Material Supply App.

- [ ] **Task 1: Project Setup**
  - **Description**: Initialize a new Go project. Set up the project structure with packages for `api`, `auth`, `models`, `services`, and `store`.
  - **Acceptance Criteria**: A new Go module is created, and the basic directory structure is in place.

- [ ] **Task 2: Implement Authentication**
  - **Description**: Implement the `AuthService` to handle JWT-based authentication. This includes password hashing with `bcrypt` and token generation/validation.
  - **Acceptance Criteria**: The `POST /api/auth/login` endpoint is functional and returns a valid JWT for correct credentials.

- [ ] **Task 3: Implement Auth Middleware**
  - **Description**: Create a middleware that inspects the `Authorization` header, validates the JWT, and extracts user information (ID and role) to be used in protected routes.
  - **Acceptance Criteria**: The middleware correctly protects routes and makes user info available in the request context.

- [ ] **Task 4: Implement Order Service**
  - **Description**: Implement the `OrderService` with the core business logic for managing the order lifecycle, including all state transition rules.
  - **Acceptance Criteria**: All methods in the service are implemented and unit-tested.

- [ ] **Task 5: Implement API Endpoints**
  - **Description**: Create the API handlers/controllers for all the endpoints defined in the proposal (`/api/orders`, `/api/users`). Connect them to the appropriate services.
  - **Acceptance Criteria**: All API endpoints are implemented and respond correctly according to the proposal.

- [ ] **Task 6: Implement Role-Based Access Control (RBAC)**
  - **Description**: Integrate the auth middleware to enforce role-based access control for each endpoint as specified in the proposal.
  - **Acceptance Criteria**: Endpoints are accessible only by users with the required roles. Unauthorized access attempts are rejected with a `403 Forbidden` status.

- [ ] **Task 7: Implement Audit Logging**
  - **Description**: Integrate the `AuditService` to log all relevant events, such as status changes and admin actions.
  - **Acceptance Criteria**: Audit log entries are created in the database for all required events.
```