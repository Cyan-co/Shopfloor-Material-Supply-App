# Implementation Tasks: Shopfloor Material Supply App

This document consolidates the high-level tasks required to implement the application.

## Epic 1: Data Model and Database Setup

- [ ] Task 1.1: Initialize PostgreSQL database and configure migration tool.
- [ ] Task 1.2: Write and apply migrations for `users`, `delivery_orders`, and `audit_logs` tables.
- [ ] Task 1.3: Define Go structs and enums for all data models.

## Epic 2: Backend API Development

- [ ] Task 2.1: Set up Go project structure for API, services, and data stores.
- [ ] Task 2.2: Implement JWT-based authentication service and middleware.
- [ ] Task 2.3: Implement role-based access control (RBAC) middleware.
- [ ] Task 2.4: Implement `OrderService` with all business logic for order lifecycle management.
- [ ] Task 2.5: Implement `AuditService` to log all state-changing actions.
- [ ] Task 2.6: Build and test all API endpoints for orders and users.

## Epic 3: Frontend UI Development

- [ ] Task 3.1: Set up React project with TypeScript and client-side routing.
- [ ] Task 3.2: Create a reusable API service to communicate with the backend.
- [ ] Task 3.3: Implement global state management for user sessions.
- [ ] Task 3.4: Build all common components (`Navbar`, `OrderCard`, `LoginForm`, etc.).
- [ ] Task 3.5: Build the role-based dashboard views for `PRODUCTION_LINE`, `WAREHOUSE`, and `ADMIN` users.
- [ ] Task 3.6: Ensure the application is fully responsive.

## Epic 4: Deployment and Verification

- [ ] Task 4.1: Create Dockerfiles for the backend and frontend applications.
- [ ] Task 4.2: Write a `docker-compose.yml` file for easy local development setup.
- [ ] Task 4.3: Configure environment variables for production deployment.
- [ ] Task 4.4: Deploy the application to a staging environment and perform end-to-end verification.
