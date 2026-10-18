# Project Management and Risks

## 1. Tools and links

| Tool | Purpose | Link |
|---|---|---|
| GitHub | Source code, documentation, diagrams, reviews (pull requests) | _TODO: repository URL_ |
| YouTrack | Task tracking, priorities, deadlines | _TODO: YouTrack project URL_ |
| Figma | UI/UX prototype | _TODO: Figma URL_ |

## 2. Team and responsibilities

| Member | Role | Main responsibilities | Reviews PRs of |
|---|---|---|---|
| Zeyin Abdikassym | Backend Developer (Database and API) | Data model, PostgreSQL schema & constraints, FastAPI services, RBAC, API spec, backend tests, performance tests, CI | Nurzhaina |
| Nurzhaina Kuralbay | Frontend Developer (UI/UX) | User scenarios, Figma prototype, React application, API integration, E2E & usability tests, accessibility | Zeyin |
| Toleu Altynbek | Supervisor | Reviews key deliverables and decisions at agreed checkpoints | — |

Shared: proposal, requirements, use cases, evaluation plan, README, interviews.

## 3. Workflow

- `main` is the reviewed baseline; work is done in branches named `DIP-<id>-<short-name>`.
- Commit messages start with the task ID: `DIP-12: Add ER diagram draft`.
- Every change is merged through a pull request that references the YouTrack task, explains how it was checked, and is reviewed by the other team member.
- A YouTrack task is closed only when its acceptance criteria are met and the PR/commit link is added to the task.
- Each pre-defense submission is tagged: `pre-defense-1`, `pre-defense-2`, `final-pre-defense`.

## 4. Milestones

| # | Milestone | Target date | Main deliverables |
|---|---|---|---|
| M0 | Project start | 2026-10-14 | Topic, roles, justification letters |
| M1 | **Pre-defense 1** | **2026-10-24** | Proposal, relevance evidence, literature review, comparisons, requirements, diagrams, prototype, evaluation plan, YouTrack backlog (`pre-defense-1` tag) |
| M2 | Design freeze & skeleton | 2026-11-15 | DB schema with constraints + migrations, OpenAPI spec v1, React project skeleton, CI pipeline, Docker Compose |
| M3 | Core features | 2026-12-20 | Auth/RBAC, reference data CRUD, schedule entries with conflict prevention, schedule views, search/filters |
| M4 | **Pre-defense 2** | _TBD by university_ | Working core prototype, test reports (`pre-defense-2` tag) |
| M5 | Extended features | _TBD_ | Free rooms, history, recent changes, iCal export, import (C), suggestions (stretch) |
| M6 | Evaluation | _TBD_ | Performance tests, usability study, comparison with baseline |
| M7 | **Final pre-defense** | _TBD by university_ | Complete system, documentation, thesis text (`final-pre-defense` tag) |

## 5. Initial backlog for Pre-defense 1

Planned YouTrack tasks (to be created in YouTrack with the same IDs where possible).

