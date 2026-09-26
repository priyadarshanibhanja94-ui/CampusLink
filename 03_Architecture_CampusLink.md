# System Architecture Document
## Product: CampusLink

---

## 1. Architecture Overview

CampusLink follows a layered, service-oriented web architecture:

```
┌─────────────────────────────────────────────────────────┐
│                     CLIENT LAYER                          │
│   Student Web App | College Admin Web App | Recruiter Web │
│         (Responsive SPA — shared component library)       │
└───────────────────────┬─────────────────────────────────┘
                         │ REST/GraphQL over HTTPS
┌───────────────────────▼─────────────────────────────────┐
│                    API GATEWAY / BFF                      │
│      Auth middleware · Role-based routing · Rate limit     │
└───────────────────────┬─────────────────────────────────┘
                         │
      ┌──────────────────┼───────────────────┐
      ▼                  ▼                    ▼
┌───────────┐    ┌───────────────┐    ┌──────────────────┐
│  Core API  │    │  AI Matching   │    │  Analytics Service │
│  Service   │    │  Service       │    │  (Aggregations)     │
└─────┬─────┘    └───────┬───────┘    └─────────┬──────────┘
      │                  │                       │
      └──────────────────┼───────────────────────┘
                         ▼
                ┌──────────────────┐
                │   Database Layer   │
                │  (Users, Jobs,      │
                │  Applications, etc.)│
                └──────────────────┘
```

---

## 2. Recommended Tech Stack

| Layer | Recommendation | Notes |
|---|---|---|
| Frontend | React (or Next.js) + Tailwind CSS | Matches card/dark-theme mockups well; component reuse across 3 role portals |
| State Management | React Query / Zustand | For server-state (jobs, applications) vs. UI state |
| Backend API | Node.js (Express/NestJS) or Python (FastAPI) | Either fits; FastAPI preferred if AI matching logic is Python-native |
| AI Matching Service | Python microservice (scikit-learn / simple rule-based scoring for prototype) | Can start rule-based (skill-set overlap %) and evolve to ML later |
| Database | PostgreSQL | Relational data (users, jobs, applications, statuses) fits well |
| Auth | JWT-based auth, role claim in token | Roles: student / college_admin / recruiter |
| Hosting | Vercel/Netlify (frontend) + Render/Railway/AWS (backend) | Fits a prototype-speed build |
| Charts (Analytics) | Recharts / Chart.js | For dept-wise & year-wise placement trends |

---

## 3. Component Breakdown

### 3.1 Frontend Modules
- `auth/` — Login, Role Selection
- `student/` — Dashboard, JobMatches, JobDetails, ApplicationTracking, AssessmentInterview, Offer
- `college/` — AdminDashboard, PlacementAnalytics, StudentList, CompanyList
- `recruiter/` — RecruiterDashboard, PostJob, CandidateList, CandidateProfile
- `shared/` — Navbar, Card, ProgressBar (skill gap), Timeline (application tracking), StarRating (if reused)

### 3.2 Backend Services

**Core API Service**
- Auth & user management
- Job CRUD (recruiter-facing)
- Application lifecycle management (create, status transitions)
- Student profile management

**AI Matching Service**
- Input: student skill set + job required skills
- Output: match percentage + missing skills list
- Used bidirectionally:
  - Student → Job Matching (ranks jobs for a student)
  - Job → Candidate Matching (ranks students for a recruiter's job)

**Analytics Service**
- Aggregates Application/Offer data into:
  - College dashboard summary counts
  - Placement funnel (Applied/Interviewed/Placed/Offers)
  - Department-wise and year-wise placement trends

---

## 4. Data Flow Examples

### 4.1 Student Applies to a Job
1. Student views Job Details (Core API fetches job + match % from AI Matching Service).
2. Student clicks "Apply Now" → Core API creates an `Application` record with status `Applied`.
3. Application Tracking screen reads this record and renders the timeline stage.

### 4.2 Recruiter Views AI Candidates
1. Recruiter opens "View Candidates" for a Job.
2. Core API fetches all Applications for that job.
3. AI Matching Service scores/ranks each applicant against the job's required skills.
4. Ranked list returned to Recruiter Dashboard UI with match %, key skills.

### 4.3 College Analytics Update
1. Any Application status change (e.g., Offer Accepted) triggers an event.
2. Analytics Service recalculates aggregate counters (Placed, Offers Accepted, dept/year breakdowns).
3. College Dashboard and Placement Analytics screens reflect updated numbers on next fetch (polling or real-time via websockets if desired).

---

## 5. API Design (Representative Endpoints)

```
POST   /auth/login
POST   /auth/signup

GET    /student/dashboard
GET    /student/jobs/matches
GET    /student/jobs/:jobId
POST   /student/jobs/:jobId/apply
GET    /student/applications
GET    /student/applications/:id/timeline

GET    /college/dashboard
GET    /college/analytics
GET    /college/students
GET    /college/companies

POST   /recruiter/jobs
GET    /recruiter/dashboard
GET    /recruiter/jobs/:jobId/candidates
GET    /recruiter/candidates/:id

POST   /internal/match/student-to-jobs
POST   /internal/match/job-to-candidates
```

---

## 6. Security & Access Control
- JWT auth with `role` claim (`student` / `college_admin` / `recruiter`).
- Middleware enforces: students can only access their own applications; recruiters can only access applicants to their own job postings; college admins have read access across their institution's data only.
- Passwords hashed (bcrypt/argon2); HTTPS enforced end-to-end.

---

## 7. Scalability & Future Extensions
- AI Matching Service is isolated as its own service so the scoring algorithm (rule-based → ML model) can evolve without touching the Core API.
- Multi-college (multi-tenant) support can be added later by scoping all queries with a `college_id`.
- Real-time status updates (websockets) can replace polling once the prototype validates the flow.

---

## 8. Deployment View (Prototype)
- Single environment (staging=prod for demo purposes).
- CI/CD: push to main → auto-deploy frontend (Vercel) and backend (Render/Railway).
- Environment variables for DB connection string, JWT secret, AI service URL.
