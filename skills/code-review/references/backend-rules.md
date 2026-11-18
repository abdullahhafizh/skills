# Backend Rules (Spring Boot / Java)

Apply these rules only when the MR diff contains backend files (`*.java`, `*.yml`, `*.yaml`, `*.gradle`, `*.kts`, `*.sql` — excluding CI/CD and infra YAML (`.gitlab-ci.yml`, `docker-compose*.yml`, `.github/**`, manifest Kubernetes/Helm)), including repo-prefixed GitLab paths (for example `bmi-pka-api/gateway/src/...`).

## 1. Always-Apply Backend Quality Gates

Every backend diff must pass all six gates:

- **No raw CRUD queries**: Never use `@Query(nativeQuery = true)` or raw SQL/JPQL for CRUD unless an identical pattern already exists in the same module and Criteria/Specification is impossible.
- **No N+1 in list or detail paths**: Enforce `@EntityGraph`, `JOIN FETCH`, `@BatchSize`, or DTO projections on associations.
- **Strict layer & contract alignment**: Follow `controller -> service -> repository`, use Criteria/Specification for filters, load literals from constants/enums, and wrap responses in the shared `ApiResponse`.
- **Permission-only AuthZ**: Enforce `@PreAuthorize("hasPermission(...)")`. Never branch or authorize by role name.
- **Zero secrets/PII & validated inputs**: Never expose secrets or PII in code, responses, or logs. Validate all inputs, especially file uploads and rich text.
- **Sibling parity**: Match package structure, naming, validation annotations, audit hooks, and security annotations with sibling features in the owning module.

## 2. Glob-Based Backend Rules (20 Files)

Match each changed file path (ignoring any leading repository folder prefix) against the `Glob` column. Evaluate the changed lines against every matched rule:

| Rule File | Evaluated Criteria | Trigger (`Wajib` / `Glob`) |
| :--- | :--- | :--- |
| **Quality gates** (`backend/00-quality-gates.mdc`) | Mandatory for all API work: native query ban, N+1 prevention, `hasPermission` AuthZ, and established layer architecture. | **Wajib:** Always apply when any backend file per the scope stated at the top of this file is in the diff |
| **Zero hardcode (wajib)** (`springboot-no-hardcode.mdc`) | Strings, magic numbers, status codes, URLs, and permissions must come from constants, enums, i18n, or config. | **Wajib:** Always apply when backend stack is detected |
| **API response & dokumentasi** (`springboot-api-response-i18n.mdc`) | `ApiResponse` wrapper structure, Swagger/OpenAPI annotations, message externalization (i18n), logging conventions. | **Glob:** `**/*Controller.java`, `**/*ServiceImpl.java`, `**/*Response.java`, `**/*Dtos.java` |
| **Async, versioning, mapping** (`springboot-async-versioning-mapping.mdc`) | Async execution pools/boundaries, API versioning strategy, MapStruct mapper conventions. | **Glob:** `**/*Service*.java`, `**/*Controller*.java`, `**/*Mapper*.java`, `**/*Config*.java` |
| **Audit logging** (`springboot-audit-logging.mdc`) | Mandatory audit trail recording for every auditable business mutation or event. | **Glob:** `**/*Controller.java`, `**/*Audit*.java`, `**/*Event.java`, `**/audit/**/*.java` |
| **Caching** (`springboot-caching.mdc`) | Spring Cache and Redis usage, `@Cacheable`, deterministic cache eviction, and explicit TTL configuration. | **Glob:** `**/*Service*.java`, `**/*Config*.java`, `**/application*.yml` |
| **Konvensi class & import** (`springboot-class-conventions.mdc`) | Class member ordering, import ordering, and Java Spring Boot code structure conventions (within quality gate bounds). | **Glob:** `**/*.java` |
| **Criteria, Specification, Pageable** (`springboot-criteria-specification.mdc`) | Dynamic filtering via Criteria/Specification and mandatory `Pageable` pagination on list endpoints. | **Glob:** `**/*Controller.java`, `**/*Service.java`, `**/*Repository.java`, `**/specification/**/*.java` |
| **Migrasi Flyway** (`springboot-database-migration.mdc`) | Timestamp/date migration versioning and strict separation of DDL and DML scripts. | **Glob:** `**/db/migration/**/*.sql`, `**/flyway/**` |
| **Error handling** (`springboot-error-handling.mdc`) | Domain exceptions, standardized error codes, global `@RestControllerAdvice` handling, and bean validation. | **Glob:** `**/*Exception*.java`, `**/*ErrorCode*.java`, `**/*ExceptionHandler*.java` |
| **Komunikasi antar service** (`springboot-inter-service-communication.mdc`) | RabbitMQ (preferred) and REST integration patterns across publishers, consumers, and declarative clients. | **Glob:** `**/*Consumer.java`, `**/*Publisher*.java`, `**/*AmqpConfig.java`, `**/*Client.java`, `**/*Event.java`, `**/*Message.java` |
| **Struktur modul fitur** (`springboot-module-structure.mdc`) | Feature package layout and naming across `gateway`, `core`, and `utility` modules. | **Glob:** `gateway/src/**/*.java`, `core/src/**/*.java`, `utility/src/**/*.java` |
| **Arsitektur multi-module** (`springboot-multi-module-architecture.mdc`) | Gradle multi-module boundaries and dependency direction across `common`, `gateway`, `core`, and `utility`. | **Glob:** `**/*.java`, `**/build.gradle`, `**/settings.gradle` |
| **Observability** (`springboot-observability.mdc`) | Spring Boot Actuator, Prometheus metrics, structured JSON logging, distributed tracing, and health checks. | **Glob:** `**/*.java`, `**/application*.yml`, `**/logback*.xml` |
| **Performance** (`springboot-performance.mdc`) | N+1 prevention, JPA fetch strategies, bulk operations, HikariCP connection pool sizing, and query optimization. | **Glob:** `**/*.java`, `**/application*.yml` |
| **PII** (`springboot-pii-protection.mdc`) | PII classification, log/response masking, field-level access, encryption at rest/in transit, and retention policy. | **Glob:** `**/*.java`, `**/application*.yml` |
| **Security** (`springboot-security-hardening.mdc`) | LDAP/JWT integration, RBAC `hasPermission(...)`, rate limiting, and prohibition of public AI endpoints/calls. | **Glob:** `**/*Config*.java`, `**/*Controller*.java`, `**/*Security*.java`, `**/application*.yml` |
| **Service, repository, model** (`springboot-service-repository.mdc`) | Service/repository boundary separation, DTO vs entity isolation (never leak entities), mappers, and zero hardcode. | **Glob:** `**/*Service.java`, `**/*ServiceImpl.java`, `**/*Repository.java`, `**/*Request.java`, `**/*Response.java`, `**/*Dtos.java` |
| **Shared common** (`springboot-shared-common.mdc`) | `BaseEntity`, shared `ApiResponse`, common error contracts, and shared Redis/RabbitMQ helpers. | **Glob:** `common/src/**/*.java` |
| **Testing** (`springboot-testing.mdc`) | Unit tests, integration tests (`*IT`), E2E tests, test count non-regression, and coverage thresholds. | **Glob:** `**/*Test.java`, `**/*IT.java`, `**/test/**/*.java` |
