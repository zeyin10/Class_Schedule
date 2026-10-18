# Technology Comparison

This document compares alternatives for the key technical decisions of the project. Each decision is evaluated against criteria derived from the requirements (`06-requirements-specification.md`), and the selected option is justified.

Scoring: 1 (poor) – 5 (excellent). Weights reflect importance for this project (sum = 100%).

---

## Decision 1. Data storage (DBMS)

**Driving requirements:** FR-07…FR-09 (conflict prevention), NFR-03 (data integrity under concurrent edits), NFR-01 (query performance), relational domain model (groups ↔ teachers ↔ rooms ↔ courses ↔ time slots).

| Criterion | Weight | PostgreSQL 16 | MySQL 8 | MongoDB 7 |
|---|---|---|---|---|
| Enforcing "no overlapping bookings" inside the DB | 30% | **5** — native range types (`tstzrange`/`int4range`) + `EXCLUDE USING gist` constraints make overlaps impossible even under concurrent transactions | 2 — no exclusion constraints; requires triggers or application-level locking | 1 — no relational constraints; must be done in application code with transactions |
| Relational integrity (FKs, transactions) | 20% | 5 | 5 | 3 |
| Query capabilities (CTEs, window functions, indexing) | 15% | 5 | 4 | 3 |
| Team experience | 15% | 5 (DBMS course, SQL optimisation internship) | 4 | 2 |
| Tooling / ecosystem (SQLAlchemy, Alembic, Docker images) | 10% | 5 | 5 | 4 |
| Licence & cost | 10% | 5 (open source) | 4 (GPL/commercial) | 4 (SSPL) |
| **Weighted score** | | **5.00** | **3.70** | **2.45** |

**Decision: PostgreSQL.** The schedule is a strongly relational domain, and the central quality requirement — no double-booking — can be guaranteed declaratively by the database instead of relying only on application checks, which are vulnerable to race conditions. MongoDB is rejected because the data is not document-shaped and integrity would have to be reimplemented.

---

## Decision 2. Backend platform

**Driving requirements:** FR-14 (REST API with documentation), NFR-04 (security/RBAC), NFR-07 (maintainability, test coverage), schedule of the project (one backend developer, limited time), stretch goal (constraint-solver-based suggestions).

| Criterion | Weight | FastAPI (Python 3.12) | Spring Boot (Java 21) | NestJS (Node.js / TypeScript) |
|---|---|---|---|---|
| Automatic OpenAPI documentation & request validation | 20% | **5** — generated from type hints and Pydantic models | 4 — via springdoc, more annotations | 4 — via decorators/Swagger module |
| Development speed for a single developer | 20% | 5 | 3 (more boilerplate) | 4 |
| Performance for expected load (≤ a few thousand users) | 10% | 4 (async, ASGI) | 5 | 4 |
| Team experience | 15% | 4 | 3 | 3 |
| Testing support (pytest, test client) | 10% | 5 | 5 | 4 |
| Ecosystem for the stretch goal (Google OR-Tools CP-SAT, data processing) | 15% | **5** — first-class Python API | 4 (Java API exists) | 2 (no official bindings) |
| ORM / migrations for PostgreSQL | 10% | 5 (SQLAlchemy 2, Alembic) | 5 (JPA/Hibernate, Flyway) | 4 (TypeORM/Prisma) |
| **Weighted score** | | **4.75** | **3.95** | **3.55** |

**Decision: FastAPI.** It produces an OpenAPI contract automatically, which is the integration point with the frontend; it allows fast iteration for a one-person backend; and Python gives direct access to OR-Tools if the automatic-suggestion stretch goal is implemented. Spring Boot is a strong alternative for large enterprise systems but adds overhead that is not justified by the expected load.

---

## Decision 3. Frontend framework

**Driving requirements:** FR-10…FR-13 (schedule views, search, filters), NFR-05 (responsive UI), NFR-06 (usability), integration with the REST API.

