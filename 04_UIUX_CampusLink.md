# UI/UX Design Document
## Product: CampusLink

---

## 1. Design Principles
- **Dark theme, card-based UI** — matches the reference mockups (black background, white/gold accent text, rounded cards).
- **Role clarity** — each role's dashboard should feel like a distinct "home base" even though components are shared.
- **Progress visibility** — students should always be able to see "where am I in the process" (match %, skill gap %, application timeline).
- **Minimal friction** — one primary action per screen (Apply Now, Start Assessment, Accept Offer, Post New Job).

---

## 2. Visual Style Guide

| Element | Style |
|---|---|
| Background | Black / near-black (#0A0A0A) |
| Primary text | White / off-white |
| Accent 1 | Gold/yellow (star ratings, highlights) — matches reference screenshot |
| Accent 2 | Green for positive states (✓ matched skills, Selected, Placed) |
| Accent 3 | Orange/Red for warnings or missing skills |
| Cards | Rounded corners (12–16px), subtle border or elevation, dark gray fill (#1A1A1A) |
| Typography | Sans-serif (Inter / System UI), bold headers, regular body |
| Progress bars | Horizontal filled bars for Skill Gap (e.g., ███████░ 70%) |
| Buttons | Full-width primary buttons on mobile, rounded, high-contrast fill |

---

## 3. Navigation Structure

### 3.1 Student (bottom nav)
`🏠 Home` · `💼 Jobs` · `📊 Status`

### 3.2 College Admin
Dashboard → [View Students] [Manage Companies] [Placement Analytics] (action-card navigation, no bottom nav needed on desktop-first admin view)

### 3.3 Recruiter
Dashboard → [Post New Job] [View Candidates]

---

## 4. Screen-by-Screen Specification

### Screen 1 — Login / Role Selection
- Purpose: Entry point; user selects Student / College Admin / Recruiter, then logs in or signs up.
- Elements: Logo/App name, role toggle or 3 role cards, email/password fields, Login button, Sign Up link.

### Screen 2 — Student Dashboard
- Header: "👋 Hello, {Name}" + "{Branch} • {Year}"
- Profile Match card: "🎯 Profile Match: {X}%"
- Recommended for You: horizontally/vertically scrollable job cards (Title, Company, Match %, View Job button)
- Skill Gap section: per-skill labeled progress bars (e.g., JavaScript 70%, React 55%)
- Bottom nav: Home / Jobs / Status

### Screen 3 — AI Job Matching
- Header: "Your AI Matches"
- List of match cards, each with:
  - Match % (large, bold, top-left)
  - Job title + company
  - Matched skills (✓ green)
  - Missing Skills (list)
  - "View Opportunity" button
- Sorted descending by match %.

### Screen 4 — Job Details + Apply
- Job title, company name
- 📍 Location, 💰 Salary (LPA)
- Required Skills (checklist)
- Your Match: {X}% (prominent)
- Application Deadline
- Primary button: "APPLY NOW" (full-width, high-contrast)

### Screen 5 — Application Tracking
- Title: "Application Status"
- Vertical timeline component with 6 stages:
  `Applied → Shortlisted → Assessment → Interview → Selected → Offer Accepted`
- Visual states: ✓ (completed, green), ● (current, gold/highlighted), ○ (pending, gray)

### Screen 6 — Assessment / Interview
- Section: "Upcoming"
- Assessment card: title, company, 📅 date, ⏰ time, "Start Assessment" button
- Divider
- Interview card: company, round type, 📅 date, ⏰ time, "View Details" button

### Screen 7 — Offer / Placement
- Celebration header: "🎉 Congratulations!"
- Sub-text: "You have received an offer!"
- Role + Company
- Package (LPA), Location
- Buttons: "View Offer" (secondary), "Accept Offer" (primary)

### Screen 8 — College Dashboard
- Header: "CAMPUSLINK — Admin Dashboard"
- Summary stat cards (grid of 4): Students, Companies, Applications, Placed
- Placement Overview list: Applied / Interviewed / Placed / Offers (numbers)
- Action buttons: View Students / Manage Companies / Placement Analytics

### Screen 9 — Placement Analytics
- Stat rows: Total Students, Total Applications, Interviewed, Placed, Offers Accepted
- Department-wise placement — bar chart or table
- Year-wise placement trend — line chart

### Screen 10 — Recruiter Dashboard + AI Candidates
**10a. Recruiter Dashboard**
- Header: Company name
- Stat cards: Active Jobs, Applications, AI Shortlisted, Interviews, Selected
- Buttons: "Post New Job", "View Candidates"

**10b. AI Recommended Candidates**
- Title: "AI Recommended Candidates"
- Ranked list: rank #, name, Match %, branch + top skills
- "View Profile" button per candidate

---

## 5. User Flow Diagrams (Textual)

### 5.1 Student Flow
```
Login → Student Dashboard → AI Job Matches → Job Details → Apply
   → Application Tracking → Assessment/Interview → Offer → Accept
```

### 5.2 College Admin Flow
```
Login → College Dashboard → Placement Analytics
                         → View Students
                         → Manage Companies
```

### 5.3 Recruiter Flow
```
Recruiter Login → Recruiter Dashboard → Post New Job
                                      → View Candidates (AI Ranked) → Candidate Profile
```

### 5.4 System Map
```
                    CAMPUSLINK
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼               ▼
       STUDENT        COLLEGE        RECRUITER
          │              │               │
          ▼              ▼               ▼
      Dashboard      Dashboard        Dashboard
          │              │               │
    ┌─────┼─────┐        ▼           ┌───┴────┐
    ▼     ▼     ▼    Analytics       ▼        ▼
  AI    Jobs  Status                Jobs   Candidates
  Match         │
    │           ▼
    ▼       Assessment
    │           ▼
    └──────→ Interview
                ▼
              Offer
                ▼
              Placed
```

---

## 6. Accessibility & Responsiveness Notes
- All primary actions (Apply, Accept Offer, Post Job) must be reachable via single tap on mobile.
- Match percentages and skill-gap bars need sufficient color contrast against dark background (do not rely on color alone — pair with numeric %).
- Timeline component (Screen 5) should remain legible at narrow (360px) widths — vertical stacking preferred over horizontal.

---

## 7. Component Library (Reusable)
- `StatCard` (used in College Dashboard, Recruiter Dashboard)
- `JobMatchCard` (used in Student Dashboard "Recommended", AI Job Matching)
- `SkillProgressBar` (Student Dashboard skill gap)
- `Timeline` (Application Tracking)
- `CandidateRow` (Recruiter AI Candidates)
- `PrimaryButton` / `SecondaryButton`
