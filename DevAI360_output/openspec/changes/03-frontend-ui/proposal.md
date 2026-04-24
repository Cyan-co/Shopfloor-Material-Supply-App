# Proposal: Shopfloor Material Supply App Frontend UI

**Ref:** Depends on `@openspec/changes/01-data-model`, `@openspec/changes/02-backend-api`

## 1. View Architecture

### Login View
- **Purpose**: Secure user authentication.
- **User Roles**: All (unauthenticated).
- **Layout**: Centered form with fields for username and password.

### Dashboard View (Role-Based)
- **Purpose**: Main workspace for users after logging in. The content is dynamically rendered based on user role.
- **User Roles**: All (authenticated).
- **Layout**: Top navigation bar with app title and logout button. Main content area displays one of the role-specific dashboards.

#### Production Line Dashboard
- A "Create New Order" form.
- A list/table of orders created by the current user, showing their status.

#### Warehouse Dashboard
- A list/table of all orders with "New" status, each with a "Pick Up" button.
- A list/table of orders assigned to the current user with "In Preparation" status, each with a "Mark as In Transit" button.

#### Admin Dashboard
- A comprehensive table of all orders in the system.
- Search and filter controls (by status, date, material).
- "Edit" and "Delete" buttons available for each order.

## 2. Component Requirements

| Component | Purpose | Data Source |
|---|---|---|
| `LoginForm` | Handles user authentication. | `/api/v1/auth/login` |
| `OrderList` | Displays a list of orders. Reusable for all roles. | `/api/v1/orders` |
| `OrderListItem` | Renders a single order row with role-specific action buttons. | N/A (props) |
| `CreateOrderForm` | Form for Production Line Users to create new orders. | `/api/v1/orders` |
| `AdminOrderControls` | Search and filter inputs for the Admin dashboard. | `/api/v1/orders` |
| `StatusBadge` | Displays order status with a distinct color. | N/A (props) |
| `Navbar` | Top-level navigation and logout functionality. | `/api/v1/auth/logout` |

## 3. User Interactions

### Create Order
- **Trigger**: Production Line User clicks "Submit" on the `CreateOrderForm`.
- **Action**: A `POST` request is sent to `/api/v1/orders`.
- **Feedback**: The form is cleared, and the new order appears in the user's order list. A success notification is shown.

### Update Order Status (e.g., Pick Up)
- **Trigger**: Warehouse User clicks the "Pick Up" button on a `NEW` order.
- **Action**: A `PUT` request is sent to `/api/v1/orders/{id}/pickup`.
- **Feedback**: The button is disabled, and the order moves from the "New" list to the "In Preparation" list.

## 4. Role-based Visibility

| Element/Action | `PRODUCTION_LINE` | `WAREHOUSE` | `ADMIN` |
|---|---|---|---|
| Create Order Form | ✓ | ✗ | ✗ |
| "Pick Up" Button | ✗ | ✓ (on `NEW` orders) | ✗ |
| "In Transit" Button | ✗ | ✓ (on own `IN_PREP` orders) | ✗ |
| "Receive" Button | ✓ (on own `IN_TRANSIT` orders) | ✗ | ✗ |
| View All Orders | ✗ | ✗ | ✓ |
| Edit/Delete Buttons | ✗ | ✗ | ✓ |

## 5. API Integration

| Component | Endpoint | Method | Purpose |
|---|---|---|---|
| `LoginForm` | `/api/v1/auth/login` | POST | Authenticate user. |
| `OrderList` | `/api/v1/orders` | GET | Fetch orders relevant to the user's role. |
| `CreateOrderForm` | `/api/v1/orders` | POST | Create a new delivery order. |
| `OrderListItem` | `/api/v1/orders/{id}/...` | PUT | Perform status update actions (pickup, transit, receive). |
| `AdminOrderControls`| `/api/v1/orders` | GET | Fetch orders with search/filter query parameters. |