| Criterion | Weight | React 18 + TypeScript (Vite) | Angular 18 | Vue 3 + TypeScript |
|---|---|---|---|---|
| Ecosystem of ready components (calendars/timetable grids, UI kits) | 25% | 5 (FullCalendar, MUI, TanStack Query) | 4 | 4 |
| Learning curve for the frontend developer | 20% | 4 | 2 | 5 |
| Type safety & generated API client from OpenAPI | 15% | 5 (openapi-typescript) | 5 | 4 |
| Community, documentation, job-market relevance | 15% | 5 | 4 | 4 |
| Responsive/mobile support | 15% | 5 | 5 | 5 |
| Testing (Vitest, React Testing Library, Playwright) | 10% | 5 | 4 | 5 |
| **Weighted score** | | **4.80** | **3.90** | **4.45** |

**Decision: React + TypeScript.** The largest ecosystem of calendar/timetable components reduces implementation risk, and TypeScript types generated from the OpenAPI specification keep the frontend consistent with the backend contract. Vue is a close and acceptable alternative; Angular is rejected due to its steeper learning curve within the project timeline.

---

## Decision 4. Component interaction (API style)

**Driving requirements:** FR-14 (public, documented API), FR-16 (iCal export), NFR-07 (maintainability), separation of work between two developers.

| Criterion | Weight | REST + OpenAPI 3 | GraphQL | gRPC |
|---|---|---|---|---|
| Browser support without proxies | 20% | 5 | 5 | 2 (needs gRPC-Web) |
| Contract-first development between two developers | 20% | 5 (OpenAPI spec, generated clients) | 4 (schema) | 5 (protobuf) |
| Simplicity / team experience | 20% | 5 | 3 | 2 |
| HTTP caching of read-heavy schedule queries | 15% | 5 | 2 | 2 |
| Reuse by external consumers (calendars, other services) | 15% | 5 | 3 | 3 |
| Flexible querying | 10% | 3 | 5 | 3 |
| **Weighted score** | | **4.80** | **3.65** | **2.85** |

**Decision: REST with an OpenAPI 3 specification.** The workload is read-heavy and benefits from HTTP caching; REST is natively supported by FastAPI and browsers; the OpenAPI document serves as the formal contract between the backend and frontend developers.

---

## Decision 5. Authentication and authorisation

| Criterion | Weight | JWT (access + refresh) with RBAC in backend | Server-side sessions (cookies) | External IdP (Keycloak / OAuth2 provider) |
|---|---|---|---|---|
| Fit for a stateless REST API | 30% | 5 | 3 | 5 |
| Implementation effort | 25% | 4 | 4 | 2 |
| Security when implemented correctly | 25% | 4 (short-lived tokens, httpOnly refresh cookie) | 4 | 5 |
| Future integration with university SSO | 20% | 4 (can be swapped behind an interface) | 2 | 5 |
| **Weighted score** | | **4.30** | **3.30** | **4.25** |

**Decision: JWT with role-based access control** (roles: `student`, `teacher`, `admin`), implemented with FastAPI security dependencies. An external IdP is noted as future work for production integration with university SSO.

---

## Decision 6. Deployment and environment

| Option | Pros | Cons |
|---|---|---|
| **Docker Compose (selected)** | One command to start DB + backend + frontend; identical environment for both developers, supervisor and committee | Single host only |
| Manual installation | No container knowledge needed | "Works on my machine" problems, hard to reproduce |
| Kubernetes | Scalable, production-grade | Excessive complexity for the project scope |

---

## Summary of the selected stack

| Layer | Technology |
|---|---|
| Database | PostgreSQL 16 (range types, exclusion constraints, `btree_gist`) |
| Backend | Python 3.12, FastAPI, SQLAlchemy 2, Alembic, Pydantic v2 |
| API | REST, OpenAPI 3 (Swagger UI) |
| Auth | JWT + role-based access control |
| Frontend | React 18, TypeScript, Vite, TanStack Query, a calendar/timetable component (e.g. FullCalendar) |
| Testing | pytest, httpx; Vitest, React Testing Library; Playwright (E2E); k6 or Locust (load) |
| DevOps | Docker, Docker Compose, GitHub Actions (CI: lint + tests) |
| Project management | Git (GitHub), YouTrack |
| Design | Figma (UI), PlantUML (diagrams) |
