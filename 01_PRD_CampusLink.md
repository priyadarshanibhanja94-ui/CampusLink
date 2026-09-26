# Product Requirements Document (PRD)
## Product: CampusLink — AI-Powered Campus Placement & Recruitment Platform

---

## 1. Overview

CampusLink is a three-sided platform that connects **Students**, **Colleges (Placement Cell/Admin)**, and **Recruiters** on a single system to streamline campus placements using AI-driven job matching, candidate shortlisting, and placement analytics.

The goal is to replace fragmented, manual placement processes (spreadsheets, emails, notice boards) with one connected system where:
- Students discover and apply to relevant jobs matched to their skill profile.
- Colleges track placement activity across the institution in real time.
- Recruiters post jobs and receive AI-ranked candidate shortlists.

---

## 2. Problem Statement

Campus placement today is manual and disconnected:
- Students don't know which jobs genuinely fit their skillset until after applying.
- Placement cells track applications and offers manually across spreadsheets.
- Recruiters receive large, unfiltered applicant pools and must manually shortlist.

CampusLink solves this with an AI matching layer sitting between all three parties.

---

## 3. Goals & Objectives

| Goal | Metric |
|---|---|
| Improve match quality between students and jobs | Avg. Profile Match % of applied jobs ≥ 75% |
| Reduce recruiter shortlisting effort | AI Shortlist reduces manual review by ≥ 60% |
| Give colleges real-time placement visibility | Placement Analytics dashboard updated live |
| Increase placement conversion | Applied → Placed conversion rate tracked per drive |

---

## 4. Target Users / Personas

### 4.1 Student (e.g., "Ronit", CSE, 3rd Year)
Wants to find jobs matching his skills, understand skill gaps, track applications, and prepare for assessments/interviews.

### 4.2 College Placement Officer / Admin
Wants a single dashboard to monitor all students, companies, applications, and placement statistics across departments and years.

### 4.3 Recruiter (e.g., ABC Technologies)
Wants to post jobs, receive AI-ranked candidates, and manage the interview-to-offer pipeline with minimal manual filtering.

---

## 5. Scope

### 5.1 In Scope (v1 — 10 Core Screens)

| # | Screen | Role | Priority |
|---|---|---|---|
| 1 | Login / Role Selection | All | ★★★ |
| 2 | Student Dashboard | Student | ★★★★★ |
| 3 | AI Job Matching | Student | ★★★★★ |
| 4 | Job Details + Apply | Student | ★★★★ |
| 5 | Application Tracking | Student | ★★★★★ |
| 6 | Assessment / Interview | Student | ★★★★ |
| 7 | Offer / Placement | Student | ★★★★ |
| 8 | College Dashboard | College Admin | ★★★★★ |
| 9 | Placement Analytics | College Admin | ★★★★★ |
| 10 | Recruiter Dashboard + AI Candidates | Recruiter | ★★★★★ |

### 5.2 Out of Scope (v1)
- Payment/subscription billing
- Native mobile apps (web-responsive only for prototype)
- Multi-language support
- Video interview hosting (only scheduling, not conducting)
- Resume builder tool (assume resume/profile is pre-filled)

---

## 6. Feature Requirements by Role

### 6.1 Student
- Login / signup, select role = Student
- Profile with branch, year, skills
- Home dashboard: greeting, profile match %, recommended jobs, skill-gap bars
- AI Job Matches list: match %, matched skills, missing skills, per job
- Job Details screen: company, location, salary (LPA), required skills, match %, deadline, Apply button
- Application Tracking: visual timeline (Applied → Shortlisted → Assessment → Interview → Selected → Offer Accepted)
- Assessment & Interview screen: upcoming assessment (date/time, Start Assessment), upcoming interview (date/time, View Details)
- Offer/Placement screen: congratulatory message, package, location, View/Accept Offer

### 6.2 College Admin
- Admin dashboard: total students, total companies, total applications, total placed (summary cards)
- Placement overview: applied, interviewed, placed, offers (funnel numbers)
- Navigation actions: View Students, Manage Companies, Placement Analytics
- Placement Analytics screen: total students, total applications, interviewed, placed, offers accepted, department-wise placement breakdown, year-wise placement trend (chart)

### 6.3 Recruiter
- Recruiter login (separate role)
- Recruiter dashboard: company name, active jobs, applications, AI shortlisted count, interviews, selected count
- Post New Job action
- AI Recommended Candidates screen: ranked list of candidates with match %, branch, key skills, View Profile action

---

## 7. AI Matching Logic (Product-Level Description)
- Match % is computed by comparing a student's declared/verified skills against a job's required skill list.
- "Missing Skills" = required skills not present in student profile.
- Same underlying matching engine ranks candidates for recruiters (inverse direction: job → best-fit students).
- Skill Gap bars on the student dashboard show proficiency level per key skill (e.g., JavaScript 70%, React 55%) — likely self-assessed or assessment-derived.

---

## 8. Success Metrics (v1 Prototype)
- All 10 screens functional and navigable end-to-end for all 3 roles.
- Student can go from Login → Job Match → Apply → Track → Interview → Offer without dead ends.
- College Admin can view live-updating counts as students/recruiters interact with the system.
- Recruiter can view AI-ranked candidates sourced from real application data.

---

## 9. Assumptions & Constraints
- This is a **prototype/demo build**, not a production-grade placement system — scope is deliberately limited to 10 screens.
- Skill match percentages can be simulated/rule-based (not necessarily a trained ML model) for the prototype stage.
- Single institution context for demo (not multi-tenant across colleges) unless explicitly extended later.

---

## 10. Open Questions
- Should recruiters see student contact details directly, or only after mutual shortlisting?
- Is the assessment itself hosted in-app (Start Assessment) or an external redirect?
- Does "Accept Offer" trigger any downstream notification to the College Admin dashboard automatically? (Assumed: yes, for real-time analytics.)
