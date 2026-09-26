# Development Document
## Product: CampusLink — Build Plan for Antigravity / AI Dev Agent

---

## 1. Purpose
This document translates the PRD, SRS, Architecture, and UI/UX documents into a concrete, ordered build plan suitable for an AI coding agent (e.g., Antigravity) to execute — covering project setup, folder structure, build order, and per-screen implementation tasks.

---

## 2. Recommended Project Setup

```
campuslink/
├── frontend/
│   ├── src/
│   │   ├── auth/
│   │   ├── student/
│   │   ├── college/
│   │   ├── recruiter/
│   │   ├── shared/components/
│   │   ├── shared/hooks/
│   │   ├── api/            # API client functions
│   │   └── App.tsx
│   └── package.json
├── backend/
│   ├── src/
│   │   ├── routes/
│   │   ├── controllers/
│   │   ├── services/
│   │   │   ├── matching/    # AI matching logic
│   │   │   └── analytics/
│   │   ├── models/
│   │   └── middleware/auth.ts
│   └── package.json
└── docs/                    # the 5 documents live here
```

**Stack (default recommendation):** React + Tailwind (frontend), Node/Express or FastAPI (backend), PostgreSQL (DB). Substitute per team preference — architecture doc is stack-agnostic at the component level.

---

## 3. Build Order (Milestones)

### Milestone 1 — Foundation
1. Set up frontend + backend scaffolding.
2. Set up PostgreSQL schema per SRS §5 (Users, StudentProfile, Company, Job, Application, Assessment, Interview, Offer, SkillGap).
3. Build Auth: Login / Role Selection (Screen 1) with JWT-based role routing.

### Milestone 2 — Student Flow (Screens 2–7)
4. Student Dashboard (Screen 2): profile header, match summary card, recommended jobs (stub AI service with rule-based matcher first).
5. AI Job Matching (Screen 3): ranked job list using matching service.
6. Job Details + Apply (Screen 4): job detail view + Apply Now → creates Application (status `Applied`).
7. Application Tracking (Screen 5): Timeline component driven by `Application.status`.
8. Assessment/Interview (Screen 6): list upcoming Assessment/Interview records for the student.
9. Offer/Placement (Screen 7): Offer view + Accept Offer action (updates status to `OfferAccepted`, fires analytics event).

### Milestone 3 — College Admin Flow (Screens 8–9)
10. College Dashboard (Screen 8): aggregate counts from Users/Company/Application tables.
11. Placement Analytics (Screen 9): department-wise and year-wise aggregation queries + charts.

### Milestone 4 — Recruiter Flow (Screen 10)
12. Recruiter Dashboard: stats (active jobs, applications, AI shortlisted, interviews, selected) + Post New Job form.
13. AI Recommended Candidates: reverse-direction matching (job → ranked students) + candidate profile view.

### Milestone 5 — Polish
14. Apply dark theme + component library (StatCard, JobMatchCard, SkillProgressBar, Timeline, CandidateRow) consistently across all screens.
15. Responsive pass (mobile-first, per UI/UX doc §6).
16. End-to-end test: full lifecycle from Student login → Offer Accepted → reflected in College Analytics.

---

## 4. AI Matching — Implementation Notes (Prototype-Level)
For the prototype, implement matching as a **deterministic scoring function** (no ML model required initially):

```
match_percent = (count(intersection(student_skills, job_required_skills))
                  / count(job_required_skills)) * 100

missing_skills = job_required_skills - student_skills
```

This satisfies FR-3, FR-4, and FR-10.4 without needing a trained model, and can be swapped for a real ML-based ranking service later without changing the API contract (`match_score(skills_a, skills_b) → {percent, missing[]}`).

---

## 5. Status Transition Rules (for backend validation)
Enforce this sequence for `Application.status` (per SRS §4, Data Integrity):

```
Applied → Shortlisted → Assessment → Interview → Selected → OfferAccepted
```
- Only recruiter/college-admin actions can advance a student's application status (students cannot self-advance beyond `Applied`).
- "Accept Offer" (student action) is the one exception where the student transitions `Selected → OfferAccepted`.

---

## 6. Definition of Done (Per Screen)
A screen is "done" when:
- [ ] UI matches the UI/UX spec (layout, card structure, primary action visible)
- [ ] Connected to real API data (no hardcoded mock data in final build)
- [ ] Role-based access enforced (unauthorized roles cannot reach the screen)
- [ ] Responsive on mobile width (~360–414px) per reference mockups
- [ ] Loading and empty states handled (e.g., "No jobs matched yet")

---

## 7. Testing Checklist
- Auth: login/signup for all 3 roles, incorrect credentials rejected.
- Student: apply to a job → application appears in tracking with status `Applied`.
- Recruiter: post a job → job appears in candidate matching for eligible students.
- College Admin: dashboard counts update after a new application / new placement.
- Status flow: attempt an illegal transition (e.g., `Applied → OfferAccepted` directly) is rejected.

---

## 8. Handoff Notes for Antigravity
- Feed in this document set in order: PRD → SRS → Architecture → UI/UX → Development.
- Build Milestone 1 first and confirm auth + schema before generating screen UIs, since every later screen depends on the Application/Job/User data model.
- Reuse the shared component library (Section 3 of the Architecture doc, Section 7 of the UI/UX doc) across all three role portals to keep the visual language consistent.
