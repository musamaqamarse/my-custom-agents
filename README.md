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
- **Team Leads** manage domain-specific pipelines (backend, frontend, mobile, database).
- **Developer agents** implement exactly one unit of work per session — no guessing, no decisions.
- **Checker agents** review every output against the spec before tests run.
- **Tester agents** write and execute tests, reporting pass/fail without touching implementation.
- A **Documentation Agent** produces accurate docs only after all work is complete.

The result is a disciplined, traceable pipeline from raw requirement to fully-reviewed, tested, and documented code.

---

## 🏗 Agent Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        TIER 1 — ORCHESTRATION                       │
│                                                                     │
│  User ──► Orchestrator ──► Requirements Analyst ──► Planning Agent  │
│                │                                         │          │
│                │◄──────────── Architect ◄────────────────┘          │
│                │                    ▲                               │
│                │◄──────── QA Lead ──┘                               │
│                │◄──────── Documentation Agent (end of cycle only)   │
└────────────────┼────────────────────────────────────────────────────┘
                 │ distributes domain plans
                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        TIER 2 — TEAM LEADS                          │
│                                                                     │
│  Spring Boot Team Lead │ FastAPI Team Lead │ Flutter Team Lead      │
│  Frontend Team Lead    │ Database Team Lead                         │
└─────────────────────────────────────────────────────────────────────┘
                 │ spawn and manage
                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     TIER 3 — SPECIALIST AGENTS                      │
│                                                                     │
│  Dev Agents          Checker Agents         Tester Agents           │
│  ──────────────────  ──────────────────     ──────────────────      │
│  Controller Dev  ──► Springboot Checker ──► Springboot Tester       │
│  Service Dev                                                        │
│  Repository Dev                                                     │
│  Security Dev                                                       │
│                                                                     │
│  FastAPI Route Dev ─► FastAPI Checker   ──► FastAPI Tester          │
│                                                                     │
│  Flutter UI Dev ───► Flutter Checker    ──► Flutter Tester          │
│  Flutter Platform Dev                                               │
│                                                                     │
│  React Component Dev ─► Frontend Checker ─► Frontend Tester        │
│  Next.js Page Dev                                                   │
│                                                                     │
│  DB Schema Dev ───► Database Checker    ──► Database Tester         │
│  DB Migration Dev                                                   │
└─────────────────────────────────────────────────────────────────────┘
```

Every dev agent output passes through **two quality gates** before being considered complete:

1. **Gate 1 — Checker**: Static review against the task spec and interface contracts.
2. **Gate 2 — Tester**: Automated tests written and run against the actual output.

---

## 🎛 Session Modes

At the start of every session, the Orchestrator identifies which teams are needed and activates only those leads. This keeps agent spawning lean and context-efficient.

| Mode | Active Teams |
|---|---|
| `backend-only` | Backend Lead + Database Lead |
| `fullstack-web` | Backend Lead + Frontend Lead |
| `fullstack-mobile` | Backend Lead + Mobile Lead |
| `fullstack-all` | All leads active |
| `frontend-only` | Frontend Lead |
| `mobile-only` | Flutter Lead |
| Custom | Any combination of leads |

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
         │
         ▼
8. Orchestrator delivers final merged output to user
```

### State Tracking

The Orchestrator maintains a **master state file** tracking:
- Current project phase: `requirements → planning → development → review → integration → documentation → delivery`
- Status of each active lead: `pending | in-progress | completed`
- Unresolved conflicts or blockers
- Timestamps for every phase transition

---

## 🎯 Tier 1 — Orchestration Layer

These agents form the command and coordination layer. The user only ever interacts with the **Orchestrator**.

### Orchestrator
> **File:** `Orchestrator.agent.md` | **Model:** Claude Opus 4.6

The engineering manager of the entire agent team. The **sole user-facing interface**. Receives raw tasks, routes them through the full pipeline, tracks progress via state files, handles conflict resolution, and delivers final output.

