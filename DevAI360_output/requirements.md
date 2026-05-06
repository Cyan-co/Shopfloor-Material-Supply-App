# Software Requirements Specification (SRS)
## for
# Shopfloor Material Supply System
| | |
| :- | :- |
| **Version** | 1.0 |
| **Date** | 2024-07-30 |
| **Prepared by** | DevAI360 Business Consultant |
| **Project Name** | Shopfloor Material Supply System |

## Revision History
| Version | Date | Author | Description |
| :- | :- | :- | :- |
| 1.0 | 2024-07-30 | DevAI360 | Initial SRS document generated from requirements. |

---
## 1. Introduction
### 1.1 Purpose
This document specifies the software requirements for the Shopfloor Material Supply System. It is intended for project managers, developers, and QA teams to understand the system's functionalities, constraints, and quality attributes.

### 1.2 Document Conventions
This document follows a standard SRS structure. Requirements are categorized as Functional (FR) and Non-Functional (NFR).

### 1.3 Project Scope
The project aims to develop a web application that facilitates material supply requests from the production line to the warehouse. The system will manage the entire lifecycle of a delivery order, from creation to completion, and provide administrative oversight.

### 1.4 References
- User Requirement Document
- DevAI360 Skill: `devai360-analyst-requirement-analysis`

---
## 2. Overall Description
### 2.1 Product Perspective
The system is a self-contained web application designed to streamline the material supply process within a manufacturing environment. It will replace manual or less efficient communication methods between the shop floor and the warehouse.

### 2.2 User Classes and Characteristics
- **Production Operator:** Located on the production line, responsible for creating material supply requests.
- **Warehouse Staff:** Located in the warehouse, responsible for picking up, preparing, and delivering orders.
- **Administrator:** Has full control over the system, including managing orders and users.

### 2.3 Operating Environment
The application will be a web-based system hosted on an on-premises application server (Linux-based). It will be accessible via modern web browsers (Chrome, Firefox, Edge). The backend will be a Java Spring Boot application with a PostgreSQL database.

### 2.4 Design and Implementation Constraints
- **Technology Stack:** Angular (TypeScript) frontend, Spring Boot (Java 17) backend, PostgreSQL database.
- **Authentication:** JWT-based authentication.
- **API:** RESTful JSON API.
- **Initial Language:** English only.

### 2.5 Assumptions and Dependencies
- Users will have access to a desktop computer with a supported web browser.
- The on-premises infrastructure will be available and maintained.
- Network connectivity exists between the shop floor and warehouse locations.

---
## 3. System Features (Functional Requirements)

### 3.1 User Management
| ID | Requirement |
| :- | :- |
| **FR-001** | As an Administrator, I shall be able to create, view, update, and deactivate user accounts. |
| **FR-002** | As any user, I shall be able to log in to the system using a username and password. |
| **FR-003** | As any user, I shall be able to log out of the system. |

### 3.2 Material Request Management (Production Operator)
| ID | Requirement |
| :- | :- |
| **FR-004** | As a Production Operator, I shall be able to create a new material supply request, specifying material name, quantity, unit of measure, target production line, and optional notes. |
| **FR-005** | As a Production Operator, I shall be able to view the status of all requests for my production line. |
| **FR-006** | As a Production Operator, I shall be able to mark an `IN_TRANSIT` order as `DELIVERED` by clicking a "Receive" button. |
| **FR-007** | As a Production Operator, I shall be able to set a request's priority to `Normal` or `Urgent`. |


### 3.3 Order Fulfillment (Warehouse Staff)
| ID | Requirement |
| :- | :- |
| **FR-008** | As a Warehouse Staff member, I shall be able to view a queue of all `PENDING` material requests, sorted by priority then by creation time. |
| **FR-009** | As a Warehouse Staff member, I shall be able to accept a `PENDING` order, changing its status to `ACCEPTED`. |
| **FR-010** | As a Warehouse Staff member, I shall be able to mark an `ACCEPTED` order as `IN_TRANSIT` once the materials are prepared and ready for delivery. |

### 3.4 System Administration (Admin)
| ID | Requirement |
| :- | :- |
| **FR-011** | As an Administrator, I shall be able to view all orders in the system, regardless of their status. |
| **FR-012** | As an Administrator, I shall be able to filter the order list by status, production line, and date range. |
| **FR-013** | As an Administrator, I shall be able to manually edit the status of any order, providing a reason for the change. |
| **FR-014** | As an Administrator, I shall be able to logically delete (soft-delete) any order. |
| **FR-015** | As an Administrator, I shall be able to export the order list to a CSV file. |


---
## 6. Quality Attributes (Non-Functional Requirements)

### 6.1 Performance
| ID | Requirement |
| :- | :- |
| **NFR-001** | The system shall support up to 50 concurrent users with page load times of less than 3 seconds. |
| **NFR-002** | API response times for standard operations shall be under 1 second. |

### 6.2 Security
| ID | Requirement |
| :- | :- |
| **NFR-003** | User passwords shall be stored securely using BCrypt hashing. |
| **NFR-004** | All web traffic shall be encrypted using HTTPS. |
| **NFR-005** | The system shall prevent unauthorized access to administrative functions. |

### 6.3 Usability
| ID | Requirement |
| :- | :- |
| **NFR-006** | The user interface shall be intuitive and require minimal training for all user roles. |
| **NFR-007** | The application shall be compliant with WCAG 2.1 AA accessibility standards. |

### 6.4 Reliability
| ID | Requirement |
| :- | :- |
| **NFR-008** | The system shall have an uptime target of 99.5% during production hours. |
| **NFR-009** | An audit trail shall be maintained for all order status changes, including the user, timestamp, and the change made. |
