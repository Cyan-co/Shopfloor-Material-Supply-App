# Phase B: Business Architecture (BA)

## 1. Business Domain

### Domain
Shopfloor Material Supply Logistics

### Process description
This architecture defines the process of requesting, preparing, and delivering materials from a central warehouse to various production lines on a shopfloor. It covers the complete lifecycle of a "Delivery Order," from its creation by a production line user to its final receipt, including all intermediate state transitions and administrative oversight.

---

## 2. Business Actors & Roles

| Role | Responsibilities |
|---|---|
| **Production Line User** | - Creates new material supply requirements (Delivery Orders).<br>- Confirms the receipt of delivered materials. |
| **Warehouse User** | - Views a queue of new Delivery Orders.<br>- "Picks up" an order to begin processing.<br>- Prepares the materials for delivery.<br>- Marks the order as "In Transit" when it leaves the warehouse. |
| **Admin** | - Monitors the end-to-end status of all Delivery Orders.<br>- Can manually edit the status of any order.<br>- Can delete any order from the system. |

---

## 3. Business Process Flow (The Golden Path)

The lifecycle of a Delivery Order follows a strict, linear state transition model:

`NEW` -> `IN_PREPARATION` -> `IN_TRANSIT` -> `COMPLETED`

This represents the "golden path" or the ideal successful flow of an order. Administrative actions can interrupt or terminate this flow.

---

## 4. Business Rules

These rules are non-negotiable and must be enforced by the backend system logic.

### State Transition Constraints
- A Delivery Order can only be created in the `NEW` state.
- Only a **Warehouse User** can transition an order from `NEW` to `IN_PREPARATION`. This action assigns them as the order owner.
- Only the assigned **Warehouse User** can transition an order from `IN_PREPARATION` to `IN_TRANSIT`.
- Only the originating **Production Line User** can transition an order from `IN_TRANSIT` to `COMPLETED`.
- An order in a terminal state (`COMPLETED`, `DELETED`) cannot be further modified by any user except an Admin performing a correction.

### Role-Based Access Control (RBAC) Rules
- **Production Line User:** Can `CREATE` orders. Can `READ` their own orders. Can `UPDATE` an order's state to `COMPLETED` if it is `IN_TRANSIT`.
- **Warehouse User:** Can `READ` all `NEW` orders. Can `UPDATE` an order's state from `NEW` to `IN_PREPARATION` and from `IN_PREPARATION` to `IN_TRANSIT`.
- **Admin:** Has full `CREATE`, `READ`, `UPDATE`, `DELETE` (CRUD) permissions on all Delivery Orders at any stage.

### Immutability and Audit Rules
- Every state transition **MUST** be recorded in an immutable audit log.
- An **Admin** performing a manual `UPDATE` or `DELETE` action **MUST** provide a reason, which is stored in the audit log.
- The creation timestamp, original requestor, and assigned warehouse user cannot be changed after they are set.

### Exception Handling
- If an order is marked as `IN_TRANSIT` but never received, it remains in that state until an **Admin** manually intervenes to resolve its status (e.g., mark as `COMPLETED` or `CANCELLED`).
