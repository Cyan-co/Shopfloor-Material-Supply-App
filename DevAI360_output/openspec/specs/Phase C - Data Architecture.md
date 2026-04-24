# Phase C: Data Architecture (DA)

## 1. Data Architecture Overview

### Data Principles
- **Single Source of Truth**: The PostgreSQL database is the definitive source for all application data.
- **Data Integrity at the Core**: Constraints (NOT NULL, UNIQUE, FOREIGN KEY) will be enforced at the database level, not just the application level.
- **Immutable Audit Trail**: All state changes to critical business entities (Orders) must be captured in an immutable audit log.
- **Soft Deletes by Default**: Business-critical data (Orders) will be soft-deleted to ensure recoverability and maintain historical integrity.

### Technology Stack
- **Database**: PostgreSQL 15+
- **ORM**: Spring Data JPA
- **Migrations**: Flyway for managing schema evolution.
- **Data Format**: JSON for API payloads, ISO 8601 for all timestamps (TIMESTAMPTZ).

---

## 2. Logical Data Model

### 2.1 Core Entities

| Entity | Description |
|---|---|
| **User** | Represents an actor in the system (Production Line, Warehouse, Admin). Stores credentials and role information. |
| **Order** | The central entity representing a material supply request. It moves through the defined business process flow. |
| **OrderAuditLog** | An immutable record of every state change or significant modification made to an Order. |

### 2.2 Entity Relationships

```
User (1) ----< (N) Order (created_by)
User (1) ----< (N) Order (assigned_to)
Order (1) ----< (N) OrderAuditLog
```

### 2.3 Entity Attributes

**Entity: User**
| Attribute | Type | Nullable | Description |
|---|---|---|---|
| id | UUID | No | Primary key |
| email | String | No | User's login email (must be unique) |
| password_hash | String | No | Hashed password |
| role | String | No | User's role ('PRODUCTION_LINE', 'WAREHOUSE', 'ADMIN') |
| created_at | Timestamp | No | Timestamp of user creation |
| updated_at | Timestamp | No | Timestamp of last user update |

**Entity: Order**
| Attribute | Type | Nullable | Description |
|---|---|---|---|
| id | UUID | No | Primary key |
| material_name | String | No | Name of the material being requested |
| quantity | Integer | No | Amount of material requested |
| delivery_location | String | No | Shopfloor location for delivery |
| status | String | No | Current status of the order ('NEW', 'IN_PREPARATION', 'IN_TRANSIT', 'COMPLETED') |
| created_by_user_id | UUID | No | Foreign key to the User who created the order |
| assigned_to_user_id | UUID | Yes | Foreign key to the User who is processing the order |
| is_deleted | Boolean | No | Flag for soft deletes |
| created_at | Timestamp | No | Timestamp of order creation |
| updated_at | Timestamp | No | Timestamp of last order update |

**Entity: OrderAuditLog**
| Attribute | Type | Nullable | Description |
|---|---|---|---|
| id | UUID | No | Primary key |
| order_id | UUID | No | Foreign key to the Order being audited |
| user_id | UUID | No | Foreign key to the User who performed the action |
| action | String | No | The action performed (e.g., 'STATUS_CHANGE', 'DELETE') |
| old_value | String | Yes | The value before the change |
| new_value | String | No | The value after the change |
| reason | String | Yes | Reason for change (mandatory for admin actions) |
| timestamp | Timestamp | No | Timestamp of the audit event |

---

## 3. Physical Data Model

### 3.1 Database Schema
Schema name: `material_supply`

### 3.2 Table Definitions

**Table: users**
| Column | Type | Constraints | Index |
|---|---|---|---|
| id | UUID | PK, NOT NULL | PK |
| email | VARCHAR(255) | NOT NULL, UNIQUE | UQ |
| password_hash | VARCHAR(255) | NOT NULL | - |
| role | VARCHAR(50) | NOT NULL, CHECK (role IN ('PRODUCTION_LINE', 'WAREHOUSE', 'ADMIN')) | IDX |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |

**Table: orders**
| Column | Type | Constraints | Index |
|---|---|---|---|
| id | UUID | PK, NOT NULL | PK |
| material_name | VARCHAR(255) | NOT NULL | - |
| quantity | INTEGER | NOT NULL, CHECK (quantity > 0) | - |
| delivery_location | VARCHAR(255) | NOT NULL | - |
| status | VARCHAR(50) | NOT NULL, CHECK (status IN ('NEW', 'IN_PREPARATION', 'IN_TRANSIT', 'COMPLETED')) | IDX |
| created_by_user_id | UUID | NOT NULL, FK to users(id) | FK, IDX |
| assigned_to_user_id | UUID | NULL, FK to users(id) | FK, IDX |
| is_deleted | BOOLEAN | NOT NULL, DEFAULT FALSE | IDX |
| created_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |
| updated_at | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |

**Table: order_audit_logs**
| Column | Type | Constraints | Index |
|---|---|---|---|
| id | UUID | PK, NOT NULL | PK |
| order_id | UUID | NOT NULL, FK to orders(id) | FK, IDX |
| user_id | UUID | NOT NULL, FK to users(id) | FK, IDX |
| action | VARCHAR(50) | NOT NULL | - |
| old_value | VARCHAR(255) | NULL | - |
| new_value | VARCHAR(255) | NOT NULL | - |
| reason | TEXT | NULL | - |
| timestamp | TIMESTAMPTZ | NOT NULL, DEFAULT NOW() | - |

### 3.3 Naming Conventions
As specified in the prompt.

### 3.4 Index Strategy
- **Primary Keys**: Automatically indexed.
- **Foreign Keys**: All FK columns (`created_by_user_id`, `assigned_to_user_id`, `order_id`, `user_id`) will have B-tree indexes.
- **Query Performance**:
    - `orders(status)`: To quickly filter orders by their current state for the dashboards.
    - `orders(is_deleted)`: To efficiently exclude soft-deleted records from queries.
    - `users(email)`: Unique index to enforce email uniqueness and speed up logins.

---

## 4. Data Integrity
- **Referential Integrity**: All `FOREIGN KEY` constraints will use `ON DELETE RESTRICT` to prevent accidental deletion of parent records (e.g., cannot delete a user who still has orders).
- **Business Constraints**: `CHECK` constraints will be used on `role` and `status` columns to enforce allowed values at the database level.
- **Validation Rules**: Application-level validation will enforce rules like password complexity and string length before data reaches the database.

---

## 5. Data Lifecycle
- **Data States**: ACTIVE, DELETED. Archival is out of scope for V1.
- **Retention Policies**:
    - Soft-deleted (`is_deleted = true`) orders will be purged from the database by a scheduled job after 365 days.
    - Audit logs will be retained for 7 years.

---

## 6. Audit Trail
The `order_audit_logs` table provides a complete, immutable history of all significant events for every order. It is populated by application-level triggers within the `OrderService` for every state-changing operation.

---

## 7. Data Migration
- **Tool**: Flyway
- **Strategy**: All schema changes MUST be managed through versioned SQL migration scripts.
- **Naming**: `V{YYYYMMDDHHMMSS}__{Description}.sql` (e.g., `V20231027103000__Create_users_table.sql`).

---

## 8. Data Security
- **Sensitive Data**: User passwords will be hashed using `bcrypt`.
- **Access Control**: Database access will be limited to the application's service account with the minimum required privileges. Direct database access for developers will be read-only in production environments.
- **Encryption**: Data-at-rest encryption will be enabled at the PostgreSQL instance level. All connections to the database will use TLS.
