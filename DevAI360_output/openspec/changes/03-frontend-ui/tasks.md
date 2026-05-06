# Tasks: Shopfloor Material Supply System Frontend Implementation

**Paths:** All frontend code under `frontend/src/app/`.

---

## Views / Pages

- [ ] **Task 1: Generate and Implement `LoginComponent`**
  - `ng generate component components/login`
  - Build a reactive form for username and password.
  - On submit, call `AuthService.login()` and handle success (navigate to dashboard) and error (display message) responses.

- [ ] **Task 2: Generate and Implement `OrderDashboardComponent`**
  - `ng generate component components/order-dashboard`
  - Fetch and display orders based on the user's role.
  - Implement conditional rendering for action buttons (`Accept`, `In Transit`, `Receive`) based on order status and user role.
  - Set up polling to refresh the order list every 30 seconds.

- [ ] **Task 3: Generate and Implement `CreateOrderFormComponent`**
  - `ng generate component components/create-order-form`
  - Build a reactive form with validation for creating a new order.
  - On submit, call `OrderApiService.createOrder()` and navigate back to the dashboard on success.

- [ ] **Task 4: Generate and Implement `AdminDashboardComponent`**
  - `ng generate component components/admin-dashboard`
  - Display all orders in a table.
  - Implement controls for admins to manually update status or delete orders.

- [ ] **Task 5: Generate and Implement `AuditLogComponent`**
  - `ng generate component components/audit-log`
  - Fetch and display the audit history for a specific order.

---

## Shared / Reusable Components

- [ ] **Task 6: Generate and Implement `StatusBadgeComponent`**
  - `ng generate component shared/components/status-badge`
  - Create a component that takes an order status as input and displays a color-coded badge.

- [ ] **Task 7: Generate and Implement `ConfirmDialogComponent`**
  - `ng generate component shared/components/confirm-dialog`
  - Create a reusable modal dialog for confirming actions like deletion.

- [ ] **Task 8: Generate `NavbarComponent`**
  - `ng generate component layout/navbar`
  - Implement a navigation bar with links that are conditionally displayed based on user role.

---

## Services

- [ ] **Task 9: Create `AuthService`**
  - `ng generate service services/auth`
  - Implement methods for `login`, `logout`, `storeToken`, `getToken`, and `getCurrentUser`.

- [ ] **Task 10: Create `OrderApiService`**
  - `ng generate service services/order-api`
  - Implement methods for all order-related API calls (`getOrders`, `createOrder`, `updateOrderStatus`, etc.).

- [ ] **Task 11: Create `AdminApiService`**
  - `ng generate service services/admin-api`
  - Implement method to fetch audit logs.

---

## HTTP Interceptor & Routing

- [ ] **Task 12: Create `JwtInterceptor`**
  - `ng generate interceptor interceptors/jwt`
  - Automatically attach the JWT to the `Authorization` header of outgoing API requests.

- [ ] **Task 13: Configure Angular Router**
  - Set up all application routes in `app-routing.module.ts`, including guards.

- [ ] **Task 14: Implement `AuthGuard` and `RoleGuard`**
  - `ng generate guard guards/auth` and `ng generate guard guards/role`
  - Protect routes based on authentication status and user role.
