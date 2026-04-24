# Solution Proposal: Shopfloor Material Supply App

## 1. Problem Statement

The current process for requesting materials from the warehouse for the shopfloor is manual, leading to inefficiencies, errors, and a lack of real-time visibility. This application aims to digitize and streamline this workflow, providing a single source of truth for all material requests.

## 2. Proposed Solution

We will build a web application that allows different user roles to manage the material supply lifecycle:
- **Production Line Users** can create new material requests (Delivery Orders).
- **Warehouse Users** can view and process these requests.
- **Admins** can oversee the entire system, manage users, and intervene in the process if necessary.

The system will be built as a modern, three-tier application with a clear separation of concerns between the database, a backend API, and a frontend UI.

## 3. Scope

- User authentication with role-based access control.
- Full lifecycle management for Delivery Orders: `NEW` -> `IN_PREPARATION` -> `IN_TRANSIT` -> `COMPLETED`.
- An immutable audit trail for all significant actions.
- A responsive user interface accessible on desktops, tablets, and mobile devices.

## 4. Technology Stack

- **Backend**: Go
- **Frontend**: React with TypeScript
- **Database**: PostgreSQL
- **Authentication**: JWT (JSON Web Tokens)
- **Deployment**: Docker containers