**Key responsibilities:**
- Routes all tasks to the Requirements Analyst first — no exceptions.
- Relays analyst questions to the user verbatim (no filtering or rephrasing).
- Distributes domain-specific plans to the relevant Team Leads.
- Coordinates deployment order in full-stack modes (DB → Backend → Frontend → Mobile).
- Escalates cross-domain conflicts to the Architect for resolution.

**Never:** writes code, makes technical assumptions on behalf of the user, communicates directly with Tier 3 agents.

---

### Requirements Analyst
> **File:** `Requirement Analyst.agent.md` | **Model:** Claude Sonnet 4.6

The first line of defence against ambiguity. Takes raw requirements, consults the Architect and QA Lead, and produces structured questions for the user. Operates within a **max 2-round** clarification loop.

**Output:** An **Enriched Requirements Document** containing:
- Original requirements (verbatim)
- User answers from all rounds
- Documented assumptions (for anything unresolved after 2 rounds)
- Technical decisions confirmed by the user
- Acceptance criteria from the QA Lead

**Question categories:** Functional · Technical Decisions · Edge Cases · Constraints/NFRs · Platform Decisions · API Contract · UX/Design

**Priority levels:** `[BLOCKING]` (cannot proceed) · `[NICE-TO-HAVE]` (can proceed with assumption)

---

### Planning Agent
> **File:** `Planning Agent.agent.md` | **Model:** Claude Opus 4.6

Transforms enriched requirements into fully-specified, executable task plans. **Only accepts enriched requirements** — rejects raw requirements.

**Consultation process:**
1. Architect → architecture design, patterns, interface contracts, OpenAPI spec
2. QA Lead → per-task acceptance criteria and Tester criteria
3. All active Team Leads → feasibility and domain constraints

**Task specification format** (every task includes):
- Description · Implementation Strategy · Interface Contracts
- Acceptance Criteria · Parallelism annotation (`[PARALLEL]` or `[SEQUENTIAL:depends-on-TASK-XXX]`)
- Target dev agent type · TODO sub-step checklist

**Sizing rule:** Each task targets ~60% of a dev agent's context window (max ~200 lines output, one concern per task).

---

### Architect
> **File:** `Architect.agent.md` | **Model:** Claude Opus 4.6

The technical authority of the team. Makes every system-level technical decision so that no downstream agent has to guess.

**Responsibilities:**
- Identifies user-input-required tech decisions (auth strategy, API style, DB choice, etc.)
- Defines architecture pattern, package structure, and design patterns per component
- Defines all **interface contracts** (method signatures, input/output types, exception types) before dev begins
- In full-stack modes: authors the **OpenAPI specification** — the single source of truth for all consumers
- Generates TypeScript types (React/Next.js), Freezed models (Flutter), and Pydantic models (FastAPI) from the OpenAPI spec
- Resolves cross-domain technical conflicts between leads via Architecture Decision Records (ADRs)

**ADR format:** Context · Decision · Alternatives considered · Consequences

---

### QA Lead
> **File:** `Qa lead.agent.md` | **Model:** Claude Sonnet 4.6

Defines quality standards and the checklists used by every Checker and Tester agent. Does not write code or tests directly.

**Responsibilities:**
- Defines per-dev-type **Checker review checklists** (controller checklist differs from service checklist)
- Defines per-task **Tester validation criteria** (happy path, error paths, edge cases, integration)
- Sets **coverage thresholds** per layer (e.g., 90% controllers, 85% services, 80% repositories, 75% UI)
- Reviews the final merged output before delivery

**Rule:** All acceptance criteria must be measurable and specific before development begins.

---

### Documentation Agent
> **File:** `Documentation Agent.agent.md` | **Model:** Claude Sonnet 4.6

Triggered **only** after all active teams report completion. Documents the finished product, not work in progress.

**Outputs:**
- **API Documentation** — generated from the actual OpenAPI spec / route implementations
- **README Update** — architecture overview, setup instructions, environment variables, project structure
- **Architecture Decision Records (ADRs)** — formatted from Architect's decisions
- **Platform-Specific Setup Guides** — per-platform install, config, dev server, and production build steps
- **Changelog Entry** — Keep a Changelog format (Added/Changed/Fixed/etc.)
- **Shared Model Documentation** — cross-platform model representations (Pydantic / TypeScript / Freezed)

