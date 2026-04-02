# 🤖 My Custom Agents

A curated collection of **GitHub Copilot custom agents** built for AI-assisted full-stack software development. These agents form a structured, multi-tier engineering team — each agent has a clearly defined role, strict boundaries, and a zero-ambiguity operating model so that every decision is made at the right level of context.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Agent Architecture](#-agent-architecture)
- [Session Modes](#-session-modes)
- [The Core Workflow](#-the-core-workflow)
- [Tier 1 — Orchestration Layer](#-tier-1--orchestration-layer)
- [Tier 2 — Team Leads](#-tier-2--team-leads)
- [Tier 3 — Specialist Dev Agents](#-tier-3--specialist-dev-agents)
- [Quality Gates](#-quality-gates)
- [Tech Stack Coverage](#-tech-stack-coverage)
- [Agent File Reference](#-agent-file-reference)

---

## 🌟 Overview

This repository provides a complete **AI agent team** for building software projects end-to-end. Rather than using a single generic AI assistant, this system decomposes development work into specialised agents that mirror a real engineering organisation:

- A **Requirements Analyst** clarifies what needs to be built before any code is written.
- An **Architect** defines every technical decision before development begins.
- A **Design Lead** produces design tokens and component specs that unblock all UI work.
- A **DevOps Lead** produces Docker setup, CI/CD pipelines, and environment configuration.
- **Team Leads** manage domain-specific pipelines (backend, frontend, mobile, database).
- **Developer agents** implement exactly one unit of work per session — no guessing, no decisions.
- **Checker agents** review every output against the spec before tests run.
- **Tester agents** write and execute tests, reporting pass/fail without touching implementation.
- A **Documentation Agent** records what was built in a single session notes file.

The result is a disciplined, traceable pipeline from raw requirement to fully-reviewed, tested code.

---

## 🏗 Agent Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         TIER 1 — ORCHESTRATION                          │
│                                                                         │
│  User ──► Orchestrator ──► Requirements Analyst ──► Planning Agent      │
│                │                                          │             │
│                │◄────────────── Architect ◄───────────────┘             │
│                │                     ▲                                  │
│                │◄────────── QA Lead ─┘                                  │
│                │◄────────── Documentation Agent (session end only)      │
└────────────────┼────────────────────────────────────────────────────────┘
                 │ distributes domain plans
                 ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          TIER 2 — TEAM LEADS                            │
│                                                                         │
│  Spring Boot TL  │ FastAPI TL   │ Flutter TL  │ Frontend TL  │ DB TL   │
│  Design Lead     │ DevOps Lead                                          │
└─────────────────────────────────────────────────────────────────────────┘
                 │ spawn and manage
                 ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      TIER 3 — SPECIALIST AGENTS                         │
│                                                                         │
│  Dev Agents           Checker Agents          Tester Agents             │
│  ─────────────────    ──────────────────      ──────────────────        │
│  Controller Dev  ───► Springboot Checker ───► Springboot Tester         │
│  Service Dev                                                            │
│  Repository Dev                                                         │
│  Security Dev                                                           │
│                                                                         │
│  Route Dev       ───► FastAPI Checker    ───► FastAPI Tester            │
│  Service Dev (FA)                                                       │
│  Repository Dev (FA)                                                    │
│                                                                         │
│  Flutter UI Dev  ───► Flutter Checker    ───► Flutter Tester            │
│  Flutter Plat Dev                                                       │
│                                                                         │
│  React Comp Dev  ───► Frontend Checker   ───► Frontend Tester           │
│  Next.js Page Dev                                                       │
│                                                                         │
│  DB Schema Dev   ───► Database Checker   ───► Database Tester           │
│  DB Migration Dev                                                       │
└─────────────────────────────────────────────────────────────────────────┘
```

Every dev agent output passes through **two quality gates** before being considered complete:

1. **Gate 1 — Checker**: Static review against the task spec and interface contracts.
2. **Gate 2 — Tester**: Automated tests written and run against the actual output.

---

## 🎛 Session Modes

At the start of every session, the Orchestrator identifies which teams are needed and activates only those leads.

| Mode | Active Teams |
|---|---|
| `backend-only` | Backend TL + Database TL |
| `fullstack-web` | Backend TL + Frontend TL + Database TL + Design Lead |
| `fullstack-mobile` | Backend TL + Flutter TL + Database TL + Design Lead |
| `fullstack-all` | All leads active |
| `frontend-only` | Frontend TL + Design Lead |
| `mobile-only` | Flutter TL + Design Lead |
| Custom | Any combination of leads |

> **Design Lead** is always activated for sessions with UI work — UI dev agents are fully blocked until design tokens are delivered.

---

## 🔄 The Core Workflow

```
1. User submits raw task to Orchestrator
         │
         ▼
2. Requirements Analyst clarifies all ambiguities
   (consults Architect for tech gaps, QA Lead for testability gaps)
   Max 2 rounds of questions → Enriched Requirements Document
         │
         ▼
3. Planning Agent transforms enriched requirements into domain plans
   (consults Architect for design, QA Lead for criteria, Leads for feasibility)
   Output: per-domain task lists with zero ambiguity
         │
         ▼
4. Orchestrator distributes plans to active Team Leads
   Design Lead runs first (for UI sessions) — unblocks Flutter + Frontend
         │
         ▼
5. Team Leads spawn dev agents (one unit of work per session)
   Dev → Checker (Gate 1) → Tester (Gate 2)
   Failed gates: fresh dev agent spawned, never reused
         │
         ▼
6. Teams notify Orchestrator on completion
   Full-stack: DB migrations → Backend API → Frontend / Mobile
         │
         ▼
7. Documentation Agent runs (only after ALL teams complete)
   Produces one session notes file: docs/session-YYYY-MM-DD.md
         │
         ▼
8. Orchestrator delivers final merged output to user
```

### State Tracking

The Orchestrator maintains a **master state file** tracking:
- Current project phase: `requirements → planning → development → review → integration → documentation → delivery`
- Status of each active lead: `pending | in-progress | completed`
- Unresolved conflicts or blockers
- ISO 8601 timestamps for every phase transition

---

## 🎯 Tier 1 — Orchestration Layer

These agents form the command and coordination layer. The user only ever interacts with the **Orchestrator**.

### Orchestrator
> **File:** `orchestrator.agent.md` | **Model:** Claude Opus 4.6

The engineering manager of the entire agent team. The **sole user-facing interface**. Receives raw tasks, routes them through the full pipeline, tracks progress via state files, handles conflict resolution, and delivers final output.

**Key responsibilities:**
- Routes all tasks to the Requirements Analyst first — no exceptions.
- Relays analyst questions to the user verbatim (no filtering or rephrasing).
- Distributes domain-specific plans to the relevant Team Leads.
- Activates Design Lead before any UI work begins.
- Coordinates deployment order in full-stack modes (DB → Backend → Frontend → Mobile).
- Escalates cross-domain conflicts to the Architect for resolution.

**Never:** writes code, makes technical assumptions on behalf of the user, communicates directly with Tier 3 agents.

---

### Requirements Analyst
> **File:** `requirement-analyst.agent.md` | **Model:** Claude Sonnet 4.6

The first line of defence against ambiguity. Takes raw requirements, consults the Architect and QA Lead, and produces structured questions for the user. Operates within a **max 2-round** clarification loop.

**Output:** An **Enriched Requirements Document** (`docs/enriched-requirements.md`) containing:
- Original requirements (verbatim)
- User answers from all rounds
- Documented assumptions tagged `[ASSUMPTION — RISK: HIGH/LOW]`
- Technical decisions confirmed by the user
- Acceptance criteria from the QA Lead

**Question categories:** Functional · Technical Decisions · Edge Cases · Constraints/NFRs · Platform Decisions · API Contract · UX/Design

**Priority levels:** `[BLOCKING]` (cannot proceed) · `[NICE-TO-HAVE]` (can proceed with assumption)

---

### Planning Agent
> **File:** `planning-agent.agent.md` | **Model:** Claude Opus 4.6

Transforms enriched requirements into fully-specified, executable task plans. **Only accepts enriched requirements** — rejects raw requirements.

**Consultation process:**
1. Architect → architecture design, patterns, interface contracts, OpenAPI spec
2. QA Lead → per-task acceptance criteria and Tester criteria
3. All active Team Leads → feasibility and domain constraints

**Task specification format** (every task includes):
- Description · Implementation Strategy · Interface Contracts
- Files to Create / Modify
- Acceptance Criteria (Given / When / Then format)
- Parallelism annotation (`[PARALLEL]` or `[SEQUENTIAL:depends-on-TASK-XXX]`)
- Target dev agent type · TODO sub-step checklist

**Planning order** (full-stack): Design Lead → Database → Backend → Frontend + Mobile (parallel)

---

### Architect
> **File:** `architect.agent.md` | **Model:** Claude Opus 4.6

The technical authority of the team. Makes every system-level technical decision so that no downstream agent has to guess.

**Responsibilities:**
- Identifies user-input-required tech decisions (auth strategy, API style, DB choice, etc.)
- Defines architecture pattern, package structure, and design patterns per component
- Defines all **interface contracts** (method signatures, input/output types, exception types) before dev begins
- In full-stack modes: authors the **OpenAPI specification** — the single source of truth for all consumers
- Generates TypeScript types (React/Next.js), Freezed models (Flutter), and Pydantic models (FastAPI) from the OpenAPI spec
- Designs observability strategy (structured logging, correlation IDs, health check contracts)
- Defines API versioning strategy and caching approach
- Resolves cross-domain technical conflicts via Architecture Decision Records (ADRs)

**ADR format:** Context · Decision · Alternatives considered · Consequences

---

### QA Lead
> **File:** `qa-lead.agent.md` | **Model:** Claude Sonnet 4.6

Defines quality standards and the checklists used by every Checker and Tester agent. Does not write code, run tests, or name specific test tools — that is the Tester's responsibility.

**Responsibilities:**
- Defines per-task acceptance criteria using **Given / When / Then** format
- Categorises test cases: happy path · error · validation · edge case · integration · accessibility · security
- Defines layer-specific Checker checklist additions beyond generic checks
- Sets **coverage thresholds** as percentages per layer (e.g., 90% controllers, 85% services)
- Defines NFR criteria: performance (p95/p99 latency), accessibility (WCAG 2.1 AA), security

**Rule:** All acceptance criteria must be measurable and specific before development begins.

---

### Documentation Agent
> **File:** `documentation-agent.agent.md` | **Model:** Claude Sonnet 4.6

Triggered **only once** after all active teams report completion. Produces a single session notes file.

**Output:** One `docs/session-YYYY-MM-DD.md` file containing:
- **Summary** — what was built in this session (2–4 sentences)
- **What Was Built** — feature list with brief descriptions
- **Decisions Made** — key Architect decisions and rationale
- **Agents Involved** — which leads and agents were active
- **Known Issues** — anything flagged but not resolved
- **Dependencies Added** — new packages, services, or infrastructure
- **Next Steps** — recommended follow-up tasks

**Never:** generates API docs, ADRs, changelogs, or READMEs — those are out of scope.

---

## 👔 Tier 2 — Team Leads

Team Leads receive domain-specific plans from the Planning Agent, split work into atomic tasks, spawn dev agents, and manage the `dev → checker → tester` pipeline. All team leads maintain a **team state file** with ISO 8601 timestamps.

### Spring Boot Team Lead
> **File:** `springboot-team-lead.agent.md` | **Model:** Claude Sonnet 4.6

Manages Java Spring Boot backend development using a strict layered architecture.

**Architecture enforced:**
```
Controller → Service → Repository → Database
```
- Spring Security with JWT (`@PreAuthorize` method-level security)
- Spring Data JPA for all database access
- Global exception handling via `@RestControllerAdvice`
- OpenAPI 3.0 documentation via SpringDoc
- Flyway for database migrations, `@Async` + `ApplicationEventPublisher` for async patterns

**Dev agent split:**

| Dev Agent | One Task Equals |
|---|---|
| Controller Dev | ONE REST endpoint (handler + DTOs + validation + OpenAPI annotations) |
| Service Dev | ONE service method (business logic + transaction + event publishing) |
| Repository Dev | ONE repository interface (CRUD + custom queries + specifications) |
| Security Dev | ONE security concern (JWT config OR role guards OR CORS — never multiple) |

---

### FastAPI Team Lead
> **File:** `fastapi-team-lead.agent.md` | **Model:** Claude Sonnet 4.6

Manages Python FastAPI backend development with a strict service-layer architecture and async-first patterns.

**Architecture enforced:**
```
Routers (HTTP) → Services (business logic) → Repositories (data access)
```
- Pydantic v2 for all request/response schemas (never raw dicts)
- Async SQLAlchemy 2.0 sessions throughout
- `Depends()` for all cross-cutting concerns (DB session, auth, service injection)
- `pydantic-settings` for all environment config
- Alembic for database migrations

**Dev agent split:**

| Dev Agent | One Task Equals |
|---|---|
| FastAPI Route Dev | ONE endpoint (route function + Pydantic schemas + Depends() wiring) |
| FastAPI Service Dev | ONE service class method (business logic + validation + custom exceptions) |
| FastAPI Repository Dev | ONE repository function set (CRUD operations for one model) |

---

### Flutter Team Lead
> **File:** `flutter-team-lead.agent.md` | **Model:** Claude Sonnet 4.6

Manages Flutter mobile development with clean architecture.

**Architecture enforced:**
```
Presentation (Widgets/Screens) → Domain (Providers/State) → Data (Repositories/API)
```
- **Riverpod** for state management (`@riverpod` code generation)
- **go_router** for declarative navigation (no `Navigator.push` ever)
- **Freezed** + `json_serializable` for immutable, type-safe API models
- **Dio** with interceptors (auth, retry, logging)
- `Either`/`Result` types for error handling in repositories
- Offline sync via **drift** + **workmanager** when specified

**Dev agent split:**

| Dev Agent | One Task Equals |
|---|---|
| Flutter UI Dev | ONE screen or ONE reusable widget tree |
| Flutter Platform Dev | ONE Riverpod provider/notifier or ONE repository |

---

### Frontend Team Lead
> **File:** `frontend-team-lead.agent.md` | **Model:** Gemini 3.1 Pro (Preview)

Manages React and Next.js frontend development.

**Standards enforced:**
- TypeScript strict mode — types match the OpenAPI contract
- Next.js App Router conventions (Server Components by default)
- Design token usage (no hardcoded colors, spacing, or fonts)
- Component composition patterns
- `next/dynamic` for code splitting heavy client-only components

**Dev agent split:**

| Dev Agent | One Task Equals |
|---|---|
| React Component Dev | ONE reusable component (typed props + hook + accessibility) |
| Next.js Page Dev | ONE page/route segment (page + loading + error files) |

---

### Database Team Lead
> **File:** `database-team-lead.agent.md` | **Model:** Claude Sonnet 4.6

Owns schema design, ORM mappings, migrations, query performance, and indexing strategy. Supports both Spring Boot (JPA + Flyway) and FastAPI (SQLAlchemy 2.0 + Alembic).

**Core rules:**
- Migrations are **always sequential** — never parallel
- Schema changes always have migration scripts — no ORM auto-DDL in production
- Index every frequently queried column proactively
- Foreign keys are mandatory; soft delete (`deleted_at`) preferred over physical deletion
- Audit columns (`created_at`, `updated_at`) on every table
- Zero-downtime migration patterns required for production changes

**Dev agent split:**

| Dev Agent | One Task Equals |
|---|---|
| Database Schema Dev | ONE entity/model class with relationships, indexes, constraints |
| Database Migration Dev | ONE migration script (create, alter, index, data) with rollback |

---

### Design Lead
> **File:** `design-lead.agent.md` | **Model:** Claude Sonnet 4.6

Produces the design system package that unblocks all UI work. Runs **before** any Flutter or Frontend dev tasks begin.

**Output:** `docs/design-system.md` containing:
- Full colour, typography, spacing, border-radius, and shadow tokens (semantic naming)
- Flutter `ThemeData` mapping + `AppSpacing` constants
- Component specs for all UI components (all states: default, hover, pressed, disabled, loading)
- Layout grid and breakpoints for web and mobile
- Iconography library and sizes

**Rule:** All UI dev work is blocked until this file is delivered.

---

### DevOps Lead
> **File:** `devops-lead.agent.md` | **Model:** Claude Sonnet 4.6

Owns all infrastructure-as-code, containerisation, CI/CD pipelines, and environment configuration. Runs during project setup and for infrastructure changes.

**Output:**
- Multi-stage `Dockerfile` per service (non-root, health checks, pinned versions)
- `docker-compose.yml` for local development (services, volumes, health dependencies)
- `.env.example` with all variables documented and marked `# REQUIRED`
- `.github/workflows/ci.yml` (lint + test + build) and `deploy.yml` (skeleton)
- Health check endpoint contract (`/health`, `/health/ready`, `/health/live`)

**Never:** modifies application source code.

---

## 🔧 Tier 3 — Specialist Dev Agents

Each dev agent implements exactly **one unit of work** per session and makes **zero decisions**. All decisions are made upstream by the Architect, Team Lead, and Planning Agent.

### Spring Boot Agents

| Agent | File | Responsibility |
|---|---|---|
| Controller Dev | `springboot/springboot-controller-dev.agent.md` | ONE REST endpoint (routing, request validation, response mapping, pagination) |
| Service Dev | `springboot/springboot-service-dev.agent.md` | ONE service method (business logic, transactions, domain event publishing) |
| Repository Dev | `springboot/springboot-repository-dev.agent.md` | ONE Spring Data JPA repository (CRUD + JPQL + Specifications + locking) |
| Security Dev | `springboot/springboot-security-dev.agent.md` | ONE security concern (JWT filter, role config, CORS, audit logging, rate limiting) |

### FastAPI Agents

| Agent | File | Responsibility |
|---|---|---|
| Route Dev | `fastapi/fastapi-route-dev.agent.md` | ONE async route handler + Pydantic schemas + service injection |
| Service Dev | `fastapi/fastapi-service-dev.agent.md` | ONE async service method (business logic + domain exceptions) |
| Repository Dev | `fastapi/fastapi-repository-dev.agent.md` | ONE SQLAlchemy 2.0 async repository (CRUD + filtering + pagination) |

### Flutter Agents

| Agent | File | Responsibility |
|---|---|---|
| UI Dev | `flutter/flutter-ui-dev.agent.md` | ONE screen or reusable widget (layout, ThemeData tokens, form validation) |
| Platform Dev | `flutter/flutter-platform-dev.agent.md` | ONE Riverpod provider/notifier or ONE repository (state + data layer + caching) |

### Frontend Agents

| Agent | File | Responsibility |
|---|---|---|
| React Component Dev | `frontend/react-component-dev.agent.md` | ONE reusable React component (TypeScript, typed props, accessibility, Suspense) |
| Next.js Page Dev | `frontend/nextjs-page-dev.agent.md` | ONE Next.js App Router page (page + loading + error + ISR/SSG strategy) |

### Database Agents

| Agent | File | Responsibility |
|---|---|---|
| Schema Dev | `database/database-schema-dev.agent.md` | ONE ORM entity (JPA) or model (SQLAlchemy 2.0) — relationships, indexes, soft delete |
| Migration Dev | `database/database-migration-dev.agent.md` | ONE migration script with upgrade + rollback — zero-downtime patterns |

---

## ✅ Quality Gates

Every dev agent output passes through two sequential quality gates before a task is considered complete.

### Gate 1 — Checker (Static Review)

Checker agents **report only — never fix code**. A failed check sends the work back to a **fresh** dev agent.

| Checker | File | Key Checks |
|---|---|---|
| Springboot Checker | `springboot/springboot-checker.agent.md` | Layer violations, injection patterns, transactions, OpenAPI annotations, logging, pagination |
| FastAPI Checker | `fastapi/fastapi-checker.agent.md` | API contract compliance, service layer separation, async patterns, Pydantic v2, security |
| Flutter Checker | `flutter/flutter-checker.agent.md` | Freezed model alignment, Riverpod patterns, widget quality, lifecycle/dispose, go_router |
| Frontend Checker | `frontend/frontend-checker.agent.md` | TypeScript strict compliance, App Router conventions, accessibility, bundle performance |
| Database Checker | `database/database-checker.agent.md` | Schema compliance, indexing, rollback completeness, soft delete, zero-downtime compliance |

### Gate 2 — Tester (Test Execution)

Tester agents **write and run tests, then report results — never modify the implementation**. Spawned only after Checker passes.

| Tester | File | Test Approach |
|---|---|---|
| Springboot Tester | `springboot/springboot-tester.agent.md` | JUnit 5 + Mockito + `@WebMvcTest` / `@DataJpaTest` / `@SpringBootTest` + Testcontainers |
| FastAPI Tester | `fastapi/fastapi-tester.agent.md` | pytest + `httpx.AsyncClient` + `ASGITransport` + Testcontainers |
| Flutter Tester | `flutter/flutter-tester.agent.md` | `testWidgets` + Mocktail + Riverpod overrides + golden tests |
| Frontend Tester | `frontend/frontend-tester.agent.md` | Vitest/Jest + React Testing Library + `jest-axe` accessibility audits |
| Database Tester | `database/database-tester.agent.md` | Testcontainers (real PostgreSQL) + migration validation + EXPLAIN ANALYZE index checks |

---

## 🛠 Tech Stack Coverage

| Domain | Technology |
|---|---|
| **Backend (Java)** | Spring Boot 3, Spring Data JPA, Spring Security (JWT), Flyway, SpringDoc OpenAPI |
| **Backend (Python)** | FastAPI, Pydantic v2, SQLAlchemy 2.0 (async), Alembic, pydantic-settings |
| **Mobile** | Flutter, Dart, Riverpod, go_router, Freezed, Dio, drift (offline), Mocktail |
| **Frontend** | React, Next.js 14+ (App Router), TypeScript strict mode, next-intl |
| **Database** | PostgreSQL (primary), JPA entities / SQLAlchemy 2.0 models |
| **Infrastructure** | Docker (multi-stage), docker-compose, GitHub Actions CI/CD |
| **Testing (Java)** | JUnit 5, Mockito, Testcontainers |
| **Testing (Python)** | pytest, httpx, pytest-asyncio, Testcontainers |
| **Testing (Dart)** | Flutter test framework, Mocktail, integration_test |
| **Testing (JS/TS)** | Vitest/Jest, React Testing Library, jest-axe, MSW |
| **API Design** | OpenAPI 3.0 spec (single source of truth across all platforms) |

---

## 📁 Agent File Reference

### Tier 1 — Orchestration

| File | Agent |
|---|---|
| `orchestrator.agent.md` | Orchestrator — central command and user interface |
| `planning-agent.agent.md` | Planning Agent — requirement-to-task translator |
| `requirement-analyst.agent.md` | Requirements Analyst — ambiguity eliminator |
| `architect.agent.md` | Architect — technical authority |
| `qa-lead.agent.md` | QA Lead — quality standards and acceptance criteria |
| `documentation-agent.agent.md` | Documentation Agent — session recorder |

### Tier 2 — Team Leads

| File | Agent |
|---|---|
| `springboot-team-lead.agent.md` | Spring Boot Team Lead |
| `fastapi-team-lead.agent.md` | FastAPI Team Lead |
| `flutter-team-lead.agent.md` | Flutter Team Lead |
| `frontend-team-lead.agent.md` | Frontend Team Lead |
| `database-team-lead.agent.md` | Database Team Lead |
| `design-lead.agent.md` | Design Lead — design tokens, component specs, ThemeData |
| `devops-lead.agent.md` | DevOps Lead — Docker, CI/CD, env vars, health checks |

### Spring Boot Team

| File | Agent |
|---|---|
| `springboot/springboot-controller-dev.agent.md` | Controller Dev |
| `springboot/springboot-service-dev.agent.md` | Service Dev |
| `springboot/springboot-repository-dev.agent.md` | Repository Dev |
| `springboot/springboot-security-dev.agent.md` | Security Dev |
| `springboot/springboot-checker.agent.md` | Spring Boot Checker |
| `springboot/springboot-tester.agent.md` | Spring Boot Tester |

### FastAPI Team

| File | Agent |
|---|---|
| `fastapi/fastapi-route-dev.agent.md` | FastAPI Route Dev |
| `fastapi/fastapi-service-dev.agent.md` | FastAPI Service Dev |
| `fastapi/fastapi-repository-dev.agent.md` | FastAPI Repository Dev |
| `fastapi/fastapi-checker.agent.md` | FastAPI Checker |
| `fastapi/fastapi-tester.agent.md` | FastAPI Tester |

### Flutter Team

| File | Agent |
|---|---|
| `flutter/flutter-ui-dev.agent.md` | Flutter UI Dev |
| `flutter/flutter-platform-dev.agent.md` | Flutter Platform Dev |
| `flutter/flutter-checker.agent.md` | Flutter Checker |
| `flutter/flutter-tester.agent.md` | Flutter Tester |

### Frontend Team

| File | Agent |
|---|---|
| `frontend/react-component-dev.agent.md` | React Component Dev |
| `frontend/nextjs-page-dev.agent.md` | Next.js Page Dev |
| `frontend/frontend-checker.agent.md` | Frontend Checker |
| `frontend/frontend-tester.agent.md` | Frontend Tester |

### Database Team

| File | Agent |
|---|---|
| `database/database-schema-dev.agent.md` | Database Schema Dev |
| `database/database-migration-dev.agent.md` | Database Migration Dev |
| `database/database-checker.agent.md` | Database Checker |
| `database/database-tester.agent.md` | Database Tester |

---

## 💡 Design Principles

1. **Zero-ambiguity tasks** — Every dev agent receives a task so well-specified that it makes zero decisions. Implementation strategy, interface contracts, and acceptance criteria are all defined before a dev agent is spawned.

2. **Context-appropriate decisions** — Decisions are made where context exists. The Architect has the most technical context, so the Architect makes technical decisions. The user makes preference decisions. Dev agents make no decisions.

3. **Strict layer boundaries** — No skipping layers (e.g., controllers never call repositories; widgets never call APIs directly; routes never contain business logic). Every violation is caught by the Checker.

4. **One concern per session** — Every dev agent handles exactly one endpoint, one component, one migration, one provider. This keeps context windows clean and outputs reviewable.

5. **Fresh agents on failure** — Failed Checker or Tester reviews result in a fresh dev agent being spawned. Agents that produced failing work are never reused for the retry.

6. **API contract as single source of truth** — In full-stack modes, the OpenAPI spec defined by the Architect is the canonical contract that all consumers (React, Flutter) and producers (FastAPI, Spring Boot) validate against.

7. **Design system as unblocking gate** — In sessions with UI work, the Design Lead delivers tokens and component specs before any UI dev task is assigned. This eliminates visual decision-making from all dev agents.