| ID | Task | Owner | Priority | Deadline | Acceptance criteria |
|---|---|---|---|---|---|
| DIP-1 | Create repository structure, README, .gitignore | Zeyin | Critical | 2026-10-19 | Structure per Git requirements; README contains all required sections |
| DIP-2 | Create YouTrack project and backlog; give access to supervisor | Nurzhaina | Critical | 2026-10-19 | Tasks have owner, priority, deadline, acceptance criteria |
| DIP-3 | Write project proposal and scope | Zeyin | Critical | 2026-10-20 | `docs/01-project-proposal.md` reviewed by partner |
| DIP-4 | Prepare interview guide and conduct 3–5 interviews | Both | Critical | 2026-10-22 | Notes for each interview; verified findings vs assumptions table |
| DIP-5 | Literature review (≥12 sources, ≥8 scientific) | Both | Critical | 2026-10-22 | Analysis table complete; all references verifiable |
| DIP-6 | Existing system comparison (3–5 systems, 6–10 criteria) | Nurzhaina | High | 2026-10-21 | Every claim has a source |
| DIP-7 | Technology comparison (≥3 decisions × 2–3 alternatives) | Zeyin | High | 2026-10-21 | Choices linked to requirements |
| DIP-8 | Requirements specification with priorities and acceptance criteria | Both | Critical | 2026-10-22 | Every Must requirement has a verifiable acceptance criterion |
| DIP-9 | System context and use case diagrams | Nurzhaina | High | 2026-10-22 | Editable sources + PNG exports in `diagrams/` |
| DIP-10 | Component diagram (draft) and architecture description | Zeyin | High | 2026-10-22 | Consistent with requirements and use cases |
| DIP-11 | ER diagram (draft) with conflict-prevention design | Zeyin | High | 2026-10-22 | Entities cover all reference data; constraint concept documented |
| DIP-12 | Figma prototype of key scenarios | Nurzhaina | Critical | 2026-10-23 | Mockups for student view, search, admin editor with conflict message |
| DIP-13 | Verification and evaluation plan | Zeyin | High | 2026-10-23 | Tests mapped to requirements; metrics, conditions, baseline defined |
| DIP-14 | Risk register and milestones | Nurzhaina | Medium | 2026-10-23 | This document complete |
| DIP-15 | Supervisor review of deliverables and response to comments | Both | Critical | 2026-10-23 | Comments and actions recorded in `docs/11-supervisor-feedback.md` |
| DIP-16 | Tag `pre-defense-1` and prepare submission summary | Both | Critical | 2026-10-24 | Tag created; summary of done work, limitations, outstanding tasks |

## 6. Risk register

Probability (P) and impact (I): Low / Medium / High.

| ID | Risk | P | I | Mitigation | Owner |
|---|---|---|---|---|---|
| R-1 | Real schedule data is not available | M | M | Synthetic data generator based on the structure of the public schedule; document assumption A-1 | Zeyin |
| R-2 | Difficulty finding interview participants (especially schedulers) | M | H | Contact dean's office early via supervisor; accept teachers/curators as proxies; record limitations | Both |
| R-3 | Domain complexity underestimated (subgroups, odd/even weeks, joint lectures) | M | H | Clarify in interviews; model joint lectures via `schedule_entry_group`; keep edge cases in Should/Could | Zeyin |
| R-4 | Frontend–backend integration delays | M | M | Contract-first OpenAPI spec; mock server for frontend; weekly integration | Both |
| R-5 | Scope creep (automatic generation) | H | M | Generation is a stretch goal only; MoSCoW priorities reviewed with supervisor | Both |
| R-6 | Unfamiliar technologies (FastAPI, React specifics) | M | M | Short spikes in first two weeks; follow official tutorials; pair sessions | Both |
| R-7 | Workload from other courses / exams | H | M | Buffer in milestones; tasks small enough for 1–3 days | Both |
| R-8 | One member unavailable for a period | L | H | Shared documentation, code review keeps both familiar with the codebase | Both |
| R-9 | Security flaws (broken access control) | M | H | RBAC tests for every endpoint; OWASP checklist; no secrets in repo | Zeyin |
| R-10 | Performance targets not met on large dataset | L | M | Indexing, query plans (`EXPLAIN ANALYZE`), caching of read endpoints | Zeyin |

## 7. Dependencies

| Dependency | Needed for | Status |
|---|---|---|
| Access to users for interviews (students, teachers, schedulers) | Evidence of relevance, usability study | _TODO_ |
| Sample of the current university schedule | Data model validation, realistic test data | _TODO_ |
| Supervisor availability for checkpoint reviews | Supervisor feedback, milestone approval | _TODO_ |
| GitHub and YouTrack access for supervisor and committee | Pre-defense assessment | _TODO_ |
| OpenAPI spec (backend) → API integration (frontend) | Frontend development | Planned at M2 |