---

## 👔 Tier 2 — Team Leads

Team Leads receive domain-specific plans from the Planning Agent, split work into atomic tasks, spawn dev agents, and manage the `dev → checker → tester` pipeline.

### Spring Boot Team Lead
> **File:** `Spring Boot Team Lead.agent.md` | **Model:** Claude Sonnet 4.6

Manages Java Spring Boot backend development using a strict layered architecture.

**Architecture enforced:**
```
Controller → Service → Repository → Database
```
- Spring Security with JWT (`@PreAuthorize` method-level security)
- Spring Data JPA for all database access
- Global exception handling via `@RestControllerAdvice`
- OpenAPI 3.0 documentation via SpringDoc
- Flyway for database migrations

**Dev agent split:**

| Dev Agent | One Task Equals |
|---|---|
| Controller Dev | ONE REST controller class |
| Service Dev | ONE service class (interface + implementation) |
| Repository Dev | ONE repository interface + custom queries |
| Security Dev | ONE security concern (JWT filter, config, guard) |

---

### FastAPI Team Lead
> **File:** `Fastapi team lead.agent.md` | **Model:** Claude Sonnet 4.6

Manages Python FastAPI backend development with an async-first approach.

**Architecture enforced:**
```
Routers (HTTP) → Services (business logic) → Repositories (data access)
```
- Pydantic v2 for all request/response schemas (never raw dicts)
- Async SQLAlchemy 2.0 sessions throughout
- `Depends()` for all cross-cutting concerns (DB session, auth, permissions)
- `pydantic-settings` for all environment config
- Alembic for database migrations

**Dev agent split:**

| Dev Agent | One Task Equals |
|---|---|
| FastAPI Route Dev | ONE endpoint (handler + Pydantic schemas + dependencies) |

---

### Flutter Team Lead
> **File:** `Flutter team lead.agent.md` | **Model:** Claude Sonnet 4.6

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

**Dev agent split:**

| Dev Agent | One Task Equals |
|---|---|
| Flutter UI Dev | ONE screen or ONE reusable widget tree |
| Flutter Platform Dev | ONE Riverpod provider/notifier or ONE repository |

---

### Frontend Team Lead
> **File:** `frontend-team-lead.md` | **Model:** Claude Sonnet 4.6

Manages React and Next.js frontend development.

**Standards enforced:**
- TypeScript strict mode throughout — types match the OpenAPI contract
- Next.js App Router conventions
- Design token usage (no hardcoded colors, spacing, or fonts)
- Component composition patterns

**Dev agent split:**

| Dev Agent | One Task Equals |
|---|---|
| React Component Dev | ONE reusable component |
| Next.js Page Dev | ONE page or route |

---

### Database Team Lead
> **File:** `database-team-lead.md` | **Model:** Claude Sonnet 4.6

Owns schema design, ORM mappings, migrations, query performance, and indexing strategy. Supports both Spring Boot (JPA + Flyway/Liquibase) and FastAPI (SQLAlchemy 2.0 + Alembic).

**Core rules:**
- Migrations are **always sequential** — never parallel
- Schema changes always have migration scripts — no ORM auto-DDL in production
- Index every frequently queried column proactively
- Foreign keys are mandatory; soft delete (`deleted_at`) preferred over physical deletion
- Audit columns (`created_at`, `updated_at`) on every table

**Dev agent split:**

| Dev Agent | One Task Equals |
|---|---|
| Database Schema Dev | ONE entity/model class with relationships, indexes, constraints |
| Database Migration Dev | ONE migration script (create, alter, index, data) |

---

## 🔧 Tier 3 — Specialist Dev Agents

Each dev agent implements exactly **one unit of work** per session and makes **zero decisions**. All decisions are made upstream by the Architect, Team Lead, and Planning Agent.

### Spring Boot Agents

| Agent | File | Responsibility |
|---|---|---|
| Controller Dev | `Springboot controller dev.agent.md` | ONE REST controller (routing, request validation, response mapping) |
| Service Dev | `Springboot service dev.agent.md` | ONE service interface + implementation (business logic) |
| Repository Dev | `Springboot repository dev.agent.md` | ONE Spring Data JPA repository + custom JPQL/native queries |
| Security Dev | `Springboot security dev.agent.md` | ONE security concern (JWT filter, security config, method guards) |

