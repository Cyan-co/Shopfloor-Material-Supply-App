# Phase E: Opportunities and Solutions

## 1. Gap Analysis Summary

### Current State
**Greenfield.** There is no existing automated system. The current process is manual, relying on verbal communication or paper-based requests, resulting in a lack of traceability, efficiency, and data.

### Target State
The target state is a fully implemented web application as defined in the architecture documents for Phase B (Business), Phase C (Application & Data), and Phase D (Technology). This includes a containerized, observable, and resilient system with a clear separation of concerns between the frontend, backend, and database.

### Gap Summary
The gap is the entirety of the target system. This project involves creating the application, data persistence, and underlying infrastructure from scratch.

---

## 2. Solution Building Blocks

### 2.1 Application Building Blocks

| ID | Building Block | Description | Gap Addressed | Build/Buy |
|---|---|---|---|---|
| ABB-01 | **Backend API Core** | Spring Boot application implementing the RESTful API for `DeliveryOrder` and `User` management, including business logic and RBAC security. | Core application logic | **Build** |
| ABB-02 | **Frontend UI Shell** | Angular application shell with routing, authentication handling, and basic layout for different user roles. | Core user interface | **Build** |
| ABB-03 | **Order Lifecycle UI** | Angular components for creating, viewing, and updating the status of `DeliveryOrders` for all user roles. | User-facing features | **Build** |
| ABB-04 | **Admin Dashboard UI** | Specific Angular components for the Admin role to monitor all orders and perform manual overrides (edit/delete). | Admin oversight | **Build** |

### 2.2 Data Building Blocks

| ID | Building Block | Description | Gap Addressed | Build/Buy |
|---|---|---|---|---|
| DBB-01 | **Database Schema** | Initial PostgreSQL schema defined via Flyway migration scripts, including `users`, `delivery_orders`, and `audit_logs` tables with all constraints. | Data persistence | **Build** |

### 2.3 Technology Building Blocks

| ID | Building Block | Description | Gap Addressed | Build/Buy |
|---|---|---|---|---|
| TBB-01 | **Containerization** | Dockerfiles for both the Spring Boot backend and the Angular frontend (using a multi-stage NGINX build). | Deployable artifacts | **Build** |
| TBB-02 | **CI/CD Pipeline** | A complete GitHub Actions workflow for building, testing, scanning, and deploying the application to Kubernetes. | Automation | **Build** |
| TBB-03 | **Infrastructure (IaC)** | Kubernetes YAML manifests for all environments (DEV, TEST, STAGING, PROD) defining deployments, services, ingress, and secrets. | Deployment environment | **Build** |
| TBB-04 | **Monitoring Stack** | Configuration for Prometheus (scraping), Grafana (dashboards), and Alertmanager (alerts) to ensure observability. | Observability | **Buy/Reuse** |

---

## 3. Build vs. Buy Analysis

### Build Decisions

| Component | Rationale |
|---|---|
| **Custom Application Logic** (ABB-01, ABB-02, etc.) | The core business logic is unique to the shopfloor process and requires a custom implementation. This is the primary value proposition of the project. |
| **Database Schema** (DBB-01) | The data model is tailored specifically to the application's needs. |

### Buy/Reuse Decisions

| Component | Product/Library | Rationale |
|---|---|---|
| **Web Frameworks** | Spring Boot, Angular | Industry-standard, well-supported frameworks that accelerate development and provide robust foundations. |
| **Database** | PostgreSQL | A powerful, open-source, and reliable relational database that meets all project requirements. |
| **Containerization** | Docker, Kubernetes | The de-facto standards for containerization and orchestration, providing scalability and portability. |
| **Monitoring** | Prometheus, Grafana | A mature, widely adopted open-source stack for metrics and observability. |

---

## 4. Work Packages

These work packages group the building blocks into logical, sequenced deliverables.

| WP ID | Name | Building Blocks | Dependencies |
|---|---|---|---|
| WP-01 | **Project Foundation** | TBB-01, TBB-02, TBB-03, DBB-01 | None |
| WP-02 | **Backend API - Core** | ABB-01 (for read/create) | WP-01 |
| WP-03 | **Frontend - Core** | ABB-02, ABB-03 (for read/create) | WP-02 |
| WP-04 | **Full Workflow Implementation** | ABB-01 (updates), ABB-03 (updates) | WP-03 |
| WP-05 | **Admin & Observability** | ABB-04, TBB-04 | WP-04 |

### Work Package Sequencing

```
WP-01 (Setup CI/CD, K8s, and DB Schema)
    ↓
WP-02 (Build basic Order creation/viewing API)
    ↓
WP-03 (Build UI for Order creation/viewing)
    ↓
WP-04 (Implement full status update logic in Backend & UI)
    ↓
WP-05 (Implement Admin features and Monitoring)
```
