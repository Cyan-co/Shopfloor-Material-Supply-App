# Deployment Plan

## Prerequisites
- Docker and Docker Compose installed on the deployment server.
- Access to a container registry (e.g., Docker Hub, GitHub Container Registry).
- A configured PostgreSQL database, with connection details available.

## Step 1: Database Setup
- **Action**: Run database migrations.
- **Details**: The backend service will automatically apply database migrations on startup using `golang-migrate/migrate`. The migration files are located in the `/migrations` directory of the backend source code.
- **Verification**: The `users`, `delivery_orders`, and `audit_logs` tables are created in the database with the correct schemas.

## Step 2: Backend Deployment
- **Build**:
  1. Build the Go application into a static binary.
  2. Build a Docker image containing the binary.
     ```bash
     docker build -t your-registry/shopfloor-backend:latest .
     ```
- **Push**:
  ```bash
  docker push your-registry/shopfloor-backend:latest
  ```
- **Deploy**:
  - The backend will be deployed as a Docker container.
  - Environment variables must be configured for the database connection, JWT secret, and other settings.

## Step 3: Frontend Deployment
- **Build**:
  1. Build the React application for production.
     ```bash
     npm run build
     ```
  2. Build a Docker image using a multi-stage `Dockerfile` with Nginx to serve the static files.
     ```bash
     docker build -t your-registry/shopfloor-frontend:latest .
     ```
- **Push**:
  ```bash
  docker push your-registry/shopfloor-frontend:latest
  ```
- **Deploy**:
  - The frontend will be deployed as a Docker container running Nginx.
  - Nginx will be configured to serve the React app and proxy `/api` requests to the backend service.

## Step 4: Integration
- A `docker-compose.yml` file will be used to orchestrate the backend, frontend, and database services.
- The frontend container will be configured to route API requests to the backend container.

## Verification
- **Health Checks**: Access the `/health` endpoint on the backend to ensure it is running.
- **UI Access**: Open the application in a browser and verify that the login page loads.
- **End-to-End Test**:
  1. Log in as a `PRODUCTION` user.
  2. Create a new order.
  3. Log in as a `WAREHOUSE` user and verify the new order is visible.
  4. Complete the order lifecycle and verify the status updates at each stage.
```