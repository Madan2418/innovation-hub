
Frontend (Next.js)
│
├── AI Matching Dashboard
├── GitHub Analytics UI
├── Mentor Marketplace
├── Mentor Booking System
└── Recommendation Panels

Backend (FastAPI)
│
├── AI Recommendation Engine
├── GitHub Integration Service
├── Mentor Service
├── Matching Algorithms
└── Analytics Engine

Database (PostgreSQL)
│
├── github_profiles
├── mentor_profiles
├── mentorship_sessions
├── recommendations
└── skill_scores

# Student Problem Marketplace — Phase 2 Development Plan

## Phase 2 Goals

### Main Objective
Enhance the MVP by introducing:
- GitHub Integration
- AI Team Matching
- Mentor Marketplace

### Expected Outcome
Create an intelligent collaboration ecosystem that helps students:
- Find suitable teammates
- Build credibility using GitHub
- Connect with mentors
- Improve project success rates

---

# 1. GitHub Integration Module

## Objectives
- Connect GitHub accounts
- Analyze coding activity
- Improve AI matching quality

## Features
### GitHub OAuth Login
- Secure GitHub authentication
- Link GitHub account to student profile

### Fetch GitHub Data
Store:
- Public repositories
- Languages used
- Contribution activity
- Followers/following
- Stars earned
- Open-source activity

### GitHub Analytics Dashboard
Display:
- Most used languages
- Contribution heatmap
- Repository statistics
- Coding consistency score
- Open-source score

---

## Backend APIs

python POST /github/connect GET  /github/profile/{username} GET  /github/repos/{username} GET  /github/stats/{username} 

---

## Database Table: github_profiles

| Column | Type |
|---|---|
| id | UUID |
| user_id | UUID |
| github_username | VARCHAR |
| followers | INT |
| following | INT |
| public_repos | INT |
| total_stars | INT |
| top_languages | JSON |
| contribution_score | FLOAT |

---

## GitHub Scoring Formula

python github_score = (     commits * 0.4 +     stars * 0.2 +     repos * 0.2 +     consistency * 0.2 ) 

---

# 2. AI Team Matching System

## Objective
Automatically recommend the best teammates for projects.

---

## Matching Parameters

### Skill Match
Compare:
- Required project skills
- User skills

### Interest Match
Compare domains such as:
- AI/ML
- Web Development
- Blockchain
- Cybersecurity
- Data Science

### Experience Match
Levels:
- Beginner
- Intermediate
- Advanced

### GitHub Activity Match
Analyze:
- Tech stack similarity
- Relevant repositories
- Coding activity

### Collaboration Score
Evaluate:
- Completed projects
- Leadership history
- Team participation
- Reviews/ratings

---

## AI Matching Workflow

text Project Created       ↓ Extract Required Skills       ↓ Analyze Candidate Profiles       ↓ Calculate Compatibility Score       ↓ Rank Candidates       ↓ Recommend Top Matches 

---

## Compatibility Formula

python compatibility_score = (     skill_match * 0.4 +     github_score * 0.2 +     interest_match * 0.15 +     experience_match * 0.15 +     collaboration_score * 0.1 ) 

---

## AI Services

### Use:
- OpenAI API
- LangChain
- Embeddings
- Vector Search

### AI Tasks
- Skill extraction
- Domain classification
- User similarity analysis
- Recommendation summaries

---

## Example Recommendation Response

json {   "recommended_user": "Rahul",   "score": 92,   "reason": [     "Strong React skills",     "Active GitHub contributor",     "Interested in AI projects"   ] } 

---

## Backend APIs

python POST /ai/match-team GET  /ai/recommendations/{project_id} GET  /ai/similar-users/{user_id} 

---

## Database Table: recommendations

| Column | Type |
|---|---|
| id | UUID |
| project_id | UUID |
| recommended_user_id | UUID |
| compatibility_score | FLOAT |
| recommendation_reason | JSON |

---

# 3. Mentor Marketplace

## Objective
Enable students to connect with mentors for guidance.

---

## Mentor Features

### Mentor Profiles
Mentors can add:
- Expertise
- Industry experience
- Availability
- LinkedIn profile
- Session types

