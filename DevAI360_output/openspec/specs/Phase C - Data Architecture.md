# Phase C: Data Architecture (DA)

## 1. Data Architecture Overview

### Data Principles
- **Single Source of Truth:** The PostgreSQL database is the absolute source of truth for all application data.
- **Data Integrity by Default:** Integrity is enforced at the database level using constraints (NOT NULL, FK, CHECK) to prevent invalid data, regardless of the application logic.
- **Auditing is Mandatory:** All changes to business-critical entities, especially `delivery_orders`, must be captured in an immutable audit trail.
- **Soft Deletes for Safety:** Business-critical records should be soft-deleted to allow for recovery and maintain historical integrity.

### Technology Stack
- **Database:** PostgreSQL 15+
- **ORM:** Spring Data JPA
- **Migrations:** Flyway for schema version control.
- **Caching:** Redis is recommended for caching frequently accessed, non-critical data like user sessions, but is not mandatory for V1.

---

## 2. Logical Data Model

### 2.1 Core Entities

| Entity | Description |
|---|---|
| **User** | Represents an actor in the system (Production, Warehouse, Admin). |
| **DeliveryOrder** | The central entity representing a single material supply request. |
| **AuditLog** | Records every state change and modification to a `DeliveryOrder`. |

### 2.2 Entity Relationships

```
User (1) ----< (N) DeliveryOrder (as requester)
User (1) ----< (N) DeliveryOrder (as processor)
DeliveryOrder (1) ----< (N) AuditLog
```

---

## 3. Physical Data Model

### 3.1 Naming Conventions

| Element | Convention | Example |
|---|---|---|
| Table | `snake_case`, plural | `delivery_orders`, `users` |
| Column | `snake_case` | `created_at`, `order_status` |
| Primary Key | `id` | `id` |
| Foreign Key | `{table_name_singular}_id` | `user_id`, `delivery_order_id` |
| Index | `idx_{table}_{columns}` | `idx_delivery_orders_status` |

### 3.2 Table Definitions

**Table: `users`**

| Column | Type | Constraints | Index |
|---|---|---|---|
| id | UUID | PK, NOT NULL | PK |
| email | VARCHAR(255) | UNIQUE, NOT NULL | UQ |
| password_hash | VARCHAR(255) | NOT NULL | - |
| role | VARCHAR(50) | NOT NULL, CHECK (role IN ('PRODUCTION', 'WAREHOUSE', 'ADMIN')) | IDX |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |

**Table: `delivery_orders`**

| Column | Type | Constraints | Index |
|---|---|---|---|
| id | UUID | PK, NOT NULL | PK |
| material_name | VARCHAR(255) | NOT NULL | - |
| quantity | INTEGER | NOT NULL, CHECK (quantity > 0) | - |
| delivery_location | VARCHAR(255) | NOT NULL | - |
| status | VARCHAR(50) | NOT NULL, CHECK (status IN ('NEW', 'IN_PREPARATION', 'IN_TRANSIT', 'COMPLETED', 'DELETED')) | IDX |
| requester_id | UUID | FK to `users(id)`, NOT NULL | IDX |
| processor_id | UUID | FK to `users(id)`, NULL | IDX |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |

**Table: `audit_logs`**

| Column | Type | Constraints | Index |
|---|---|---|---|
| id | BIGSERIAL | PK, NOT NULL | PK |
| delivery_order_id | UUID | FK to `delivery_orders(id)`, NOT NULL | IDX |
| user_id | UUID | FK to `users(id)`, NOT NULL | IDX |
| action | VARCHAR(50) | NOT NULL | - |
| details | TEXT | NULL | - |
| timestamp | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | IDX |

### 3.3 Index Strategy
- **`delivery_orders(status)`**: To quickly query orders by their current state for the dashboards.
- **All Foreign Keys**: `requester_id`, `processor_id`, `delivery_order_id` must be indexed to ensure fast JOIN performance.
- **`audit_logs(timestamp)`**: To allow efficient time-based querying of the audit trail.

---

## 4. Data Integrity

### 4.1 Referential Integrity
- `delivery_orders.requester_id` refers to `users.id`. `ON DELETE RESTRICT` - a user cannot be deleted if they have orders.
- `delivery_orders.processor_id` refers to `users.id`. `ON DELETE SET NULL` - if a warehouse user is deleted, their assigned orders become unassigned.
- `audit_logs.delivery_order_id` refers to `delivery_orders.id`. `ON DELETE CASCADE` - if an order is purged, its audit logs are purged too.

### 4.2 Business Constraints
- `users.role` must be one of 'PRODUCTION', 'WAREHOUSE', or 'ADMIN'.
- `delivery_orders.status` must be one of 'NEW', 'IN_PREPARATION', 'IN_TRANSIT', 'COMPLETED', or 'DELETED'.
- `delivery_orders.quantity` must be greater than zero.

---

## 5. Data Migration

### 5.1 Migration Strategy
- **Tool:** Flyway will be integrated into the Spring Boot application.
- **Process:** Migrations will run automatically on application startup.
- **Naming:** `V<YYYYMMDDHHMM>__Description_in_snake_case.sql` (e.g., `V202310271000__Create_users_table.sql`).
- **Rule:** Migrations must be forward-only and non-destructive in production.

---

## 6. Audit Trail

The `audit_logs` table provides a complete, immutable history of every Delivery Order.
- An entry **MUST** be created in this table within the same database transaction as any change to a `delivery_orders` record.
- For `Admin` actions (manual edit/delete), the `details` column must contain the reason provided by the Admin.
