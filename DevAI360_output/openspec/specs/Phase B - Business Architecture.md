# Phase B: Business Architecture (BA)

## 1. Business Domain

- **Domain**: Shop Floor Operations - Material Logistics Management.
- **Process description**: This document models the business process for requesting, fulfilling, and delivering material supplies from a warehouse to a production line. The process is initiated by a production operator, fulfilled by warehouse staff, and overseen by administrators. It is designed to be a closed-loop, trackable workflow ensuring that every request is accounted for from initiation to completion.

---

## 2. Business Actors & Roles

| Role | Responsibilities |
| :--- | :--- |
| **Production Operator** | - Creates new material supply requests (orders).<br>- Monitors the status of their active requests.<br>- Confirms the receipt of delivered materials to complete the order. |
| **Warehouse Staff** | - Views the queue of pending material requests.<br>- Accepts and takes ownership of a request for fulfillment.<br>- Updates the order status as it is being prepared and when it is in transit.<br>- Delivers the material to the production line. |
| **Administrator** | - Has full oversight of all orders in the system.<br>- Can manually edit the status of any order to resolve exceptions.<br>- Can delete orders (soft delete) if they are created in error.<br>- Manages user roles and system settings. |

---

## 3. Business Process Flow (The Golden Path)

The lifecycle of a material supply order follows a strict, linear progression:

`PENDING` -> `ACCEPTED` -> `IN_TRANSIT` -> `DELIVERED`

- **PENDING**: A new order is created by a Production Operator and is awaiting action from the warehouse.
- **ACCEPTED**: A Warehouse Staff member has claimed the order and is preparing the materials.
- **IN_TRANSIT**: The materials have been prepared and are being transported to the production line.
- **DELIVERED**: The Production Operator has confirmed receipt of the materials, and the order is complete.

---

## 4. Business Rules

These rules are mandatory and must be enforced by the backend business logic.

| ID | Rule | Implementation Constraint |
| :-- | :-- | :-- |
| **BR-01** | An order can only be accepted by Warehouse Staff if it is in the `PENDING` state. | The `acceptOrder()` service method must first verify the current status is `PENDING`. |
| **BR-02** | An order can only be marked `IN_TRANSIT` by the assigned Warehouse Staff if it is in the `ACCEPTED` state. | The `markInTransit()` service method must verify the current status is `ACCEPTED` and that the caller is the assigned user. |
| **BR-03** | An order can only be marked `DELIVERED` by a Production Operator if it is in the `IN_TRANSIT` state. | The `confirmDelivery()` service method must verify the current status is `IN_TRANSIT`. |
| **BR-04** | The core order details (material name, quantity, target line) are immutable after creation. | The update/edit service methods must not allow modification of these fields for existing orders. |
| **BR-05** | Only an Administrator can edit the status of an order outside the standard flow. | An `adminEditOrder()` endpoint must be created and protected by an Admin role check. All such edits must generate an audit log entry with a reason. |
| **BR-06** | Only an Administrator can delete (soft delete) an order. | A `deleteOrder()` endpoint must be protected by an Admin role check and must set an `is_active` flag to `false` rather than performing a hard delete. |
| **BR-07** | A Warehouse Staff member cannot accept a new order if they already have an active (`ACCEPTED` or `IN_TRANSIT`) order. | The `acceptOrder()` method must check if the user has other active orders before proceeding. (This is a sample rule that might be clarified with the business). |

---