### Mentor Discovery
Students can filter by:
- Domain
- Experience
- Availability
- Ratings

### Mentorship Requests
Students can:
- Request mentorship
- Schedule sessions
- Request project reviews

### Session Types
- 1:1 Guidance
- Resume Review
- Architecture Review
- Hackathon Preparation
- Startup Mentoring

---

## Mentor Dashboard

Mentors can:
- Accept/reject requests
- Manage sessions
- View student projects
- Provide feedback

---

## Backend APIs

python POST /mentor/create-profile GET  /mentor/list POST /mentor/request GET  /mentor/sessions 

---

## Database Table: mentor_profiles

| Column | Type |
|---|---|
| id | UUID |
| user_id | UUID |
| expertise | JSON |
| years_experience | INT |
| availability | JSON |
| rating | FLOAT |

---

## Database Table: mentorship_sessions

| Column | Type |
|---|---|
| id | UUID |
| mentor_id | UUID |
| student_id | UUID |
| project_id | UUID |
| session_time | TIMESTAMP |
| status | VARCHAR |

---

# Frontend Development

## New Pages

text /dashboard/ai-match /dashboard/github /dashboard/mentors /dashboard/mentor/[id] /dashboard/recommendations 

---

## Required UI Components

### AI Components
- Match score cards
- Compatibility charts
- Recommendation cards

### GitHub Components
- Contribution heatmap
- Language charts
- Repository cards

### Mentor Components
- Mentor cards
- Booking modal
- Session calendar

---

## Recommended Frontend Stack
- Next.js
- TypeScript
- Tailwind CSS
- Shadcn UI
- Framer Motion
- React Query
- Zustand

---



# Database Additions

## New Tables

text github_profiles recommendations mentor_profiles mentorship_sessions user_skill_scores project_skill_requirements 

---

# AI Infrastructure

## Recommended Services

| Purpose | Tool |
|---|---|
| AI Chat | OpenAI API |
| Workflow | LangChain |
| Embeddings | OpenAI Embeddings |
| Vector Database | pgvector / Pinecone |
| Recommendation Engine | Custom Python Logic |

---

# AI Recommendation Pipeline

text User Profile    ↓ Generate Skill Embeddings    ↓ Store in Vector DB    ↓ Compare With Project Embeddings    ↓ Return Similar Users 

---

# Security & Performance

## GitHub API Protection
- OAuth token management
- API caching
- Rate limit handling

## AI Protection
- Prompt validation
- Abuse prevention
- Request throttling

---

# Phase 2 Development Timeline

## Week 1 — Backend Setup
- GitHub OAuth
- GitHub APIs
- Mentor schema
- Recommendation schema

## Week 2 — GitHub Analytics
- Repository parsing
- Contribution tracking
- Language analytics

## Week 3 — AI Matching Engine
- Skill extraction
- Compatibility scoring
- Recommendation APIs

## Week 4 — Mentor Marketplace
- Mentor profiles
- Booking system
- Session management

## Week 5 — Frontend Integration
- AI dashboard
- GitHub profile UI
- Mentor pages

## Week 6 — Testing & Deployment
- Performance optimization
- AI tuning
- Bug fixes
- Deployment

---

# Priority Order

## High Priority
1. GitHub Integration
2. AI Team Matching
3. Mentor Profiles

## Medium Priority
4. Recommendation explanations
5. Mentor booking
6. Skill analytics

## Low Priority
7. AI insights dashboard
8. Advanced ranking algorithms

---

# Final Deliverables

By the end of Phase 2, the platform should support:

✅ GitHub-integrated student profiles  
✅ AI-powered teammate recommendations  
✅ Mentor discovery and booking  
✅ Recommendation engine  
✅ Skill analytics  
✅ Intelligent collaboration ecosystem

---

# Future Enhancements After Phase 2
- AI Project Success Prediction
- Resume Builder
- Hackathon Auto-Team Formation
- Recruiter Dashboard
- Startup Potential Analysis
- Funding Recommendations

database - supabase
framework - react
Frontend

* React￼
* TypeScript
* Tailwind CSS
* shadcn/ui

Backend

* Python +  FastAPI￼

