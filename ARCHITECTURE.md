# Architecture — Student Problem Marketplace

## Table of Contents

1. [Overview](#1-overview)
2. [System Diagram](#2-system-diagram)
3. [Architecture Decisions](#3-architecture-decisions)
4. [Layer Breakdown](#4-layer-breakdown)
   - 4.1 [Frontend](#41-frontend)
   - 4.2 [Backend Infrastructure (Supabase)](#42-backend-infrastructure-supabase)
   - 4.3 [Core Application Services (FastAPI)](#43-core-application-services-fastapi)
   - 4.4 [AI Service Layer](#44-ai-service-layer)
   - 4.5 [External Integrations](#45-external-integrations)
5. [Module Ownership](#5-module-ownership)
6. [Realtime Architecture](#6-realtime-architecture)
7. [Security](#7-security)
8. [Deployment](#8-deployment)
9. [Scalability Roadmap](#9-scalability-roadmap)

---

## 1. Overview

The Student Problem Marketplace is an AI-powered collaboration platform that connects students, co-founders, and mentors. It supports project discovery, team formation, task management, and AI-driven matchmaking.

**Architecture pattern:** Modular Monolith with an Independent AI Service Layer

**Core principles:**
- Module ownership aligned to team boundaries
- Managed infrastructure to maximise development velocity at MVP stage
- AI logic isolated in a dedicated service for clean separation of concerns
- Realtime-first for collaboration features

**Data flow:**
```
Browser → Next.js Frontend → Supabase (Auth + DB + Realtime) → FastAPI Services → External APIs
```

---

## 2. System Diagram

```
                        ┌───────────────────────┐
                        │       Frontend        │
                        │   Next.js + React TS  │
                        │   Tailwind + shadcn   │
                        └──────────┬────────────┘
                                   │  REST / Realtime subscriptions
                                   ▼
                   ┌───────────────────────────────┐
                   │           Supabase            │
                   │───────────────────────────────│
                   │  Auth (JWT + OAuth)           │
                   │  PostgreSQL (RLS enforced)    │
                   │  Realtime (WebSockets)        │
                   │  Storage (files & media)      │
                   └──────┬─────────────────┬──────┘
                          │                 │
              ┌───────────▼───┐     ┌───────▼────────────┐
              │  Core API     │     │   AI Service API   │
              │  FastAPI      │     │   FastAPI + Python │
              │  (Teammate 1) │     │   (Teammate 2)     │
              └───────────────┘     └────────────────────┘
                      │                       │
        ┌─────────────▼──────┐    ┌──────────-▼──────────┐
        │ Collaboration Layer│    │ Intelligence Layer  │
        │ Projects & Teams   │    │ AI Matching         │
        │ Tasks & Chat       │    │ Resume Generation   │
        │ Notifications      │    │ GitHub Analysis     │
        └────────────────────┘    └──────────┬──────────┘
                                             │
                              ┌──────────────▼─────────────┐
                              │     External Integrations  │
                              │  GitHub API  │  OpenAI API │
                              └────────────────────────────┘
```

---

## 3. Architecture Decisions

### Why Modular Monolith (not Microservices)?

| Factor | Microservices | Modular Monolith (chosen) |
|---|---|---|
| Deployment complexity | High | Low |
| Inter-service communication | Requires service mesh / message bus | In-process function calls |
| Team overhead (2 people) | Unsustainable | Manageable |
| Development speed at MVP | Slow | Fast |
| Future migration path | N/A | Modules can be extracted later |

Microservices are appropriate at scale. For a two-person student startup, the operational cost outweighs any benefit.

### Why Supabase?

- Eliminates manual infrastructure work (auth, DB, storage, realtime) in one managed platform
- PostgreSQL with Row Level Security gives production-grade data access control without a custom auth layer
- Built-in realtime removes the need to operate a separate WebSocket server at MVP stage

### Why Next.js?

- File-system routing scales cleanly as page count grows
- TypeScript + React + Tailwind is a well-supported, widely documented stack
- Vercel deployment is near-zero config

### Why FastAPI + Python for AI?

- Python has the strongest ecosystem for LLM integrations (OpenAI SDK, LangChain, etc.)
- FastAPI provides async-capable, high-performance API endpoints
- Isolating AI logic from the core backend keeps the codebase clean and makes the AI layer independently deployable when needed

---

## 4. Layer Breakdown

### 4.1 Frontend

**Stack:** Next.js · React · TypeScript · Tailwind CSS · shadcn/ui

**Responsibilities:**

| Area | Pages / Components |
|---|---|
| Authentication | Login, Register, OAuth callback |
| Project Marketplace | Browse, search, filter, project detail |
| Team Workspaces | Members, task board, chat panel |
| Mentor Pages | Mentor profiles, session booking |
| Hackathon Finder | Listings, filters, registration links |
| Portfolio / Resume | Public profile, AI-generated resume export |
| Notifications | Toast + notification centre |
| Dashboards | User home, project owner view |

**Key patterns:**
- Server Components for data-heavy pages (reduce client-side fetches)
- Client Components for interactive UI (chat, real-time task board)
- Supabase JS client for auth state and realtime subscriptions

---

### 4.2 Backend Infrastructure (Supabase)

**Services used:**

| Service | Purpose |
|---|---|
| **Auth** | Email/password, Google OAuth, JWT session management |
| **PostgreSQL** | Primary relational database for all entities |
| **Realtime** | WebSocket subscriptions for chat, notifications, task updates |
| **Storage** | Resumes, certificates, profile images, project attachments |

**Database ownership split:**

> See [Module Ownership](#5-module-ownership) for the full table.

**Security model:** Row Level Security (RLS) is enabled on all tables. No row is accessible unless a policy explicitly permits it for the authenticated user.

---

### 4.3 Core Application Services (FastAPI)

**Owner:** Teammate 1  
**Deployed on:** Render

This service implements the Collaboration Layer — all features that require server-side business logic beyond what Supabase handles directly.

**Modules:**

| Module | Key Responsibilities |
|---|---|
| `auth` | Token validation helpers, role checks |
| `users` | Profile CRUD, skill tags, onboarding flow |
| `projects` | Create/update/delete projects, visibility rules, search |
| `applications` | Apply to projects, accept/reject, status transitions |
| `teams` | Team formation, member roles, invite system |
| `tasks` | Task CRUD, assignment, status workflow |
| `chat` | Message persistence, thread management |
| `notifications` | Event-driven notification fanout |

**API conventions:**
- RESTful endpoints, versioned under `/api/v1/`
- JWT from Supabase Auth passed as `Authorization: Bearer <token>` on every request
- Pydantic models for request/response validation
- Async handlers throughout for non-blocking I/O

---

### 4.4 AI Service Layer

**Owner:** Teammate 2  
**Stack:** FastAPI · Python · OpenAI API  
**Deployed on:** Render (separate service)

This service implements the Intelligence Layer — all AI-driven features are isolated here.

**Features:**

| Feature | Description |
|---|---|
| **AI Team Matching** | Scores candidate profiles against project requirements using embeddings + GPT analysis |
| **Co-founder Scoring** | Rates compatibility between two user profiles across skills, goals, and working style |
| **Startup Potential Analysis** | Evaluates project viability based on problem statement, team, and market signals |
| **Skill Gap Analysis** | Identifies missing skills on a team relative to project needs |
| **AI Resume Generation** | Generates a tailored resume from user profile, projects, and GitHub data |
| **GitHub Intelligence** | Analyses repositories for language distribution, commit patterns, and code quality signals |
| **Mentor Matching** | Recommends mentors based on user goals and mentor expertise embeddings |

**Integration pattern:**

```
Core API  ──HTTP──▶  AI Service  ──▶  OpenAI API
                          │
                          └──▶  GitHub API
```

The Core API calls the AI Service for scoring/generation tasks. Results are persisted back to the database by the AI Service after computation.

---

### 4.5 External Integrations

**GitHub API**

| Data Point | Used For |
|---|---|
| Repository list | Portfolio display, project showcase |
| Language statistics | Skill inference, developer profile enrichment |
| Commit frequency | Activity scoring in AI matching |
| Contribution graph | Developer credibility signals |

**OpenAI API**

| Feature | Model Usage |
|---|---|
| Team matching & scoring | Embeddings (`text-embedding-3-small`) + GPT-4o reasoning |
| Resume generation | GPT-4o with structured output |
| Startup potential analysis | GPT-4o with chain-of-thought prompting |
| Skill gap analysis | GPT-4o with JSON mode |

---

## 5. Module Ownership

### Teammate 1 — Platform & Collaboration Layer

**Service:** Core API (FastAPI)

| Module | Tables Owned |
|---|---|
| Auth & Users | `users`, `user_skills`, `user_profiles` |
| Project Marketplace | `projects`, `project_tags`, `project_categories` |
| Applications | `applications`, `application_status_log` |
| Teams | `teams`, `team_members`, `team_invites` |
| Tasks | `tasks`, `task_assignments`, `task_comments` |
| Chat | `chat_messages`, `chat_threads` |
| Notifications | `notifications`, `notification_preferences` |

---

### Teammate 2 — AI, Mentorship & Discovery Layer

**Service:** AI Service API (FastAPI)

| Module | Tables Owned |
|---|---|
| GitHub Integration | `github_profiles`, `github_repos`, `github_stats` |
| AI Scores | `ai_match_scores`, `ai_startup_scores`, `skill_gaps` |
| Mentor Marketplace | `mentors`, `mentor_expertise`, `mentor_sessions` |
| Hackathon Finder | `hackathons`, `hackathon_registrations` |
| Portfolio | `portfolios`, `portfolio_items` |
| Resume | `resume_data`, `resume_versions` |

---

## 6. Realtime Architecture

**Technology:** Supabase Realtime (Postgres Change Data Capture → WebSocket broadcast)

**Active subscriptions:**

| Channel | Trigger | Consumers |
|---|---|---|
| `chat:thread:{id}` | INSERT on `chat_messages` | Team workspace chat panel |
| `notifications:{user_id}` | INSERT on `notifications` | Notification centre, toast alerts |
| `tasks:{project_id}` | INSERT / UPDATE on `tasks` | Task board (drag-and-drop sync) |
| `applications:{project_id}` | INSERT / UPDATE on `applications` | Project owner dashboard |
| `mentor_sessions:{user_id}` | UPDATE on `mentor_sessions` | Session status indicators |

**Why not custom WebSockets?**

At MVP stage, Supabase Realtime removes the need to operate a stateful WebSocket server. This is a deliberate trade-off: operational simplicity now, with the option to migrate to a dedicated realtime service (e.g., Soketi, Ably) if Supabase's limits become a constraint at scale.

---

## 7. Security

### Authentication

- All sessions managed by Supabase Auth
- JWTs signed with Supabase's project secret; verified on every FastAPI request
- Google OAuth supported out of the box
- Sessions expire and rotate via Supabase's built-in refresh token mechanism

### Database Access Control

- **Row Level Security (RLS)** enabled on every table
- Users can only read/write rows where a policy condition is satisfied (e.g., `auth.uid() = user_id`)
- Service-role key (used by FastAPI backends) bypasses RLS only for server-side operations where trusted

### File Storage

- Supabase Storage bucket policies restrict access per user
- Resumes and certificates are private by default; only the owning user and authorised team members can read
- Public assets (profile images) served via a separate public bucket

### API Security

- All FastAPI routes require a valid Supabase JWT (except public project listings)
- CORS configured to allow only the production frontend origin
- Rate limiting applied at the Render proxy layer

---

## 8. Deployment

| Layer | Platform | Notes |
|---|---|---|
| Frontend | **Vercel** | Auto-deploy from `main` branch; preview deployments on PRs |
| Core API | **Render** | Web service, auto-deploy from `main`; health check on `/health` |
| AI Service API | **Render** | Separate web service; can scale independently |
| Database + Auth | **Supabase** | Managed; no ops required |
| Storage | **Supabase** | Managed |

**Environment variables** (never committed to source):

```
# Frontend (.env.local)
NEXT_PUBLIC_SUPABASE_URL
NEXT_PUBLIC_SUPABASE_ANON_KEY

# Core API
SUPABASE_URL
SUPABASE_SERVICE_ROLE_KEY
AI_SERVICE_URL

# AI Service
SUPABASE_URL
SUPABASE_SERVICE_ROLE_KEY
OPENAI_API_KEY
GITHUB_TOKEN
```

---

## 9. Scalability Roadmap

The modular structure means individual pieces can be extracted into independent services as load demands it — without a full rewrite.

### Phase 1 — MVP (current)

- Centralised modular backend on Render
- Supabase handles all realtime and storage
- Single OpenAI API key, direct calls from AI Service

### Phase 2 — Growth

| Bottleneck | Solution |
|---|---|
| AI response latency | Add a job queue (e.g., Redis + ARQ) so scoring runs async; return results via Supabase Realtime |
| Chat at scale | Evaluate dedicated realtime service if Supabase concurrent connection limits are hit |
| GitHub rate limits | Cache GitHub responses in Supabase; schedule background refresh |
| Database load | Add Supabase connection pooling (PgBouncer, already available on paid plans) |

### Phase 3 — Scale

If traffic grows significantly:

1. **AI Engine** → independent, auto-scaling service (GPU-backed if needed)
2. **Notification Worker** → dedicated queue consumer
3. **Realtime Chat** → dedicated service (Ably, Pusher, or self-hosted Soketi)
4. **Recommendation Engine** → dedicated service with its own vector store

The modular boundaries drawn at MVP stage map directly to these future service boundaries, making extraction a refactor — not a redesign.

---

*Last updated: June 2025*
