# Project Proposal

## 1. General information

| Item | Value |
|---|---|
| **Project title** | Development of a Class Schedule Management System |
| **Programme** | Software Engineering, group SE-2431 |
| **Team members** | Zeyin Abdikassym — Backend Developer (Database and API Development)<br>Nurzhaina Kuralbay — Frontend Developer (UI/UX Design and Implementation) |
| **Supervisor** | Toleu Altynbek |
| **Project type** | Team diploma project (2 students) |

## 2. Problem statement

At most universities the class schedule is prepared by a small number of staff in spreadsheets or desktop timetabling tools and then published as static files (PDF/Excel) or images. This causes several recurring problems:

1. **Conflicts are detected late.** When the schedule is edited manually (room change, teacher replacement, moved lesson), double-bookings of a teacher, a room or a student group are often discovered only when students and teachers arrive at the room.
2. **Information is fragmented and quickly outdated.** Students and teachers have to look through several files or messenger channels to find their current schedule; changes are communicated informally and are easy to miss.
3. **Search and personalisation are poor.** A static file cannot easily answer simple questions such as *"Where is my next lesson?"*, *"When is room C1.3.234 free?"* or *"What does teacher X teach on Wednesday?"*.
4. **Editing is labour-intensive.** Schedulers spend time manually checking every change against teacher, room and group availability.
5. **No integration.** Schedule data is not available through an API, so it cannot be reused by other services (LMS, mobile apps, calendars).

> Note: the statements above are working hypotheses. They will be confirmed or corrected by the interviews described in `02-evidence-of-relevance.md`, and the requirements will be updated accordingly.

## 3. Target users

| User group | Main needs |
|---|---|
| **Students** | Quickly see their own group's schedule on a phone, find rooms, receive notice about changes, export to a personal calendar. |
| **Teachers** | See their personal teaching schedule, check free rooms, be informed about changes. |
| **Schedule administrators (schedulers / dean's office staff)** | Create and edit the timetable, manage reference data (groups, teachers, rooms, courses, time slots), be warned immediately about conflicts, publish changes. |
| **External systems (secondary)** | Read schedule data through a documented REST API (e.g. iCal subscription, other university services). |

## 4. Aim

To design and develop a web-based class schedule management system that stores the university timetable centrally, **prevents scheduling conflicts at the moment of editing**, and gives students, teachers and administrators fast, role-appropriate access to an always-current schedule.

## 5. Objectives

1. Analyse the scheduling process and the needs of students, teachers and administrators (interviews, existing schedule data).
2. Review literature on university course timetabling, and compare existing timetabling systems and candidate technologies.
3. Specify functional and non-functional requirements with priorities and acceptance criteria.
4. Design the system architecture, the relational data model and the REST API (OpenAPI specification).
5. Design UI/UX prototypes for the key user scenarios.
6. Implement the backend: database with integrity constraints that prevent teacher/room/group double-booking, business logic, role-based access control and the REST API.
7. Implement the responsive frontend: schedule views, search and filters, administrator editing interface with conflict feedback.
8. Verify the system through automated tests, performance measurements and a usability evaluation against a baseline (the current spreadsheet/PDF process).

## 6. Expected outcomes

- A working prototype of the web system deployed with Docker Compose.
- A PostgreSQL database schema with constraints guaranteeing conflict-free schedules, plus seed/test data.
- A documented REST API (OpenAPI/Swagger).
- A responsive React web interface for three roles: student, teacher, administrator.
- Project documentation: requirements, architecture and diagrams, test reports, evaluation results.
- Evaluation results comparing the system with the current process (time to find information, time to make a correct change, number of undetected conflicts).

## 7. Project scope

### In scope

- Management of reference data: faculties/departments, groups, teachers, rooms (with capacity and type), courses, lesson types (lecture/practice/lab), time slots, academic terms.
- Manual creation and editing of schedule entries by administrators.
- **Automatic conflict detection and prevention** for teachers, rooms and groups; capacity and room-type checks.
- Schedule views by group, teacher and room (day / week), search and filtering.
- Authentication and role-based access control (student, teacher, administrator).
- Change history (audit log) and display of recent changes to affected users.
- Export of a personal schedule to iCalendar (.ics).
- REST API with OpenAPI documentation.
- Responsive web UI (desktop and mobile browsers) in Kazakh, Russian and English.

### Out of scope (for this diploma)

- Native mobile applications (the web UI is responsive instead).
- Full automatic timetable generation for the whole university — planned only as an optional **stretch goal** (generation suggestions for a limited set of entries using a constraint solver).
- Integration with real university SSO/LMS systems (a mock or import from CSV/Excel is used instead).
- Exam scheduling, attendance tracking, grade management.
- Push/SMS notifications (in-app change indicators and optional e-mail only).

## 8. Engineering complexity

The project goes beyond a simple CRUD application:

- **Data integrity under concurrency:** conflicts must be impossible even if two administrators edit the schedule at the same time. This is enforced at the database level (PostgreSQL range types and exclusion constraints) and reported clearly in the API and UI.
- **Non-trivial domain model:** recurring weekly lessons, academic terms, odd/even weeks, groups and subgroups, rooms of different types and capacities.
- **Query performance:** fast filtering and search over the whole timetable (indexes, query optimisation, measured response times).
- **Security:** authentication, role-based authorisation, protection against OWASP API Top 10 risks.
- **Full-stack integration:** a contract-first REST API shared between two independently developed components.
- **Measurable evaluation:** automated tests, performance metrics and a usability study against a baseline.

## 9. Responsibilities

| Area | Zeyin Abdikassym (Backend) | Nurzhaina Kuralbay (Frontend) |
|---|---|---|
| Analysis | Data and integrity requirements | User scenarios, UX requirements |
| Design | ER model, DB schema, API specification, component architecture | Information architecture, wireframes, Figma prototype |
| Implementation | FastAPI service, PostgreSQL, conflict detection, RBAC, audit log, iCal export | React application, schedule views, search/filters, admin editor, API integration |
| Testing | Unit and integration tests for API and DB constraints, performance tests | Component tests, end-to-end tests, usability testing |
| Shared | Requirements, use cases, evaluation plan, README, code review of each other's pull requests | |
