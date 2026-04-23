# Frontend UI Implementation Tasks

This document lists the tasks required to implement the frontend UI for the Shopfloor Material Supply App.

- [ ] **Task 1: Project Setup**
  - **Description**: Initialize a new React project using Create React App with the TypeScript template.
  - **Acceptance Criteria**: A new React application is created and can be started locally.

- [ ] **Task 2: Implement API Service**
  - **Description**: Create an API service layer (`api.ts`) that encapsulates all `fetch` or `axios` calls to the backend. It should handle attaching the JWT to headers.
  - **Acceptance Criteria**: The API service has methods for all required backend endpoints.

- [ ] **Task 3: Implement State Management**
  - **Description**: Set up the global state management for user authentication and session information.
  - **Acceptance Criteria**: User data and token are stored securely upon login and cleared on logout.

- [ ] **Task 4: Implement Routing**
  - **Description**: Set up client-side routing using `react-router-dom`. Implement protected routes that redirect to the login page if the user is not authenticated.
  - **Acceptance Criteria**: All routes are defined, and protected routes are inaccessible to unauthenticated users.

- [ ] **Task 5: Build Common Components**
  - **Description**: Create the set of reusable components like `LoginPage`, `Navbar`, `OrderCard`, and `CreateOrderForm`.
  - **Acceptance Criteria**: Components are implemented, styled, and ready for use in the main views.

- [ ] **Task 6: Build Role-Based Dashboards**
  - **Description**: Create the main dashboard views for each user role (`PRODUCTION`, `WAREHOUSE`, `ADMIN`).
  - **Acceptance Criteria**: Each dashboard correctly displays the components and data relevant to the user's role.

- [ ] **Task 7: Implement Role-Based UI Logic**
  - **Description**: Add the logic to conditionally render UI elements (like buttons) based on the user's role and the state of the data.
  - **Acceptance Criteria**: The UI dynamically adapts to the logged-in user, showing only the actions they are permitted to perform.

- [ ] **Task 8: Make Application Responsive**
  - **Description**: Ensure the application is usable on a variety of screen sizes, particularly tablets and mobile devices.
  - **Acceptance Criteria**: The layout adjusts gracefully to different viewport sizes without breaking or losing functionality.
```