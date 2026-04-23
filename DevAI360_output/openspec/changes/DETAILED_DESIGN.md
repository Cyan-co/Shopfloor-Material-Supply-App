## Low-Level Design — Complete

**Requirement:** Design a web application for a shopfloor material supply system where users can request materials, warehouse staff can process the orders, and admins can monitor and manage the entire workflow.

### Generated Files

[x] Step 01: Data Model  → changes/01-data-model/proposal.md + tasks.md
[x] Step 02: Backend API → changes/02-backend-api/proposal.md + tasks.md
[x] Step 03: Frontend UI → changes/03-frontend-ui/proposal.md + tasks.md

### Summary

This low-level design provides a complete technical specification for the Shopfloor Material Supply App. It includes:
- A **Data Model** with User, DeliveryOrder, and AuditLog entities using PostgreSQL.
- A **Backend API** designed for a Go-based service architecture, featuring role-based access control and a clear RESTful interface for all workflow actions.
- A **Frontend UI** specification for a responsive React application with distinct, role-based dashboards for Production, Warehouse, and Admin users, detailing the necessary components and user interactions.
