# Phase A: Architecture Vision

## 1. Vision and scope

### Vision
To create a seamless, efficient, and transparent digital workflow for shopfloor material supply, replacing the manual request process. This system will provide real-time visibility into the material lifecycle, reduce fulfillment errors, and serve as a foundational platform for future operational intelligence and analytics.

### Scope
The scope for Version 1.0 is a self-contained web application that manages the entire lifecycle of a material request:
- **In Scope:** User authentication, creation of delivery orders by production line staff, viewing and processing of orders by warehouse staff, status updates (New, In Preparation, In Transit, Completed), and administrative oversight for monitoring, manual status edits, and order deletion. The application must be responsive and accessible on desktop and mobile devices.
- **Out of Scope (for V1.0):** Integration with external inventory systems, automated notifications (email/push), advanced analytics dashboards, and request prioritization logic.

### Stakeholders
- **Production Line User:** Initiates material requests and confirms receipt.
- **Warehouse User:** Fulfills material requests.
- **Admin:** Oversees the system, manages orders, and has full system privileges.

---

## 2. Architecture principles (impact on implementation)

These principles are mandatory constraints on the system's design and implementation.

| # | Principle | Rationale | Implementation impact |
|---|---|---|---|
| 1 | **Spec-First, API-First Design** | Ensures clarity and decouples frontend and backend development. The API contract is the source of truth. | An OpenAPI (Swagger) specification **MUST** be created before any backend coding begins. Backend development will implement this spec. Frontend development will mock and consume this spec. |
| 2 | **Role-Based Access Control (RBAC)** | To enforce strict security and ensure users only perform actions permitted for their role, as defined in FR-007, FR-008, and SYS-001. | All API endpoints **MUST** be protected. A middleware or filter **MUST** validate the user's role against the required role for the endpoint before processing any request. Logic must be centralized, not scattered. |
| 3 | **Immutable Audit Trail** | To ensure reliability and traceability for all critical operations, as required by SYS-003. | For every state change of an order (create, update, delete), a corresponding immutable record **MUST** be written to a separate `order_audit_log` table. This log cannot be modified or deleted by any user role. |
| 4 | **Standardized Technology Stack** | To ensure maintainability, security, and alignment with enterprise standards. | The application **MUST** use Java 21 for the backend, Angular 17 for the frontend, and connect to internal services via the Bosch Px Proxy. No other frameworks or languages are permitted without a formal exception review. |
| 5 | **Stateless Services** | To achieve scalability and resilience. Services should not store client session data locally. | The backend API **MUST** be stateless. All state required to process a request must be provided in the request itself (e.g., via JWT tokens for authentication). Avoid using `HttpSession` or similar server-side session mechanisms. |

---

## 3. How to use this document

This document is the primary architectural guide for this project.
- **Developers/Agents:** You **MUST** read this document before writing any code or specifications. The principles defined in Section 2 are not suggestions; they are mandatory constraints that must be reflected in the design and implementation of your components.
- **Code Reviews:** During code reviews, every pull request **MUST** be checked for compliance with these architectural principles.
- **Conflict Resolution:** If a functional requirement appears to conflict with an architectural principle, the conflict must be escalated to the Solution Architect for resolution *before* proceeding with implementation.

---

## 4. References

This document is the first in a series of architecture specifications.
- Phase B – Business Architecture (forthcoming)
- Phase C – Application & Data Architecture (forthcoming)
- Phase D – Technology Architecture (forthcoming)
- Phase F – Migration Plan (forthcoming)
- Phase G – Governance (forthcoming)
