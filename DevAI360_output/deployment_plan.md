# Deployment Plan: Shopfloor Material Supply App

This document outlines the steps to deploy the Shopfloor Material Supply App, which consists of a Go backend, a React frontend, and a PostgreSQL database.

## 1. Prerequisites

- A provisioned PostgreSQL database with connection details (host, port, user, password, database name).
- A server environment (e.g., a VM or Kubernetes cluster) to run the backend and frontend applications.
- Docker and Docker Compose installed for containerized deployment.

## 2. Environment Configuration

- **Backend (`backend/.env`)**:
  - `DATABASE_URL`: The connection string for the PostgreSQL database.
  - `JWT_SECRET`: A strong, randomly generated secret for signing JWTs.
- **Frontend (`frontend/.env`)**:
  - `REACT_APP_API_BASE_URL`: The public URL of the backend API (e.g., `https://api.shopfloor.example.com`).

## 3. Deployment Steps

### Step 1: Database Migrations
1.  The Go backend will use a library like `golang-migrate/migrate`.
2.  The migrations are stored in the `backend/migrations` directory.
3.  Upon application startup, the backend will automatically check for and apply any pending database migrations to ensure the schema is up to date.

### Step 2: Backend Deployment (Go)
1.  Build a static Go binary: `go build -o main ./cmd/server`.
2.  Create a Docker image for the backend service. The Dockerfile will copy the binary and the `.env` configuration file.
3.  Push the Docker image to a container registry.
4.  Deploy the container to the server environment, exposing the API port (e.g., 8080).

### Step 3: Frontend Deployment (React)
1.  Build the static frontend assets: `npm run build`. This will create an optimized set of HTML, CSS, and JavaScript files in the `frontend/build` directory.
2.  Create a Docker image for the frontend using a lightweight web server like Nginx. The Dockerfile will copy the contents of the `frontend/build` directory to the Nginx web root.
3.  Push the Docker image to a container registry.
4.  Deploy the container to the server environment, exposing the web port (e.g., 80).

## 4. Verification

1.  **Backend Health**: Access the backend's `/health` endpoint to confirm it is running and can connect to the database.
2.  **Frontend Access**: Open the application's URL in a web browser. The login page should load correctly.
3.  **End-to-End Test**:
    - Log in with a test `PRODUCTION_LINE` user.
    - Create a new delivery order.
    - Verify the order appears in the database with the `NEW` status.
    - Log in as a `WAREHOUSE` user and complete the order lifecycle.
    - Confirm all state changes are correctly recorded in the `audit_logs` table.
