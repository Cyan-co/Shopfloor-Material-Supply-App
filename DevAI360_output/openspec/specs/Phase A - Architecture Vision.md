# Phase A: Architecture Vision

## 1. Vision and scope

### Vision
To create a seamless, real-time, and error-proof digital bridge between the shopfloor and the warehouse, ensuring production lines are supplied with the right materials at the right time. This application will serve as the single source of truth for material flow, improving efficiency, providing operational visibility, and forming a data foundation for future analytics.

### Scope
This project covers the design, development, and deployment of a web application that facilitates the entire lifecycle of a material supply request. The scope for Version 1.0 includes:
- User authentication and role-based access for three distinct roles: Production Line User, Warehouse User, and Admin.
- A digital workflow for creating, processing, and receiving material "Delivery Orders."
- Real-time status tracking of orders from "New" to "Completed."
- An administrative dashboard for monitoring all orders and performing manual overrides (edit status, delete orders) with full audit trails.
- The application will be a responsive web app, accessible on both desktop and mobile devices.

**Out of Scope for V1.0:**
- Integration with external inventory management systems.
- Push notifications and email alerts.
- Advanced analytics and reporting dashboards.
- Automated request prioritization (all requests are FIFO).

### Stakeholders
| Stakeholder | Role | Interest |
|---|---|---|
| Production Line User | End User | Fast and easy way to request materials, confirmation of delivery. |
| Warehouse User | End User | Clear, consolidated list of new orders; simple process to update order status. |
| Shopfloor Manager | Business Stakeholder | Reduced production downtime, improved material flow efficiency. |
| Warehouse Manager | Business Stakeholder | Improved order accuracy, better workload management for warehouse staff. |
| System Administrator | Technical Stakeholder | System oversight, user management, ability to correct data errors. |

---

## 2. Architecture principles (impact on implementation)

| # | Principle | Rationale | Implementation impact |
|---|---|---|---|
| 1 | **Stateless Services** | To ensure scalability and resilience, backend services must not store session state. This allows for horizontal scaling and simplifies failover. | - All API endpoints must be RESTful and stateless. <br> - Client-side tokens (e.g., JWT) must be used for managing user sessions. <br> - No in-memory storage of user data on the server between requests. |
| 2 | **Immutable Audit Trail** | To meet reliability and accountability requirements (FR-007, FR-008, SYS-003), every state change of an order must be captured and be immutable. | - Use an append-only log or a dedicated `order_history` table to record all status changes. <br> - The primary `orders` table should show the *current* state, but the history table must be the source of truth for audits. <br> - API endpoints that modify order state MUST write to the audit trail in the same transaction. |
| 3 | **Spec-First API Development** | To decouple frontend and backend development and ensure clear contracts, the API will be defined using the OpenAPI 3.0 specification *before* any implementation begins. | - An `openapi.yaml` file will be created and committed to the repository. <br> - Backend development must implement the API exactly as defined in the spec. <br> - Frontend development can use the spec to create mock servers and build UI components in parallel. |
| 4 | **Role-Based Access Control (RBAC) at the API Gateway** | To enforce security (SYS-001) consistently and centrally, access control logic must be handled at the API gateway or a dedicated middleware layer, not within business logic. | - API endpoints must be annotated with the required roles (e.g., `@requires(role='Admin')`). <br> - A middleware function will intercept all incoming requests, validate the user's JWT, and check their role against the required role for the endpoint. <br> - Business logic code should assume the user is authorized and not contain any role-checking `if` statements. |
| 5 | **Component-Based Frontend Architecture** | To ensure maintainability and reusability of the UI (SYS-002), the frontend will be built as a collection of small, independent, and reusable components. | - The application will be built using Angular 17. <br> - Features will be broken down into "smart" container components and "dumb" presentational components. <br> - A shared component library will be created for common UI elements (buttons, forms, modals). |
| 6 | **Data-at-Rest Encryption** | To protect sensitive operational data, all data stored in the database must be encrypted at rest. | - The chosen database solution (e.g., PostgreSQL on a managed cloud service) must have data-at-rest encryption enabled. <br> - No unencrypted backups or exports of the database are permitted. |

---

## 3. How to use this document

- **When to read:** This document is the primary architectural guide and must be read by all developers, architects, and technical leads before starting any implementation work.
- **How to apply:** All design decisions and code implementation must adhere to the principles defined in Section 2. When designing a new feature, explicitly reference the principles that guide your design.
- **Conflict resolution:** If a requirement seems to conflict with a principle, a formal Architecture Decision Record (ADR) must be created to document the conflict and the resolution. The default position is to uphold the principle.

---

## 4. References

- Phase B – Business Architecture.md *(pending)*
- Phase C – Application & Data Architecture.md *(pending)*
- Phase D – Technology Architecture.md *(pending)*
- Phase F – Implementation and Migration Plan.md *(pending)*
- Phase G – Implementation Governance.md *(pending)*
- Phase H – Architecture Changeamento.md *(pending)*
