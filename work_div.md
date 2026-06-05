# Work Division & Responsibility Mapping

This document outlines the allocation of modules, responsibilities, database ownership, and shared tasks between **Teammate 1** (Platform & Collaboration) and **Teammate 2** (AI, Mentorship & Discovery).

---

## 🧑‍💻 Teammate 1: Platform & Collaboration Module
*Focuses on the core application flow, user interactions, and workspace collaboration.*

### 📦 Module 1: Authentication & User Management
* **Scope / Features:**
  * Authentication
  * User Profiles
* **Responsibilities:**
  * Implement Signup & Login (Email/Password)
  * Integrate Google OAuth
  * Manage Role assignment (Student vs. Mentor)
  * Develop profile creation & editing interfaces
  * Build skills and interests management systems

### 📦 Module 2: Project Marketplace
* **Scope / Features:**
  * Project Marketplace
  * Project Applications
* **Responsibilities:**
  * Create, Edit, and Delete projects (CRUD operations)
  * Implement project listing, filtering, and search functionality
  * Build application flow (apply to projects, accept/reject applicants)
  * Enable bookmarking and saving projects

### 📦 Module 3: Team Collaboration
* **Scope / Features:**
  * Team Workspace
  * Team Chat
  * Task Management (Kanban Board)
  * Shared Resources
  * Progress Tracking
* **Responsibilities:**
  * Create teams and manage membership
  * Implement real-time team chat
  * Build a collaborative Kanban/task board
  * Handle file/resource uploads for teams
  * Create team progress dashboards

### 📦 Module 4: Notification System
* **Scope / Features:**
  * Team Invitations
  * Application Updates
  * Mentor Responses
  * Task Notifications
* **Responsibilities:**
  * Design backend Notification APIs
  * Implement real-time alerts
  * Build an in-app notification center/bell component

#### 🗄️ Database Ownership
Teammate 1 owns the schema and operations for:

| Table | Description |
| :--- | :--- |
| `Users` | Core user accounts, roles, and profiles |
| `Projects` | Project listings and metadata |
| `Applications` | Project join requests and statuses |
| `Teams` | Team records and configurations |
| `Team Members` | Team membership mappings |
| `Tasks` | Kanban tasks and assignments |
| `Chat Messages` | Real-time team chat history |
| `Notifications` | In-app alerts and updates |

---

## 🤖 Teammate 2: AI, Mentorship & Discovery Module
*Focuses on third-party integrations, smart recommendations, AI features, and external data services.*

### 📦 Module 1: GitHub Integration
* **Scope / Features:**
  * GitHub Integration
* **Responsibilities:**
  * Integrate GitHub OAuth
  * Fetch user repositories and contributions
  * Analyze developer tech stacks
  * Generate GitHub stats/charts

### 📦 Module 2: Mentor Marketplace
* **Scope / Features:**
  * Mentor Marketplace
  * Meeting Scheduler
* **Responsibilities:**
  * Set up mentor profiles and expertise lists
  * Implement mentor search and filters
  * Build availability scheduling and session booking systems

### 📦 Module 3: Hackathon Ecosystem
* **Scope / Features:**
  * Hackathon Finder
* **Responsibilities:**
  * Build and maintain a hackathon database/schema
  * Implement event search and filters
  * Track registration deadlines
  * Set up recommendation engine for hackathons

### 📦 Module 4: Portfolio Builder
* **Scope / Features:**
  * Portfolio Builder
* **Responsibilities:**
  * Build dynamic portfolio generation engines
  * Design achievement badges/showcase systems
  * Display project highlights and public profile pages

### 📦 Module 5: AI Engine
* **Scope / Features:**
  * AI Team Matching
  * AI Co-Founder Score
  * Project Success Prediction
  * Skill Gap Analysis
  * Startup Potential Score
  * AI Resume Generator
* **Responsibilities:**
  * Develop recommendation algorithms
  * Integrate OpenAI / LLM APIs
  * Build skill gap analysis engines
  * Implement resume generation PDF/docx output
  * Define and tune scoring systems
  * Design AI prompts and LLM workflows

#### 🗄️ Database Ownership
Teammate 2 owns the schema and operations for:

| Table | Description |
| :--- | :--- |
| `Mentors` | Mentor profiles and expertise data |
| `Mentor Sessions` | Booked sessions and availability slots |
| `Hackathons` | Event listings, deadlines, and filters |
| `Portfolio Data` | Generated portfolios and achievement records |
| `GitHub Data` | Fetched repos, contributions, and stats |
| `AI Scores` | Co-founder, startup, and success scores |
| `Resume Data` | AI-generated resume outputs |

---

## 🤝 Shared Integration Work
Both teammates will collaborate closely on the following areas:

* **API Integration:**
  * Connecting frontend pages/components to FastAPI backend endpoints
  * End-to-end testing of core user flows
* **UI Development:**
  * Designing and maintaining a unified Tailwind CSS / Shadcn UI design system
  * Standardizing layout wrappers (e.g. Dashboard navigation, Sidebars, Modals)
* **Deployment:**
  * Frontend deployment on Vercel
  * Backend API deployment on Render or AWS
  * PostgreSQL database setup, indexing, and connection pooling

---

## 📊 Workload Distribution Matrix

| Area / Feature | Teammate 1 | Teammate 2 |
| :--- | :---: | :---: |
| **Authentication & OAuth** | ✅ | |
| **User Profiles** | ✅ | |
| **Project CRUD & Marketplace** | ✅ | |
| **Project Applications** | ✅ | |
| **Teams & Workspaces** | ✅ | |
| **Real-time Chat** | ✅ | |
| **Task Management (Kanban)** | ✅ | |
| **Notification System** | ✅ | |
| **GitHub Integration & OAuth** | | ✅ |
| **Mentor System & Profiles** | | ✅ |
| **Meeting Scheduler** | | ✅ |
| **Hackathon Finder** | | ✅ |
| **Portfolio Builder** | | ✅ |
| **AI Team Matching** | | ✅ |
| **AI Co-Founder Score** | | ✅ |
| **Skill Gap Analysis** | | ✅ |
| **Startup Score** | | ✅ |
| **Resume Generator** | | ✅ |
| **Project Success Prediction** | | ✅ |
