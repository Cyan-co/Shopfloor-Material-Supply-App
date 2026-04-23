# Phase D: Technology Architecture (TA)

## 1. Infrastructure Overview

This document defines the infrastructure and operational standards for the Shopfloor Material Supply application. All environments must be managed via Infrastructure as Code (IaC), using tools like Terraform or Kubernetes YAML.

### Container Platform
- **Runtime:** Docker is the mandatory container runtime.
- **Orchestration:**
  - **Local/DEV:** Docker Compose will be used for simplicity and fast startup.
  - **STAGING/PROD:** Kubernetes is the mandatory orchestration platform for scalability and resilience.
- **Registry:** A private container registry (e.g., Docker Hub Private, GitHub Container Registry) will be used to store application images.

### Deployment Environments

| Environment | Purpose | Orchestration | Scaling |
|---|---|---|---|
| **DEV (Local)** | Individual developer environment | Docker Compose | 1 replica per service |
| **TEST** | Automated integration & E2E testing | Kubernetes | 1 replica per service |
| **STAGING** | Pre-production user acceptance testing (UAT) | Kubernetes | 2 replicas, production-like configuration |
| **PROD** | Live production environment | Kubernetes | 3+ replicas with Horizontal Pod Autoscaling (HPA) |

---

## 2. Network Architecture

### Ingress Configuration
- **Ingress Controller:** A standard Kubernetes Ingress controller (e.g., NGINX Ingress) will be used to manage external access to the services.
- **TLS Termination:** TLS certificates (e.g., from Let's Encrypt) will be managed by `cert-manager` and terminated at the Ingress layer. All external traffic must be encrypted (HTTPS).
- **DNS:** The application will be accessible via a standard domain, e.g., `shopfloor-supply.yourcompany.com`.

### Service Communication
- **Internal:** Services within the Kubernetes cluster will communicate using standard Kubernetes DNS service names (e.g., `http://backend-service:8080`).
- **Network Policies:** Kubernetes Network Policies **MUST** be implemented to enforce a "deny-by-default" posture. Policies will explicitly allow traffic only between required components (e.g., allow frontend to call backend API, allow backend to connect to database).

---

## 3. Database Infrastructure

### PostgreSQL Configuration
- **Version:** 15+
- **Deployment:** For PROD, the database will be deployed as a managed service (e.g., AWS RDS, Azure Database for PostgreSQL) or a stateful set in Kubernetes with persistent volume claims.
- **Backup Strategy:** Daily automated snapshots are mandatory, with Point-In-Time Recovery (PITR) enabled and a retention of at least 14 days.
- **Replication:** A master-slave replication setup with at least one read replica is required for the PROD environment to ensure high availability.

---

## 4. Security Infrastructure

### Authentication & Authorization
- **Protocol:** The system will use OAuth 2.0 with the Client Credentials or Authorization Code flow.
- **Identity Provider (IdP):** A centralized IdP like Keycloak is recommended. For V1, a simple identity service can be built, but it must issue signed JWTs.
- **Token Format:** JWT containing `user_id`, `role`, and an expiration (`exp`) claim.

### Secrets Management
- **Storage:** All secrets (database passwords, API keys, JWT signing keys) **MUST** be stored in Kubernetes Secrets. They must not be checked into source control.
- **Access:** Secrets will be mounted into pods as environment variables or files at runtime, following the principle of least privilege.

---

## 5. Monitoring & Observability

### Metrics
- **Tool:** A Prometheus instance will scrape metrics from all application components.
- **Dashboards:** Grafana will be used to visualize key metrics.
- **Required Metrics:**
  - **Application:** HTTP request rate, error rate, latency (p95, p99).
  - **JVM:** Heap usage, GC activity.
  - **Database:** Connection pool size, query latency.

### Logging
- **Tool:** The ELK Stack (Elasticsearch, Logstash, Kibana) or Loki will be used for centralized logging.
- **Log Format:** All application logs **MUST** be structured (JSON format) and written to `stdout`.
- **Required Fields:** `timestamp`, `log_level`, `service_name`, `trace_id`, `message`.

### Alerting
- Alerts will be configured in Prometheus's Alertmanager.
- **Critical Alerts:** High error rate (>5% in 5 min), service down, database connection failure.
- **Warning Alerts:** High latency (p99 > 1.5s), high CPU/memory usage.

---

## 6. CI/CD Pipeline

A GitHub Actions pipeline will automate the build, test, and deployment process.

### Pipeline Stages
```
On Push (main) -> Build -> Unit & Integration Test -> Security Scan -> Build & Push Docker Image -> Deploy to STAGING -> [Manual Approval] -> Deploy to PROD
```

### Stage Requirements

| Stage | Actions | Gate |
|---|---|---|
| **Build** | `mvn clean install`, `npm install && npm run build` | All unit tests pass with >80% code coverage. |
| **Security Scan** | SonarQube SAST analysis, Trivy dependency scan. | No new critical or high-severity vulnerabilities found. |
| **Deploy STAGING** | `kubectl apply -f k8s/staging` | Automatic upon successful scan. |
| **Deploy PROD** | `kubectl apply -f k8s/prod` | Requires manual approval from a project lead in the GitHub Actions environment. |

---

## 7. Disaster Recovery

### Backup Strategy
| Component | Frequency | Retention | RTO | RPO |
|---|---|---|---|---|
| Database | Daily Full, PITR enabled | 14 days | < 1 hour | < 5 minutes |
| K8s Config (IaC) | On change (Git) | Indefinite | < 30 minutes | N/A |

### Recovery Procedures
A runbook must be maintained detailing the step-by-step process for restoring the application and database from backups in a new region or cluster. The target Recovery Time Objective (RTO) is 2 hours.
