# Proposal: Shopfloor Material Supply System Backend API

**Ref:** Depends on `@openspec/changes/01-data-model`

This document defines the complete backend design: service layer logic, REST API contract, security model, DTOs, and error handling strategy.

---

## 1. Service Layer

### `DeliveryOrderService`

**Class:** `com.example.shopfloor.service.DeliveryOrderService`
**Responsibility:** Owns all business logic for creating, retrieving, and updating DeliveryOrders, including status lifecycle enforcement, role-based authorization, and audit event emission.

**Methods:**

| Method Signature | Description | Business Rules Applied |
|---|---|---|
| `DeliveryOrderDto createOrder(CreateDeliveryOrderDto dto, User currentUser)` | Creates a new order. | Initial Status, Role-Based Creation |
| `Page<DeliveryOrderDto> getOrders(User currentUser, Pageable pageable)` | Retrieves orders based on the user's role. | Role-Based Filtering |
| `DeliveryOrderDto getOrderById(UUID orderId, User currentUser)` | Retrieves a single order by its ID. | |
| `DeliveryOrderDto updateOrderStatus(UUID orderId, UpdateOrderStatusDto dto, User currentUser)` | Transitions an order to a new status. | Status Transition Guard, Role-Based Status Transition |
| `void deleteOrder(UUID orderId, String reason, User currentUser)` | Soft-deletes an order. | Soft Delete, Audit on Admin Action |

### `AuditLogService`

**Class:** `com.example.shopfloor.service.AuditLogService`
**Responsibility:** Creates immutable audit log entries. Called by other services.

**Methods:**

| Method Signature | Description |
|---|---|
| `void logAction(DeliveryOrder order, User actor, String action, String reason)` | Persists a new `AuditLog` row. |

---

## 2. Business Rules

1.  **Initial Status Assignment**
    - **When:** A new `DeliveryOrder` is created.
    - **Then:** `status` is always set to `PENDING`.
    - **Enforced in:** `DeliveryOrderService.createOrder()`

2.  **Status Transition Guard**
    - **When:** `updateOrderStatus()` is called.
    - **Then:** The new status must be in `currentStatus.getAllowedNextStates()`.
    - **Enforced in:** `DeliveryOrderService.updateOrderStatus()`

3.  **Role-Based Status Transition Restriction**
    - **When:** A user attempts a status transition.
    - **Then:**
        - `PENDING` -> `ACCEPTED`: Requires `WAREHOUSE_STAFF` role.
        - `ACCEPTED` -> `IN_TRANSIT`: Requires `WAREHOUSE_STAFF` role.
        - `IN_TRANSIT` -> `DELIVERED`: Requires `PRODUCTION_OPERATOR` role.
        - Any transition: `ADMINISTRATOR` role is permitted.
    - **Enforced in:** `DeliveryOrderService.updateOrderStatus()`

4.  **Soft Delete Only**
    - **When:** `deleteOrder()` is called.
    - **Then:** Set `isDeleted = true`. Do NOT issue a SQL DELETE.
    - **Enforced in:** `DeliveryOrderService.deleteOrder()`

5.  **Audit on Admin Action**
    - **When:** An `ADMINISTRATOR` calls `deleteOrder()` or `updateOrderStatus()`.
    - **Then:** Call `AuditLogService.logAction()`.
    - **Enforced in:** `DeliveryOrderService`

---

## 3. API Endpoints

**Base Path:** `/api`
**Authentication:** All endpoints require a valid JWT.

| Method | Path | Description | Request Body | Response | Auth Role(s) |
|---|---|---|---|---|---|
| `POST` | `/api/orders` | Creates a new delivery order. | `CreateDeliveryOrderDto` | `DeliveryOrderDto` (201) | `PRODUCTION_OPERATOR` |
| `GET` | `/api/orders` | Returns orders based on user's role. | `?page=0&size=20` | `Page<DeliveryOrderDto>` (200) | All Roles |
| `GET` | `/api/orders/{id}` | Returns a single order. | - | `DeliveryOrderDto` (200) | All Roles |
| `PATCH` | `/api/orders/{id}/status` | Updates the order's status. | `UpdateOrderStatusDto` | `DeliveryOrderDto` (200) | All Roles |
| `DELETE` | `/api/orders/{id}` | Soft-deletes an order. | `DeleteOrderDto` | 204 No Content | `ADMINISTRATOR` |
| `GET` | `/api/orders/{id}/history` | Returns the audit history for an order. | - | `List<AuditLogDto>` (200) | `ADMINISTRATOR` |


---

## 4. DTOs (Data Transfer Objects)

### `CreateDeliveryOrderDto` (Request)
```json
{
  "materialName": "string",
  "quantity": "integer",
  "unitOfMeasure": "string",
  "targetProductionLine": "string",
  "priority": "NORMAL | URGENT",
  "notes": "string (optional)"
}
```

### `UpdateOrderStatusDto` (Request)
```json
{
  "newStatus": "ACCEPTED | IN_TRANSIT | DELIVERED | CANCELLED",
  "reason": "string (optional, for admin actions)"
}
```

### `DeleteOrderDto` (Request)
```json
{
  "reason": "string (required)"
}
```

### `DeliveryOrderDto` (Response)
```json
{
  "id": "uuid",
  "materialName": "string",
  "quantity": "integer",
  "status": "PENDING | ...",
  "priority": "NORMAL | URGENT",
  "requester": { "id": "uuid", "username": "string" },
  "handler": { "id": "uuid", "username": "string" },
  "createdAt": "ISO-8601 datetime"
}
```

### `AuditLogDto` (Response)
```json
{
  "id": "uuid",
  "userId": "uuid",
  "action": "string",
  "details": { "reason": "string" },
  "createdAt": "ISO-8601 datetime"
}
```

---

## 5. Security Design

**Implementation:** Spring Security 6 + JWT

| Layer | Mechanism | Detail |
|---|---|---|
| Authentication | JWT Bearer token | Validated by a `JwtAuthFilter` on every request. |
| Authorization | `@PreAuthorize` on service methods | e.g., `@PreAuthorize("hasRole('ADMINISTRATOR')")` on `deleteOrder()`. |

**Role-Permission Matrix:**

| Action | `PRODUCTION_OPERATOR` | `WAREHOUSE_STAFF` | `ADMINISTRATOR` |
|---|---|---|---|
| Create Order | ✓ | ✗ | ✓ |
| View Own Orders | ✓ | ✗ | ✓ |
| View All Orders | ✗ | ✓ | ✓ |
| Accept Order | ✗ | ✓ | ✓ |
| Ship Order | ✗ | ✓ | ✓ |
| Receive Order | ✓ | ✗ | ✓ |
| Delete Order | ✗ | ✗ | ✓ |
| View Audit Logs | ✗ | ✗ | ✓ |

---

## 6. Error Handling

**Global handler:** `@RestControllerAdvice`

| Exception Class | HTTP Status |
|---|---|
| `ResourceNotFoundException` | 404 Not Found |
| `InvalidStatusTransitionException` | 409 Conflict |
| `AccessDeniedException` | 403 Forbidden |
| `MethodArgumentNotValidException` | 400 Bad Request |
| `AuthenticationException` | 401 Unauthorized |