### FastAPI Agents

| Agent | File | Responsibility |
|---|---|---|
| Route Dev | `fastapi-route-dev.md` | ONE async route handler + Pydantic schemas + Depends() injection |

### Flutter Agents

| Agent | File | Responsibility |
|---|---|---|
| UI Dev | `Flutter ui dev.agent.md` | ONE screen or reusable widget (layout, styling, navigation, interactions) |
| Platform Dev | `Flutter platform dev.agent.md` | ONE Riverpod provider/notifier or ONE repository (state + data layer) |

### Frontend Agents

| Agent | File | Responsibility |
|---|---|---|
| React Component Dev | `react-component-dev.md` | ONE reusable React component (TypeScript, typed props) |
| Next.js Page Dev | `nextjs-page-dev.md` | ONE Next.js App Router page or layout |

### Database Agents

| Agent | File | Responsibility |
|---|---|---|
| Schema Dev | `database-schema-dev.md` | ONE ORM entity (JPA) or model (SQLAlchemy 2.0) with all relationships and indexes |
| Migration Dev | `database-migration-dev.md` | ONE migration script with upgrade + rollback (Flyway or Alembic) |

---

## ✅ Quality Gates

Every dev agent output passes through two sequential quality gates before a task is considered complete.

### Gate 1 — Checker (Static Review)

Checker agents **report only — never fix code**. A failed check sends the work back to a **fresh** dev agent.

| Checker | File | Key Checks |
|---|---|---|
| Springboot Checker | `Springboot checker.agent.md` | Layer boundary violations, Spring annotation correctness, error handling, interface contract compliance |
| FastAPI Checker | `Fastapi-checker.md` | API contract compliance (URL, method, Pydantic schemas, status codes), async patterns, Pydantic v2 usage |
| Flutter Checker | `Flutter checker.agent.md` | Freezed model alignment, Riverpod patterns, widget quality, go_router usage, null safety |
| Frontend Checker | `frontend-checker.md` | TypeScript strict mode, component prop types, API contract type alignment, design token usage |
| Database Checker | `database-checker.md` | Schema compliance, relationship correctness, index coverage, migration ordering, rollback completeness, N+1 risks |

**Common checklist items across all checkers:**
- ✅ Spec compliance (requirement by requirement)
- ✅ Interface contract compliance (method signatures, types)
- ✅ Error handling coverage (all exception paths)
- ✅ No TODO comments or placeholder code
- ✅ No truncated methods

### Gate 2 — Tester (Test Execution)

Tester agents **write and run tests, then report results — never modify the implementation**. Spawned only after Checker passes.

| Tester | File | Test Approach |
|---|---|---|
| Springboot Tester | `Springboot tester.agent.md` | JUnit 5 + Mockito + `@SpringBootTest` + Testcontainers |
| FastAPI Tester | `fastapi-tester.md` | pytest + `httpx.AsyncClient` + `ASGITransport` + Testcontainers |
| Flutter Tester | `Flutter tester.agent.md` | `testWidgets` + Mocktail + Riverpod `ProviderScope` overrides |
| Frontend Tester | `frontend-tester.md` | Jest + React Testing Library + MSW for API mocking |
| Database Tester | `database-tester.md` | Testcontainers (real PostgreSQL) + migration validation + ORM mapping tests |

**All testers validate:**
- Happy path (expected normal flow)
- Error paths (every error scenario returns correct exception/status)
- Edge cases (boundary conditions from acceptance criteria)
- Integration (components work together correctly)

---

## 🛠 Tech Stack Coverage

