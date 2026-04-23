# Phase G: Implementation Governance

## 1. Governance Overview

### Governance Objectives
- **Ensure Compliance:** Guarantee that the implemented code and infrastructure strictly adhere to the architecture defined in Phases A through F.
- **Prevent Architectural Drift:** Use automated checks to prevent gradual deviation from the established patterns and principles.
- **Enable Controlled Evolution:** Provide a clear process for making deliberate, well-documented changes to the architecture when necessary.

### Governance Scope
| Area | Governed By |
|---|---|
| Code Structure | Automated architecture tests (ArchUnit) & static analysis. |
| API Design | OpenAPI contract testing in the CI/CD pipeline. |
| Data Model | Flyway migration validation; schema linting. |
| Infrastructure | Infrastructure as Code (IaC) validation and policy checks. |
| Security | Automated SAST, DAST, and dependency scanning in the CI/CD pipeline. |

---

## 2. Architecture Compliance Criteria

### 2.1 Code Architecture Compliance
| Criterion | Rule | Verification Method |
|---|---|---|
| Layer Separation | Controllers **MUST NOT** access Repositories directly. Business logic **MUST** be in the Service layer. | ArchUnit tests integrated into the build process. |
| Naming Conventions | Java classes and packages must adhere to the conventions set in Phase C. | Static analysis tools (e.g., Checkstyle, PMD). |
| No Circular Dependencies | The build must fail if circular dependencies between packages are detected. | Maven Dependency Plugin or similar tools. |

### 2.2 API Compliance
| Criterion | Rule | Verification Method |
|---|---|---|
| API Contract | The running application's API **MUST** match the `openapi.spec` file committed to the repository. | Automated contract testing during the integration test phase. |
| Security | All API endpoints (except for a public health check) **MUST** require authentication. | Automated security scan (DAST) in the CI/CD pipeline. |

### 2.3 Data Compliance
| Criterion | Rule | Verification Method |
|---|---|---|
| Schema Changes | All database schema changes **MUST** be performed via a Flyway migration script. Manual changes are forbidden. | CI/CD pipeline validates Flyway scripts. |
| Data Integrity | All foreign keys **MUST** be indexed. | A script that runs during the test phase to analyze the schema. |

---

## 3. Automated Enforcement

### 3.1 CI/CD Pipeline Gates
The CI/CD pipeline is the primary mechanism for enforcing governance. A merge to `main` is blocked if any of these gates fail.

| Gate | Stage | Failure Action |
|---|---|---|
| **Unit & Arch Tests** | Build | **Block Merge** |
| **SonarQube Quality Gate** | Build | **Block Merge** |
| **SAST & Dependency Scan** | Build | **Block Merge** (if critical vulnerabilities found) |
| **Integration & Contract Tests**| Test | **Block Deployment** to STAGING |

### 3.2 Quality Gates (SonarQube)
| Metric | Threshold |
|---|---|
| Code Coverage | >= 80% on new code |
| Duplications | <= 3% on new code |
| Maintainability Rating | 'A' on new code |
| Critical Vulnerabilities | 0 |

### 3.3 Architecture Fitness Functions (Example)
The following ArchUnit tests **MUST** be included in the backend codebase to enforce layering.
```java
// This code is illustrative and will be implemented in the project's test sources.

// Enforces that controllers do not directly access the database layer.
@ArchTest
static final ArchRule controllers_should_not_access_repositories =
    noClasses().that().resideInAPackage("..controller..")
        .should().accessClassesThat().resideInAPackage("..repository..");

// Enforces business logic is not in the controller layer.
@ArchTest
static final ArchRule services_should_be_the_only_classes_accessing_repositories =
    classes().that().resideInAPackage("..service..")
        .should().accessClassesThat().resideInAPackage("..repository..");
```

---

## 4. Review Processes

### 4.1 Code Review Requirements
- **Standard Changes:** Require at least **one** peer developer approval.
- **API or DB Schema Changes:** Require approval from the **Tech Lead**.
- **Architectural Changes:** Require an approved Architecture Decision Record (ADR) and approval from the **Solution Architect**.

### 4.2 Architecture Decision Records (ADR)
An ADR is mandatory for:
- Introducing a new library, framework, or third-party service.
- Changing an established architecture pattern (e.g., modifying the layering).

---

## 5. Exception Handling

### 5.1 Exception Request Process
1.  **Request:** The developer creates an ADR document outlining the need for an exception, the justification, and the associated risks.
2.  **Review:** The Tech Lead and Solution Architect review the ADR.
3.  **Decision:** The decision (Approved/Rejected) is recorded in the ADR.
4.  **Implementation:** If approved, the change is implemented and the ADR number is referenced in the pull request.

---

## 6. Roles and Responsibilities

| Role | Governance Responsibilities |
|---|---|
| **Developer** | Writes code compliant with standards; fixes violations identified by the pipeline. |
| **Tech Lead** | Conducts code reviews for architectural compliance; mentors developers on standards. |
| **Solution Architect** | Defines and maintains the architecture and governance rules; reviews and approves exceptions. |
| **DevOps Engineer** | Implements and maintains the automated governance gates within the CI/CD pipeline. |
