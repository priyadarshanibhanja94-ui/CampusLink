# Software Requirements Specification (SRS)
## Product: CampusLink

---

## 1. Introduction

### 1.1 Purpose
This document specifies the functional and non-functional requirements for CampusLink, an AI-powered campus placement platform covering Student, College Admin, and Recruiter roles.

### 1.2 Intended Audience
Development team (design/build via Antigravity or equivalent AI dev tool), QA, and product stakeholders.

### 1.3 Scope
Covers the 10-screen prototype: Login/Role Selection, Student Dashboard, AI Job Matching, Job Details+Apply, Application Tracking, Assessment/Interview, Offer/Placement, College Dashboard, Placement Analytics, Recruiter Dashboard+AI Candidates.

### 1.4 Definitions
- **Match %**: Computed compatibility score between a student profile and a job's required skills.
- **Skill Gap**: Difference between a student's current skill proficiency and a target/required level.
- **Shortlist**: Subset of applicants AI-ranks as top-fit for a job.

---

## 2. Overall Description

### 2.1 System Perspective
CampusLink is a standalone web application with three role-based portals (Student / College / Recruiter) sharing one backend and one AI matching engine.

### 2.2 User Classes

| Role | Description | Access Level |
|---|---|---|
| Student | Applies to jobs, tracks status | Own profile + own applications |
| College Admin | Institution-level oversight | All students, companies, analytics for their college |
| Recruiter | Company representative | Own job postings + applicants to those jobs |

### 2.3 Operating Environment
- Web application, responsive (mobile-first per provided mockups, dark theme).
- Client: modern browsers (Chrome, Safari, Edge, Firefox — latest 2 versions).
- Server: cloud-hosted backend + database + AI matching service.

---

## 3. Functional Requirements

### FR-1 Authentication & Role Selection
- FR-1.1: System shall allow login/signup with role selection: Student, College Admin, Recruiter.
- FR-1.2: System shall route the user to the correct dashboard based on role after login.
- FR-1.3: System shall support separate recruiter login flow (company-linked account).

### FR-2 Student Dashboard
- FR-2.1: System shall display student name, branch, and year.
- FR-2.2: System shall display an overall Profile Match % summary.
- FR-2.3: System shall display a "Recommended for You" job list (top matches).
- FR-2.4: System shall display a Skill Gap section with per-skill proficiency bars.

### FR-3 AI Job Matching
- FR-3.1: System shall list jobs ranked by descending Match %.
- FR-3.2: Each job entry shall show company, role title, Match %, matched skills (✓), and missing skills.
- FR-3.3: System shall allow navigating from a match entry to full Job Details.

### FR-4 Job Details + Apply
- FR-4.1: System shall display job title, company, location, salary range (LPA), required skills, Match %, and application deadline.
- FR-4.2: System shall provide an "Apply Now" action that creates an Application record linked to student + job.
- FR-4.3: System shall prevent duplicate applications to the same job by the same student.

### FR-5 Application Tracking
- FR-5.1: System shall display a visual timeline with stages: Applied → Shortlisted → Assessment → Interview → Selected → Offer Accepted.
- FR-5.2: System shall visually distinguish completed (✓), current (●), and pending (○) stages.
- FR-5.3: Status changes shall be driven by recruiter/college actions on the backend (e.g., recruiter marks "Shortlisted").

### FR-6 Assessment / Interview
- FR-6.1: System shall list upcoming assessments with job title, company, date, time, and a "Start Assessment" action.
- FR-6.2: System shall list upcoming interviews with company, round type, date, time, and a "View Details" action.

### FR-7 Offer / Placement
- FR-7.1: On selection, system shall display an offer screen with role, company, package (LPA), and location.
- FR-7.2: System shall provide "View Offer" and "Accept Offer" actions.
- FR-7.3: Accepting an offer shall update the student's application status to "Offer Accepted" and increment college-level "Placed" counters.

### FR-8 College Dashboard
- FR-8.1: System shall display institution-wide summary cards: Total Students, Total Companies, Total Applications, Total Placed.
- FR-8.2: System shall display a Placement Overview funnel: Applied, Interviewed, Placed, Offers.
- FR-8.3: System shall provide navigation to View Students, Manage Companies, and Placement Analytics.

### FR-9 Placement Analytics
- FR-9.1: System shall display total students, total applications, interviewed count, placed count, offers accepted.
- FR-9.2: System shall display department-wise placement breakdown (chart/table).
- FR-9.3: System shall display year-wise placement trend (line/bar chart).

### FR-10 Recruiter Dashboard + AI Candidates
- FR-10.1: System shall display recruiter's company name, active job count, total applications, AI-shortlisted count, interview count, selected count.
- FR-10.2: System shall provide a "Post New Job" action to create a job listing (title, required skills, location, salary, deadline).
- FR-10.3: System shall provide a "View Candidates" action leading to an AI Recommended Candidates list.
- FR-10.4: AI Recommended Candidates list shall rank applicants by Match % and show branch and key skills per candidate.
- FR-10.5: System shall provide a "View Profile" action per candidate.

---

## 4. Non-Functional Requirements

| Category | Requirement |
|---|---|
| Performance | Dashboard and match list screens shall load within 2 seconds under normal load. |
| Scalability | System shall support at least 5,000 concurrent students and 500 recruiters (prototype target: much lower, but design should not block scaling). |
| Availability | Target 99% uptime for the hosted prototype. |
| Security | Role-based access control (RBAC) must prevent students from viewing other students' applications; recruiters must only see applicants to their own postings. |
| Usability | UI shall follow the dark-themed, card-based visual style shown in the reference mockups; mobile-responsive by default. |
| Data Integrity | Application status transitions must follow the defined sequence (no skipping from Applied directly to Offer Accepted without intermediate states, except where explicitly overridden by an admin). |
| Auditability | Status changes (shortlist, interview scheduling, offer) should be timestamped and attributable to the acting role. |

---

## 5. Data Requirements (Entities)

- **User** (id, name, role[student/college/recruiter], email, password_hash)
- **StudentProfile** (user_id, branch, year, skills[], resume_url)
- **Company** (id, name, industry, recruiter_user_id)
- **Job** (id, company_id, title, required_skills[], location, salary_range, deadline, status)
- **Application** (id, student_id, job_id, match_percent, status[Applied/Shortlisted/Assessment/Interview/Selected/OfferAccepted], created_at, updated_at)
- **Assessment** (id, application_id, scheduled_at, type, status)
- **Interview** (id, application_id, scheduled_at, round_type, status)
- **Offer** (id, application_id, package, location, status[Pending/Accepted])
- **SkillGap** (student_id, skill_name, proficiency_percent)

---

## 6. External Interface Requirements
- REST/GraphQL API between frontend and backend (see Architecture Document).
- AI Matching Service exposed as an internal API: `match_score(student_skills, job_requirements) → percent, missing_skills[]`.
- No third-party payment or video-conferencing integration required for v1.

---

## 7. Constraints
- Prototype-grade build: 10 screens only, no admin CRUD-heavy back-office beyond what's specified.
- Dark theme as default per reference design.

---

## 8. Acceptance Criteria (Summary)
A build is considered complete when:
1. All three roles can log in and reach their respective dashboards.
2. A student can move through the full lifecycle: match → apply → track → assessment/interview → offer → accept.
3. A recruiter can post a job and view an AI-ranked candidate list sourced from real applications.
4. A college admin sees dashboard numbers that update as students apply/get placed.
