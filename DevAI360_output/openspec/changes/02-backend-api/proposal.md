# Proposal: Shopfloor Material Supply App Backend API

**Ref:** Depends on `@openspec/changes/01-data-model`

## 1. Service Layer

### AuthService
Handles user registration, login, and session management.
**Methods:**
- `Login(email, password)`: Authenticates a user and returns a JWT.
- `RegisterUser(name, email, password, role)`: Creates a new user (primarily for seeding/admin use).

### OrderService
Manages the core business logic for delivery orders.
**Methods:**
- `CreateOrder(requestorID, materialName, quantity, location)`: Creates a new order with `NEW` status.
- `GetNewOrders()`: Returns a list of all orders with `NEW` status.
- `GetOrdersByStatus(status)`: Returns orders matching a specific status.
- `GetOrderById(orderID)`: Retrieves a single order.
- `AssignOrder(orderID, processorID)`: Assigns a warehouse user to an order and sets status to `IN_PREPARATION`.
- `UpdateOrderStatus(orderID, newStatus, userID)`: Updates an order's status, enforcing business rules.
- `GetAllOrders()`: Returns all orders for the admin dashboard.

### AdminService
Handles administrator-specific actions that bypass standard workflows.
**Methods:**
- `EditOrderStatus(orderID, newStatus, adminID, reason)`: Manually changes an order's status and creates an audit log entry.
- `DeleteOrder(orderID, adminID, reason)`: Deletes an order and creates an audit log entry.

## 2. Business Rules

1. **Role-Based Access (RBAC)**:
   - **When**: Any API endpoint is accessed.
   - **Then**: A middleware checks the user's JWT to ensure their role (`PRODUCTION`, `WAREHOUSE`, `ADMIN`) is authorized for that endpoint.

2. **Order State Transitions**:
   - **When**: `UpdateOrderStatus` is called.
   - **Then**: The service must validate that the transition is legal (e.g., `NEW` -> `IN_PREPARATION`, `IN_PREPARATION` -> `IN_TRANSIT`, `IN_TRANSIT` -> `COMPLETED`). Invalid transitions (e.g., `NEW` -> `COMPLETED`) are rejected.

3. **Order Ownership**:
   - **When**: A user attempts to update an order.
   - **Then**: The service verifies the user's role and, if applicable, their relationship to the order (e.g., only the original requestor can mark it `COMPLETED`).

4. **Audit Logging**:
   - **When**: An order's status is changed, or an admin edits/deletes an order.
   - **Then**: An entry must be created in the `audit_logs` table detailing the change, the user who made it, and the reason (if applicable).

## 3. API Endpoints

| Method | Path | Description | Auth Required |
|--------|------|-------------|---------------|
| POST | /api/auth/login | Authenticate a user | No |
| POST | /api/orders | Create a new delivery order | Yes (`PRODUCTION`) |
| GET | /api/orders | Get orders based on user role and status query | Yes (`PRODUCTION`, `WAREHOUSE`) |
| GET | /api/orders/all | Get all orders, any status | Yes (`ADMIN`) |
| GET | /api/orders/{id} | Get a single order by ID | Yes (All roles) |
| PUT | /api/orders/{id}/pickup | Pick up a new order | Yes (`WAREHOUSE`) |
| PUT | /api/orders/{id}/transit | Mark an order as in transit | Yes (`WAREHOUSE`) |
| PUT | /api/orders/{id}/receive | Mark an order as completed | Yes (`PRODUCTION`) |
| PUT | /api/orders/{id}/status | Manually edit an order's status | Yes (`ADMIN`) |
| DELETE | /api/orders/{id} | Delete an order | Yes (`ADMIN`) |

## 4. Error Handling

- `400 Bad Request`: Validation error on request body (e.g., missing fields).
- `401 Unauthorized`: User is not authenticated (missing or invalid JWT).
- `403 Forbidden`: User is authenticated but their role is not authorized for the action.
- `404 Not Found`: The requested resource (e.g., order) does not exist.
- `409 Conflict`: Business rule violation (e.g., invalid status transition).
