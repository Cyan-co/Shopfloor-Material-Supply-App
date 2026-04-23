# Tasks: Shopfloor App Backend API Implementation

**Paths:** All backend code under `backend/`.

## Service Layer (Go)

- [ ] Implement `internal/services/auth_service.go` with login and user creation logic.
- [ ] Implement `internal/services/order_service.go` with core business logic for order state transitions and ownership rules.
- [ ] Implement `internal/services/admin_service.go` for administrative overrides (edit, delete).
- [ ] Implement `internal/services/audit_service.go` to handle the creation of audit log entries for all significant events.
- [ ] Implement input validation for all service methods.

## Controller/Handler Layer (Go)

- [ ] Create API handlers in `internal/handlers/` for each endpoint defined in the proposal.
- [ ] Implement handler for `POST /api/auth/login`.
- [ ] Implement handlers for `POST /api/orders` and `GET /api/orders/*`.
- [ ] Implement handlers for order status updates: `PUT /api/orders/{id}/pickup`, `transit`, `receive`.
- [ ] Implement handlers for admin actions: `PUT /api/orders/{id}/status`, `DELETE /api/orders/{id}`.
- [ ] Define request and response structs (DTOs) for all API endpoints to decouple the API layer from the data model.

## Security

- [ ] Implement JWT-based authentication middleware to protect endpoints.
- [ ] Implement RBAC (Role-Based Access Control) middleware to enforce endpoint access rules based on user roles.

## Error Handling

- [ ] Create custom error types in `internal/errors/` for business logic failures (e.g., `ErrNotFound`, `ErrForbidden`, `ErrInvalidState`).
- [ ] Implement a global error handling middleware to catch service-layer errors and translate them into appropriate HTTP status codes and JSON responses.
