# Implementation Tasks: Shopfloor Material Supply App

## Phase 1: Backend Development

### Task 1.1: Data Model and Database Setup
- **Description**: Define the database schema and set up migrations.
- **Acceptance Criteria**:
  - Migration scripts for `users`, `delivery_orders`, and `audit_logs` tables are created.
  - Go structs for all models and enums (`UserRole`, `OrderStatus`) are implemented.

### Task 1.2: Authentication Service
- **Description**: Implement user login and JWT generation.
- **Acceptance Criteria**:
  - `POST /api/auth/login` endpoint is functional and returns a valid JWT for correct credentials.

### Task 1.3: Core Order Service
- **Description**: Implement the business logic for the order lifecycle.
- **Acceptance Criteria**:
  - The service correctly handles creation, status transitions, and ownership rules for delivery orders.
  - All state changes are logged to the `audit_logs` table.

### Task 1.4: API Endpoints & RBAC
- **Description**: Implement the full REST API and secure it with role-based access control.
- **Acceptance Criteria**:
  - All endpoints defined in the API proposal are implemented.
  - A middleware correctly enforces that only users with the appropriate role can access each endpoint.

## Phase 2: Frontend Development

### Task 2.1: Project Setup and API Service
- **Description**: Initialize the React project and create a service to communicate with the backend.
- **Acceptance Criteria**:
  - A React application is created with TypeScript.
  - A service layer (`api.ts`) is implemented to handle all HTTP requests, including attaching the auth token.
  - State management for the user session is in place.

### Task 2.2: Views and Components
- **Description**: Build all required pages and reusable components.
- **Acceptance Criteria**:
  - Components for `LoginPage`, `OrderCard`, `CreateOrderForm`, and all role-based dashboards are implemented.
  - Client-side routing is functional.

### Task 2.3: Role-Based UI Implementation
- **Description**: Implement the logic to dynamically show/hide UI elements based on the user's role.
- **Acceptance Criteria**:
  - Production, Warehouse, and Admin users see only the UI elements and actions relevant to their role as defined in the UI proposal.
  - The application is responsive and usable on mobile devices.

## Phase 3: Deployment

### Task 3.1: Containerization and CI/CD
- **Description**: Create Dockerfiles for the backend and frontend and set up CI/CD pipelines.
- **Acceptance Criteria**:
  - `Dockerfile` for Go and Nginx/React are created.
  - CI/CD pipelines automatically build, test, and push container images to a registry upon code changes.
