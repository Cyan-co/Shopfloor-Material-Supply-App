# Tasks: Shopfloor Material Supply App Frontend Implementation

**Paths:** All frontend code under `frontend/`.

## Views/Pages

- [ ] Create `LoginPage.tsx` with the `LoginForm` component.
- [ ] Create `DashboardPage.tsx` that conditionally renders child components based on the authenticated user's role.
- [ ] Implement client-side routing (e.g., using React Router) for protected routes.

## Components

- [ ] Build `Navbar.tsx` component with title and logout button.
- [ ] Build `LoginForm.tsx` component with form handling and validation.
- [ ] Build `CreateOrderForm.tsx` component.
- [ ] Build a generic `OrderList.tsx` component that takes an array of orders as a prop.
- [ ] Build `OrderListItem.tsx` component to display a single order and its role-specific action buttons.
- [ ] Build `StatusBadge.tsx` to visually represent order statuses.
- [ ] Build `AdminOrderControls.tsx` for search and filter functionality.

## Services

- [ ] Create an `apiService.ts` (e.g., using Axios) to handle all HTTP requests to the backend. It should manage auth tokens in headers.
- [ ] Implement state management (e.g., using React Context or Redux/Zustand) to manage user session and global state.
- [ ] Create a `useOrders.ts` hook to encapsulate the logic for fetching and managing order data.

## Styling

- [ ] Set up a global CSS framework or component library (e.g., Material-UI, Tailwind CSS).
- [ ] Ensure the application layout is responsive and usable on mobile/tablet devices as per NFRs.

## Integration

- [ ] Configure a proxy in the development environment to forward API requests from the React app to the backend server.
- [ ] Implement UI feedback for API calls (e.g., loading spinners, success/error notifications).
