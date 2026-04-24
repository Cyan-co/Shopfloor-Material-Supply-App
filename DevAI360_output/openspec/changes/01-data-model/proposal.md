# Proposal: Shopfloor Material Supply App Data Model

## 1. Entities Overview

- **User**: Represents any user of the application, differentiated by roles (Production, Warehouse, Admin).
- **DeliveryOrder**: The core entity representing a single material supply request from the production line to the warehouse.
- **AuditLog**: A record of all significant events and state changes for a DeliveryOrder, ensuring traceability.

## 2. User Schema

- **Table Name**: `users`
- **Description**: Stores user credentials and role-based access control information.
- **Fields**:
    - `UUID id` (Primary Key): Unique identifier for the user.
    - `String username`: The user's unique login name/email.
    - `String password_hash`: Hashed password for secure authentication.
    - `ENUM role`: User's role (`PRODUCTION_LINE`, `WAREHOUSE`, `ADMIN`).
    - `DateTime created_at`: Timestamp of user creation.

## 3. DeliveryOrder Schema

- **Table Name**: `delivery_orders`
- **Description**: Tracks the entire lifecycle of a material request.
- **Fields**:
    - `UUID id` (Primary Key): Unique identifier for the order.
    - `String material_name`: Name or code of the material being requested.
    - `Integer quantity`: The amount of material requested.
    - `String delivery_location`: The specific production line or area where the material is needed.
    - `ENUM status`: The current state of the order (`NEW`, `IN_PREPARATION`, `IN_TRANSIT`, `COMPLETED`).
    - `UUID requestor_id` (Foreign Key -> users.id): The user who created the order.
    - `UUID warehouse_user_id` (Foreign Key -> users.id, Nullable): The warehouse user processing the order.
    - `DateTime created_at`: Timestamp of order creation.
    - `DateTime updated_at`: Timestamp of the last status change.

## 4. AuditLog Schema

- **Table Name**: `audit_logs`
- **Description**: Provides a detailed, immutable history of actions performed on a DeliveryOrder.
- **Fields**:
    - `UUID id` (Primary Key): Unique identifier for the log entry.
    - `UUID order_id` (Foreign Key -> delivery_orders.id): The order this log entry pertains to.
    - `DateTime timestamp`: When the action occurred.
    - `String action`: The type of action performed (e.g., 'STATUS_CHANGE', 'MANUAL_EDIT', 'DELETE').
    - `String details`: A description of the action, including the reason for manual changes.
    - `UUID changed_by_id` (Foreign Key -> users.id): The user who performed the action.

## 5. Relationships

- **User to DeliveryOrder**: A one-to-many relationship. One `User` (as a requestor) can create many `DeliveryOrders`. One `User` (as a warehouse worker) can process many `DeliveryOrders`.
- **DeliveryOrder to AuditLog**: A one-to-many relationship. One `DeliveryOrder` can have many `AuditLog` entries, creating a complete history.

## 6. Status/Lifecycle for DeliveryOrder

The `status` field follows a clear progression:
- `NEW`: The initial state when a Production Line User creates a request.
- `IN_PREPARATION`: A Warehouse User has accepted the request and is preparing the materials.
- `IN_TRANSIT`: The materials have left the warehouse and are on their way to the production line.
- `COMPLETED`: The Production Line User has confirmed receipt of the materials.