| Domain | Technology |
|---|---|
| **Backend (Java)** | Spring Boot 3, Spring Data JPA, Spring Security (JWT), Flyway, SpringDoc OpenAPI |
| **Backend (Python)** | FastAPI, Pydantic v2, SQLAlchemy 2.0 (async), Alembic, pydantic-settings |
| **Mobile** | Flutter, Dart, Riverpod, go_router, Freezed, Dio, Mocktail |
| **Frontend** | React, Next.js (App Router), TypeScript strict mode |
| **Database** | PostgreSQL (primary), JPA entities / SQLAlchemy 2.0 models |
| **Testing (Java)** | JUnit 5, Mockito, Testcontainers |
| **Testing (Python)** | pytest, httpx, pytest-asyncio, Testcontainers |
| **Testing (Dart)** | Flutter test framework, Mocktail |
| **Testing (JS/TS)** | Jest, React Testing Library, MSW |
| **API Design** | OpenAPI 3.0 spec (single source of truth across all platforms) |

---

## 📁 Agent File Reference

### Orchestration

| File | Agent |
|---|---|
| `Orchestrator.agent.md` | Orchestrator — central command and user interface |
| `Planning Agent.agent.md` | Planning Agent — requirement-to-task translator |
| `Requirement Analyst.agent.md` | Requirements Analyst — ambiguity eliminator |
| `Architect.agent.md` | Architect — technical authority |
| `Qa lead.agent.md` | QA Lead — quality standards and criteria |
| `Documentation Agent.agent.md` | Documentation Agent — post-completion technical writer |

### Spring Boot Team

| File | Agent |
|---|---|
| `Spring Boot Team Lead.agent.md` | Spring Boot Team Lead |
| `Springboot controller dev.agent.md` | Controller Dev |
| `Springboot service dev.agent.md` | Service Dev |
| `Springboot repository dev.agent.md` | Repository Dev |
| `Springboot security dev.agent.md` | Security Dev |
| `Springboot checker.agent.md` | Spring Boot Checker |
| `Springboot tester.agent.md` | Spring Boot Tester |

### FastAPI Team

| File | Agent |
|---|---|
| `Fastapi team lead.agent.md` | FastAPI Team Lead |
| `fastapi-route-dev.md` | FastAPI Route Dev |
| `Fastapi-checker.md` | FastAPI Checker |
| `fastapi-tester.md` | FastAPI Tester |

### Flutter Team

| File | Agent |
|---|---|
| `Flutter team lead.agent.md` | Flutter Team Lead |
| `Flutter ui dev.agent.md` | Flutter UI Dev |
| `Flutter platform dev.agent.md` | Flutter Platform Dev |
| `Flutter checker.agent.md` | Flutter Checker |
| `Flutter tester.agent.md` | Flutter Tester |

### Frontend Team

| File | Agent |
|---|---|
| `frontend-team-lead.md` | Frontend Team Lead |
| `react-component-dev.md` | React Component Dev |
| `nextjs-page-dev.md` | Next.js Page Dev |
| `frontend-checker.md` | Frontend Checker |
| `frontend-tester.md` | Frontend Tester |

### Database Team

| File | Agent |
|---|---|
| `database-team-lead.md` | Database Team Lead |
| `database-schema-dev.md` | Database Schema Dev |
| `database-migration-dev.md` | Database Migration Dev |
| `database-checker.md` | Database Checker |
| `database-tester.md` | Database Tester |

---

## 💡 Design Principles

1. **Zero-ambiguity tasks** — Every dev agent receives a task so well-specified that it makes zero decisions. Implementation strategy, interface contracts, and acceptance criteria are all defined before a dev agent is spawned.

2. **Context-appropriate decisions** — Decisions are made where context exists. The Architect has the most technical context, so the Architect makes technical decisions. The user makes preference decisions. Dev agents make no decisions.

3. **Strict layer boundaries** — No skipping layers (e.g., controllers never call repositories; widgets never call APIs directly). Every violation is caught by the Checker.

4. **One concern per session** — Every dev agent handles exactly one endpoint, one component, one migration, one provider. This keeps context windows clean and outputs reviewable.

5. **Fresh agents on failure** — Failed Checker or Tester reviews result in a fresh dev agent being spawned. Agents that produced failing work are never reused for the retry.

6. **API contract as single source of truth** — In full-stack modes, the OpenAPI spec defined by the Architect is the canonical contract that all consumers (React, Flutter) and producers (FastAPI, Spring Boot) validate against.
