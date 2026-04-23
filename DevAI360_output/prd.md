# Product Requirements Document (PRD): Shopfloor Material Supply App

## 1. Executive Summary

This document outlines the requirements for a web application designed to digitize and streamline the process of requesting materials from the warehouse for the shopfloor production lines. The goal is to replace the current manual process, reducing errors, increasing efficiency, and providing real-time visibility into the entire material supply lifecycle. This system will serve as the single source of truth for all material requests.

## 2. Goals and Success Criteria

- **Goal 1:** Improve the efficiency of the material request process.
  - **Success Metric:** Reduce the average order fulfillment time (from request to receipt) by 30% within 6 months of launch.
- **Goal 2:** Increase visibility and accountability.
  - **Success Metric:** 100% of material requests are tracked in the system, with accurate timestamps for all status changes.
- **Goal 3:** Reduce material delivery errors.
  - **Success Metric:** Reduce the number of incorrect material deliveries by 90%.

## 3. Solution Recommendation

### Option 1: Full Custom Development
- **Description:** Build a bespoke web application using a modern web stack (e.g., React frontend, Go backend, PostgreSQL database).
- **Pros:** Maximum flexibility for features and UI/UX, scalable for future integrations (inventory, analytics), no vendor lock-in.
- **Cons:** Higher initial development cost and longer time to market.

### Option 2: Low-Code/No-Code Platform
- **Description:** Utilize a platform like Retool or Microsoft Power Apps to build the application with a visual interface and minimal code.
- **Pros:** Extremely fast to build and deploy (days instead of weeks/months), lower upfront cost.
- **Cons:** Potential for platform limitations, less UI/UX customization, long-term subscription costs.

### **Recommended: Option 1 - Full Custom Development**
- **Rationale:** A custom solution is recommended to support the long-term strategic vision. This application is not just a workflow tool but a future data-gathering platform for operational intelligence. The flexibility to integrate with other systems and build custom analytics dashboards will provide a significantly higher return on investment.

## 4. User Roles (User Classes)

- **Production Line User:** Creates material requests and confirms receipt of materials.
- **Warehouse User:** Views new requests, processes them, and updates their status.
- **Admin:** Has full oversight of the system, can manage users, and can manually edit or delete any order.

## 5. Functional Requirements (FRs)

- **FR-001: User Authentication**
  - A user shall be able to log in to the application using an email and password.
  - **Acceptance Criteria:**
    - The system provides a secure login page.
    - Users are redirected to the appropriate dashboard based on their role after login.
    - A "logout" function is available.

- **FR-002: Create Delivery Order (Production Line User)**
  - A Production Line User shall be able to create a new material supply requirement (Delivery Order).
  - **Acceptance Criteria:**
    - The creation form must include fields for `Material Name`, `Quantity`, and `Delivery Location`.
    - The `Requestor Name` is automatically populated with the logged-in user.
    - On submission, a new order is created with the status "New".

- **FR-003: View and Pick Up Order (Warehouse User)**
  - A Warehouse User shall be able to view a list of "New" Delivery Orders and "pick one up" for processing.
  - **Acceptance Criteria:**
    - The warehouse dashboard displays a list of all orders with the "New" status, sorted by creation time.
    - A "Pick Up" button is available for each new order.
    - Clicking "Pick Up" changes the order status to "In Preparation" and assigns the Warehouse User to the order.

- **FR-004: Mark Order as In Transit (Warehouse User)**
  - A Warehouse User shall be able to mark an "In Preparation" order as "In Transit".
  - **Acceptance Criteria:**
    - For any order in the "In Preparation" state, a "Mark as In Transit" button is available.
    - Clicking the button changes the order status to "In Transit".

- **FR-005: Receive Order (Production Line User)**
  - A Production Line User shall be able to confirm the receipt of an "In Transit" order.
  - **Acceptance Criteria:**
    - For any order in the "In Transit" state, a "Receive" button is available to the originating Production Line User.
    - Clicking "Receive" changes the order status to "Completed".

- **FR-006: Order Monitoring (Admin)**
  - An Admin shall be able to view a dashboard of all orders, regardless of status.
  - **Acceptance Criteria:**
    - The dashboard displays all orders with their current status, timestamps for each status change, and assigned users.
    - The dashboard is searchable by Material Name and filterable by status and date range.

- **FR-007: Manual Order Editing (Admin)**
  - An Admin shall be able to manually edit the status of any order.
  - **Acceptance Criteria:**
    - An "Edit" function is available for every order on the Admin dashboard.
    - The Admin must provide a reason for the change, which is logged in an audit trail.

- **FR-008: Order Deletion (Admin)**
  - An Admin shall be able to delete any order from the system.
  - **Acceptance Criteria:**
    - A "Delete" function is available for every order on the Admin dashboard.
    - The Admin must provide a reason for the deletion, which is logged in an audit trail.

## 6. Non-Functional Requirements (NFRs)

- **SYS-001 (Security):** The system shall implement role-based access control (RBAC) to ensure users can only perform actions permitted for their role.
- **SYS-002 (Usability):** The application must be a responsive web application, fully functional on both desktop browsers and mobile/tablet devices.
- **SYS-003 (Reliability):** All state changes for an order (create, pick up, transit, receive, edit, delete) must be logged in an immutable audit trail.
- **SYS-004 (Performance):** Order lists and dashboards should load in under 2 seconds.

## 7. Out of Scope (for Version 1.0)

- Integration with existing inventory management systems.
- Push notifications or email alerts.
- Advanced analytics and reporting dashboards beyond the basic admin monitoring view.
- Request prioritization (all requests are treated as FIFO - First In, First Out).

## 8. Assumptions

- A simple, self-contained user management system will be built for V1. No integration with external identity providers (e.g., Active Directory) is required.
- Users will have reliable network access on the shop floor.
