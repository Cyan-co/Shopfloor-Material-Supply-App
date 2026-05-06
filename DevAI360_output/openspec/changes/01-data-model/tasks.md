# Tasks: Shopfloor Material Supply System Data Model Implementation

**Paths:** Backend code under `backend/src/main/java/com/example/shopfloor/`. Database migrations under `backend/src/main/resources/db/migration/`. Frontend models under `frontend/src/app/models/`.

---

## Flyway Migration

- [ ] **Task 1: Create Flyway Migration Script**
  - File: `backend/src/main/resources/db/migration/V202407311000__Create_shopfloor_schema.sql`
  - Add `CREATE SCHEMA IF NOT EXISTS shopfloor;`
  - Add `CREATE TABLE shopfloor.users` with all columns, types, and constraints as defined in `proposal.md`.
  - Add `CREATE TABLE shopfloor.delivery_orders` including all FK references.
  - Add `CREATE TABLE shopfloor.audit_logs` with JSONB `details` column.
  - Add all indexes listed in the proposal's "Indexes & Performance" section.
  - Verify the script runs cleanly against a fresh H2 instance.

---

## Spring Data JPA Entities

- [ ] **Task 2: Create `User` Entity**
  - File: `backend/src/main/java/com/example/shopfloor/model/User.java`
  - Annotate with `@Entity`, `@Table(name = "users", schema = "shopfloor")`.
  - Add `@Id @GeneratedValue(strategy = GenerationType.UUID) private UUID id;`.
  - Add all fields with corresponding JPA column annotations, including `@Column(name = "password_hash")` for the password.
  - Use `@Enumerated(EnumType.STRING)` for the `role` field.
  - Add `@CreatedDate` and `@LastModifiedDate` annotations.

- [ ] **Task 3: Create `DeliveryOrder` Entity**
  - File: `backend/src/main/java/com/example/shopfloor/model/DeliveryOrder.java`
  - Annotate with `@Entity`, `@Table(name = "delivery_orders", schema = "shopfloor")`.
  - Add `@ManyToOne(fetch = FetchType.LAZY) @JoinColumn(name = "requester_id", nullable = false) private User requester;`.
  - Add `@ManyToOne(fetch = FetchType.LAZY) @JoinColumn(name = "handler_id") private User handler;`.
  - Add all remaining fields.
  - For soft delete: add `@Column(name = "is_deleted", nullable = false) private boolean deleted = false;` and annotate the class with `@SQLRestriction("is_deleted = false")`.

- [ ] **Task 4: Create `AuditLog` Entity**
  - File: `backend/src/main/java/com/example/shopfloor/model/AuditLog.java`
  - Annotate with `@Entity`, `@Table(name = "audit_logs", schema = "shopfloor")`.
  - Map the `details` JSONB column using `@JdbcTypeCode(SqlTypes.JSON)`.
  - This entity should be immutable (omit setters).
  - Add `@ManyToOne` FK relationships to `DeliveryOrder` and `User`.

---

## Enums

- [ ] **Task 5: Create `DeliveryOrderStatus` Enum**
  - File: `backend/src/main/java/com/example/shopfloor/model/DeliveryOrderStatus.java`
  - Define enum values: `PENDING`, `ACCEPTED`, `IN_TRANSIT`, `DELIVERED`, `CANCELLED`.
  - Add a `Set<DeliveryOrderStatus> getAllowedNextStates()` method on each constant to encode the state transition logic.

- [ ] **Task 6: Create `UserRole` Enum**
  - File: `backend/src/main/java/com/example/shopfloor/model/UserRole.java`
  - Define enum values: `PRODUCTION_OPERATOR`, `WAREHOUSE_STAFF`, `ADMINISTRATOR`.

---

## Spring Data JPA Repositories

- [ ] **Task 7: Create `UserRepository`**
  - File: `backend/src/main/java/com/example/shopfloor/repository/UserRepository.java`
  - Extend `JpaRepository<User, UUID>`.
  - Add `Optional<User> findByUsername(String username);`.

- [ ] **Task 8: Create `DeliveryOrderRepository`**
  - File: `backend/src/main/java/com/example/shopfloor/repository/DeliveryOrderRepository.java`
  - Extend `JpaRepository<DeliveryOrder, UUID>`.
  - Add `Page<DeliveryOrder> findByRequesterId(UUID requesterId, Pageable pageable);`.
  - Add `Page<DeliveryOrder> findByStatusIn(Set<DeliveryOrderStatus> statuses, Pageable pageable);`.

- [ ] **Task 9: Create `AuditLogRepository`**
  - File: `backend/src/main/java/com/example/shopfloor/repository/AuditLogRepository.java`
  - Extend `JpaRepository<AuditLog, UUID>`.
  - Add `List<AuditLog> findByOrderIdOrderByCreatedAtDesc(UUID orderId);`.

---

## Frontend TypeScript Models

- [ ] **Task 10: Create `user.model.ts`**
  - File: `frontend/src/app/models/user.model.ts`
  - Export `interface User { id: string; username: string; role: UserRole; }`.

- [ ] **Task 11: Create `delivery-order.model.ts`**
  - File: `frontend/src/app/models/delivery-order.model.ts`
  - Export `interface DeliveryOrder { id: string; materialName: string; quantity: number; ...; requester?: User; handler?: User; createdAt: string; }`.

- [ ] **Task 12: Create enums for frontend**
  - File: `frontend/src/app/models/enums.ts`
  - Export `enum DeliveryOrderStatus { PENDING = 'PENDING', ... }`.
  - Export `enum UserRole { PRODUCTION_OPERATOR = 'PRODUCTION_OPERATOR', ... }`.
