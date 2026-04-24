# Tasks: Shopfloor Material Supply App Data Model Implementation

**Paths:** Backend code under `backend/`. Frontend code under `frontend/`.

## Backend (Go)

- [ ] Create database migration script (e.g., using `golang-migrate`) for the `users`, `delivery_orders`, and `audit_logs` tables.
- [ ] Define `User` struct in `internal/models/user.go` with appropriate tags for database mapping (e.g., `gorm`).
- [ ] Define `DeliveryOrder` struct in `internal/models/delivery_order.go`.
- [ ] Define `AuditLog` struct in `internal/models/audit_log.go`.
- [ ] Define enums for `UserRole` and `OrderStatus` in a shared constants file.
- [ ] Implement database repository/store for `User` to handle user creation and retrieval.
- [ ] Implement database repository/store for `DeliveryOrder` to handle CRUD operations and status transitions.
- [ ] Implement database repository/store for `AuditLog` to create new log entries.

## Frontend (React)

- [ ] Create TypeScript interface `User` in `src/types/user.ts`.
- [ ] Create TypeScript interface `DeliveryOrder` in `src/types/deliveryOrder.ts`.
- [ ] Create TypeScript enum `UserRole` in `src/types/enums.ts`.
- [ ] Create TypeScript enum `OrderStatus` in `src/types/enums.ts`.
