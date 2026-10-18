# Requirements Specification

**Version:** 0.1 (initial, before interview validation)
**Prioritisation:** MoSCoW — **M**ust, **S**hould, **C**ould, **W**on't (this release).
**Traceability:** each requirement has an ID used in YouTrack tasks, tests and diagrams.

> The requirements are initial. After the user interviews (`02-evidence-of-relevance.md`) they will be confirmed, re-prioritised or extended, and the change will be recorded in the version history at the end of this document.

## 1. Actors

| Actor | Description |
|---|---|
| Guest | Unauthenticated visitor (may view the public schedule if enabled). |
| Student | Views the schedule of their group, searches, exports. |
| Teacher | Views personal schedule and room availability. |
| Administrator | Manages reference data and schedule entries. |
| External system | Consumes the read-only API / iCal feed. |

## 2. Functional requirements

| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-01 | The system shall authenticate users by login and password. | M | Given valid credentials, the user receives an access token and sees the page for their role; invalid credentials return HTTP 401 and an error message; passwords are stored only as hashes (bcrypt/argon2). |
| FR-02 | The system shall restrict operations by role (student, teacher, admin). | M | A student or teacher calling any create/update/delete endpoint receives HTTP 403; an admin can perform them; verified by automated tests for every protected endpoint. |
| FR-03 | Admin shall manage groups (create, edit, delete, list). | M | CRUD operations work via API and UI; a group with scheduled lessons cannot be deleted without confirmation (HTTP 409 with explanation). |
| FR-04 | Admin shall manage teachers. | M | Same as FR-03 for teachers. |
| FR-05 | Admin shall manage rooms with capacity and type (lecture hall, computer lab, classroom). | M | Rooms have name, building, capacity and type; validation rejects capacity ≤ 0. |
| FR-06 | Admin shall manage courses, lesson types and time slots for an academic term. | M | A term has start/end dates; time slots do not overlap within a day; courses can be linked to groups and teachers. |
| FR-07 | Admin shall create, edit and delete schedule entries (course, lesson type, teacher, group(s), room, day, time slot, week pattern). | M | A valid entry is saved and immediately visible in group, teacher and room views. |
| FR-08 | The system shall prevent conflicts: the same teacher, room or group cannot be booked in overlapping time. | M | An attempt to save a conflicting entry is rejected (HTTP 409) with a description of the conflicting entry; the rule holds when two admins save conflicting entries concurrently (verified by a concurrency test); 0 conflicts in the database after the full test suite. |
| FR-09 | The system shall warn when room capacity is smaller than the group size or the room type does not match the lesson type. | S | The entry can be saved only after explicit confirmation; the warning is shown in the UI. |
| FR-10 | Users shall view the schedule by group, teacher or room in day and week mode. | M | Selecting a group/teacher/room shows its lessons for the chosen week; switching week updates the view. |
| FR-11 | Users shall search and filter the schedule (by group, teacher, room, course, day). | M | Search returns matching results; filters can be combined; results appear within NFR-01 limits. |
| FR-12 | A logged-in student/teacher shall see "My schedule" by default and the next upcoming lesson. | S | After login, the personal schedule for the current week is shown with the next lesson highlighted. |
| FR-13 | Users shall see free rooms for a selected time slot. | S | The list contains only rooms without bookings in that slot; can be filtered by type and minimum capacity. |
| FR-14 | The system shall provide a documented REST API. | M | An OpenAPI 3 specification is generated and available via Swagger UI; every endpoint has request/response schemas. |
| FR-15 | The system shall keep a change history of schedule entries (who, when, what). | S | Every create/update/delete produces an audit record; admin can view history of an entry. |
| FR-16 | Users shall export their schedule to iCalendar (.ics). | S | The exported file imports correctly into Google Calendar and Outlook with correct times and time zone (Asia/Almaty). |
| FR-17 | Affected users shall see recent changes to their schedule. | S | Lessons changed in the last 7 days are marked in the user's view; a "Recent changes" list is available. |
| FR-18 | Admin shall import reference data and schedule entries from CSV/Excel. | C | A file in the documented template is imported; invalid rows are reported with line numbers; conflicting rows are not imported. |
| FR-19 | The system shall suggest free (teacher, room, slot) combinations for a lesson being scheduled. | C | For a selected course and group, the system lists conflict-free options; (stretch) uses a constraint solver for a batch of lessons. |
| FR-20 | The system shall send e-mail notifications on schedule changes. | C | Affected users receive an e-mail within 5 minutes of the change. |
| FR-21 | Full automatic timetable generation for the whole university. | W | — (out of scope) |
| FR-22 | Native mobile applications. | W | — (out of scope; replaced by responsive UI) |

## 3. Non-functional requirements

| ID | Category | Requirement | Priority | Acceptance criteria / metric |
|---|---|---|---|---|
| NFR-01 | Performance | Schedule views and search respond quickly. | M | 95th percentile API response time ≤ 300 ms for schedule queries with a dataset of ≥ 200 groups, 300 teachers, 150 rooms, 5,000 entries, under 100 concurrent users (load test). |
| NFR-02 | Performance | Pages load quickly on mobile. | S | First contentful paint ≤ 2 s on a simulated 4G connection (Lighthouse). |
| NFR-03 | Data integrity | No conflicting schedule entries can be stored, including under concurrent edits. | M | Enforced by DB constraints; concurrency test with parallel requests results in exactly one success and others HTTP 409. |
| NFR-04 | Security | Protect against common API threats. | M | Passwords hashed; JWT with expiry ≤ 30 min; all write endpoints require admin role; input validated; no secrets in repository; checked against OWASP API Security Top 10 checklist. |
| NFR-05 | Usability / responsiveness | UI works on screens from 360 px to 1920 px wide. | M | All key scenarios can be completed on a 360 px wide viewport without horizontal scrolling (manual + Playwright mobile test). |
| NFR-06 | Usability | Users can complete key tasks easily. | S | In usability testing with ≥ 5 participants, task success rate ≥ 90% and SUS score ≥ 70. |
| NFR-07 | Maintainability | Code is tested and consistently structured. | S | Backend test coverage ≥ 70% of business logic; linting (ruff, eslint) passes in CI; every change goes through a reviewed pull request. |
| NFR-08 | Portability / deployment | The system can be started with a single command. | M | `docker compose up` starts DB, backend and frontend on a clean machine; setup steps documented in README. |
| NFR-09 | Accessibility | Basic accessibility. | C | Lighthouse accessibility score ≥ 90; sufficient colour contrast (WCAG 2.1 AA). |
| NFR-10 | Localisation | Interface available in Kazakh, Russian and English. | S | All UI strings externalised (i18n); KZ/RU/EN translations complete for key scenarios; language switch persists per user. Motivation: no compared system has confirmed Kazakh support (see `04-existing-systems-comparison.md`). |
| NFR-11 | Availability | Read access remains available during edits. | S | Editing the schedule does not block viewing (no table-level locks during normal edits). |

## 4. Constraints and assumptions

- C-1: Development time is limited to the diploma timeline (until final defence).
- C-2: Team of two developers; no real integration with university SSO/LMS.
- A-1 (assumption): Real schedule data or a realistic anonymised sample can be obtained for testing; otherwise synthetic data is generated.
- A-2 (assumption): A weekly recurring schedule (with optional odd/even weeks) covers the university's needs.

## 5. Version history

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-10-18 | Initial requirements based on the project proposal. |
