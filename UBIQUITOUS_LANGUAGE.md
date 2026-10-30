# UBIQUITOUS LANGUAGE — LinkedOut

> **Project:** LinkedOut — AI-Powered Career Platform for Students & Recruiters  
> **Version:** 1.0.0  
> **Last Updated:** October 2026  
> **Tech Stack:** React (Vite) · Node.js · Express · MongoDB · JWT Auth

---

## Table of Contents

1. [Overview](#overview)
2. [Core Actors / Roles](#1-core-actors--roles)
3. [Career Twin](#2-career-twin)
4. [Job Listings](#3-job-listings)
5. [Applications & Application Lifecycle](#4-applications--application-lifecycle)
6. [Skill Assessments](#5-skill-assessments)
7. [Authentication & Sessions](#6-authentication--sessions)
8. [Platform-Wide Concepts](#7-platform-wide-concepts)
9. [System / Technical Terms](#8-system--technical-terms)

---

## Overview

**LinkedOut** is a full-stack career networking and job-matching platform designed for students and fresh graduates. It enables students to build rich professional profiles (called **Career Twins**), discover and apply to jobs, verify their skills through timed assessments, and track their entire application journey in one place. Recruiters can post job listings, define eligibility criteria, and review student profiles.

The terms defined in this document are the **single source of truth** for naming conventions across the codebase, UI labels, API endpoints, database schemas, and team discussions. Any team member — developer, designer, or product owner — should use these terms consistently.

---

## 1. Core Actors / Roles

### User
A registered account on the platform. Every user belongs to exactly one **Role**.

| Role | Description |
|---|---|
| `student` | A job-seeking student or fresh graduate. Can build a Career Twin, apply to jobs, and take skill assessments. |
| `recruiter` | A hiring professional or company representative. Can post Job Listings and review Applications. |
| `admin` | A platform administrator with elevated permissions to manage all data. |

### Student
A **User** with the role `student`. The primary beneficiary of the platform. A Student's identity on the platform is represented by their **Career Twin**.

### Recruiter
A **User** with the role `recruiter`. Posts **Job Listings** and evaluates **Applicants**. A recruiter is identified by their company affiliation (e.g., `Priya Mehta — Talent Lead @ TechCorp`).

### Applicant
A **Student** who has submitted an **Application** for a specific **Job Listing**. An Applicant transitions through the **Application Lifecycle**.

---

## 2. Career Twin

### Career Twin
The central professional profile of a **Student** on LinkedOut. It is called a "twin" because it is meant to be a complete, living digital mirror of the student's real-world career identity. One Career Twin exists per Student and is automatically initialized upon registration.

A Career Twin is composed of the following sub-sections:

---

### Personal Info
The identity block of a Career Twin. Contains:

| Field | Description |
|---|---|
| `fullName` | Student's full legal name. |
| `headline` | A short one-liner describing the student's professional identity (e.g., *"Full-Stack Developer & Aspiring Cloud Architect"*). |
| `bio` | A paragraph-length professional summary. |
| `email` | Contact email displayed on the profile. |
| `phone` | Contact phone number. |
| `location` | City/Country the student is based in or targeting. |
| `linkedIn` | URL to the student's LinkedIn profile. |
| `github` | URL to the student's GitHub profile. |
| `portfolio` | URL to the student's personal portfolio website. |

---

### Education
An ordered list of academic qualifications. Each **Education Entry** contains:

| Field | Description |
|---|---|
| `institution` | Name of the school, college, or university. |
| `degree` | Degree type (e.g., B.Tech, M.Sc, Senior Secondary). |
| `fieldOfStudy` | Major / specialization (e.g., *Computer Science & Engineering*). |
| `startYear` / `endYear` | Year range of attendance. |
| `grade` | CGPA or percentage score. |
| `activities` | Co-curricular involvement (clubs, olympiads, hackathons). |

---

### Skill
A technical or soft competency listed on the Career Twin. Skills can be **Verified** or **Unverified**.

| Field | Allowed Values |
|---|---|
| `name` | Free text (e.g., *JavaScript*, *Docker*). |
| `category` | `Languages` · `Frameworks` · `Databases` · `Tools` · `Soft Skills` · `Other` |
| `proficiency` | `Beginner` · `Intermediate` · `Advanced` · `Expert` |
| `verified` | `true` if the student passed an **Assessment** for this skill. |
| `verifiedScore` | Percentage score achieved in the corresponding Assessment (0–100). |

### Verified Skill
A **Skill** whose `verified` flag is `true`, awarded automatically when a Student passes a **Skill Assessment** for that skill tag. Verified Skills display a badge on the Career Twin.

### Skill Tag
A normalized short identifier that links a **Skill** to an **Assessment** (e.g., `JavaScript`, `React`, `Node.js`). Used as a join key between the `Skill` and `Assessment` documents.

---

### Project
A portfolio showcase item on the Career Twin representing a software project built by the Student.

| Field | Description |
|---|---|
| `title` | Project name. |
| `description` | What the project does. |
| `technologies` | Array of tech stack items used. |
| `liveUrl` | Deployed demo URL. |
| `githubUrl` | Source code repository URL. |
| `highlights` | Bullet-point key achievements/metrics. |
| `startDate` / `endDate` | Duration of the project. |

---

### Experience
A professional work history entry on the Career Twin.

| Field | Allowed Values / Description |
|---|---|
| `role` | Job title / designation held. |
| `company` | Employer name. |
| `location` | Work location. |
| `type` | `Full-time` · `Part-time` · `Internship` · `Freelance` |
| `startDate` / `endDate` | Tenure dates. |
| `isCurrent` | `true` if this is the student's current position. |
| `responsibilities` | Array of bullet-point role responsibilities. |

---

### Certification
A professional credential or course completion certificate added to the Career Twin.

| Field | Description |
|---|---|
| `name` | Certificate title. |
| `issuer` | Issuing organization (e.g., *Amazon Web Services*, *Meta / Coursera*). |
| `issueDate` | Year/date issued. |
| `credentialUrl` | Verification URL. |

---

### Preferences
A Student's job-seeking preferences used for smart matching.

| Field | Allowed Values / Description |
|---|---|
| `targetRoles` | Job titles the Student is actively targeting. |
| `preferredLocations` | Cities or regions preferred for work. |
| `jobTypes` | `Remote` · `Hybrid` · `On-site` |
| `expectedSalary` | Salary range expectation (e.g., *Rs. 12,00,000 – Rs. 18,00,000 / yr*). |
| `noticePeriod` | Availability timeline (e.g., *Immediate*, *1 Month*). |

---

### Profile Completion Score
A numeric percentage (0–100) auto-calculated for each Career Twin that reflects how completely the student has filled their profile. Sections contributing to the score:

| Section | Points |
|---|---|
| Personal Info (fullName + headline + bio) | +20 |
| At least one Education entry | +20 |
| At least 3 Skills | +20 |
| At least 1 Project | +20 |
| At least 1 Experience | +10 |
| At least 1 Target Role in Preferences | +10 |
| **Maximum** | **100** |

---

## 3. Job Listings

### Job Listing (Job)
A position posted by a **Recruiter** on the platform that **Students** can discover and apply to.

| Field | Description |
|---|---|
| `title` | Job position name (e.g., *Frontend Engineer (React / TypeScript)*). |
| `company` | Hiring company name. |
| `logo` | Company logo emoji or URL. |
| `location` | Work location (city or *Remote*). |
| `type` | Work arrangement: `Remote` · `Hybrid` · `On-site` |
| `employmentType` | Nature of engagement: `Full-time` · `Internship` · `Contract` · `Part-time` |
| `salaryRange` | Compensation range (e.g., *Rs. 10,00,000 – Rs. 15,00,000 / yr*). |
| `experienceLevel` | Seniority: `Fresher / Entry` · `1-3 Years` · `3-5 Years` · `Senior (5+ Years)` |
| `requiredSkills` | Skills that are mandatory for the role. |
| `preferredSkills` | Nice-to-have skills for the role. |
| `description` | Full narrative description of the role. |
| `responsibilities` | Bullet-point list of expected duties. |
| `deadline` | Last date to submit applications. |
| `isActive` | `true` if the listing is live and accepting applications. |
| `applicantsCount` | Running count of students who have applied. |
| `postedBy` | Reference to the **User** (recruiter) who created the listing. |

### Eligibility Criteria
Minimum qualifications defined by the Recruiter that a Student must meet to apply.

| Field | Description |
|---|---|
| `minCgpa` | Minimum CGPA required (e.g., `7.0`). |
| `allowedBranches` | Academic branches eligible to apply (e.g., *CSE*, *IT*, *ECE*, *Any Graduate*). |
| `graduationYears` | Batch years eligible to apply (e.g., *2025*, *2026*, *2027*). |

### Required Skills
Skills that a **Student** *must* have to be considered for a **Job Listing**. Used to compute the **Match Score**.

### Preferred Skills
Skills that are *nice-to-have* for a **Job Listing** but are not mandatory. Increase the **Match Score** if present.

### Match Score
A numeric score (0–100) computed by comparing a **Student's** Career Twin skills against a **Job Listing's** required and preferred skills. A higher score indicates a stronger profile-to-job alignment. Displayed on the Application.

---

## 4. Applications & Application Lifecycle

### Application
A formal submission by a **Student** (Applicant) expressing intent to be considered for a **Job Listing**. One student can have at most one Application per Job (enforced by a unique index on `userId + jobId`).

| Field | Description |
|---|---|
| `userId` | Reference to the applying Student's User account. |
| `jobId` | Reference to the Job Listing being applied to. |
| `status` | Current stage in the Application Lifecycle. |
| `matchScore` | Profile-to-job alignment score at the time of application. |
| `notes` | Recruiter or system notes attached to the application. |
| `interviewDate` | Scheduled interview date, if applicable. |
| `appliedDate` | Timestamp when the application was submitted. |
| `timeline` | Ordered log of status transitions with dates and notes. |

### Application Lifecycle
The sequential stages an **Application** moves through, from submission to resolution:

```
Applied --> Under Review --> Shortlisted --> Interview Scheduled --> Selected
                                                                --> Rejected
```

| Status | Meaning |
|---|---|
| `Applied` | Student submitted the application. Awaiting recruiter review. |
| `Under Review` | Recruiter is actively reviewing the application. |
| `Shortlisted` | Application passed initial screening and candidate is on the shortlist. |
| `Interview Scheduled` | A formal interview has been booked with the candidate. |
| `Selected` | Candidate received an offer / was selected for the role. |
| `Rejected` | Application did not proceed further in the hiring process. |

### Application Timeline
An append-only ordered log attached to each **Application** tracking every status change, its timestamp, and any notes from the recruiter.

---

## 5. Skill Assessments

### Assessment
A timed, multiple-choice skill quiz that a **Student** can take to get a **Skill** on their **Career Twin** verified. Each Assessment is uniquely identified by a **Skill Tag**.

| Field | Description |
|---|---|
| `skillTag` | Unique identifier linking the Assessment to a Skill (e.g., `JavaScript`). |
| `title` | Full display title of the assessment. |
| `category` | Topic domain: `Frontend` · `Backend` · `Full Stack` · `Languages` · `Database` · `Cloud & DevOps` |
| `difficulty` | `Beginner` · `Intermediate` · `Advanced` |
| `description` | Brief description of what the assessment evaluates. |
| `timeLimitMinutes` | Maximum time allowed (in minutes) to complete the quiz. |
| `passingScorePercentage` | Minimum percentage (e.g., `70`) required to pass and earn skill verification. |
| `questions` | Ordered list of **Questions**. |

### Question
An individual multiple-choice item within an **Assessment**.

| Field | Description |
|---|---|
| `questionText` | The prompt/question posed to the Student. |
| `options` | Array of 4 answer choices. |
| `correctIndex` | Zero-based index of the correct answer in the options array. |
| `explanation` | Explanation shown after the student answers (for learning). |

### Assessment Result
The stored outcome of a **Student** completing an **Assessment**.

| Field | Description |
|---|---|
| `userId` | Student who took the assessment. |
| `assessmentId` | Assessment that was taken. |
| `skillTag` | Skill tag the assessment was for. |
| `score` | Number of questions answered correctly. |
| `totalQuestions` | Total number of questions in the assessment. |
| `percentage` | `(score / totalQuestions) * 100`. |
| `passed` | `true` if `percentage >= passingScorePercentage`. |
| `answers` | Per-question breakdown: selected option and correctness. |
| `takenAt` | Timestamp of when the assessment was submitted. |

### Passing Score
The threshold percentage (default `70%`) a Student must achieve in an **Assessment** to pass. On passing, the corresponding **Skill** on the Student's Career Twin becomes **Verified**.

### Skill Verification
The process by which a **Skill** on a Career Twin is marked as `verified: true` and given a `verifiedScore` after a Student successfully passes the corresponding **Assessment**. A verified badge is displayed on the skill.

---

## 6. Authentication & Sessions

### Registration
The process of a new **User** creating an account. Requires `name`, `email`, `password`, and optionally a `role` (defaults to `student`). On successful registration, a **Career Twin** is automatically initialized for Student accounts.

### Login
The process of an existing **User** authenticating with their `email` and `password`. Returns a **JWT Token** and user data on success.

### JWT Token (JSON Web Token)
A signed, stateless authentication token issued to a **User** upon **Login** or **Registration**. Valid for **30 days**. Must be included in the `Authorization: Bearer <token>` header for all protected API requests.

### Protected Route
An API endpoint or frontend page that requires a valid **JWT Token** to access. Unauthenticated requests are rejected with HTTP `401 Unauthorized`.

### Demo Login
A shortcut authentication flow for testing and presentations. Allows instant login as a pre-seeded demo **User** of a given **Role** without needing a real email/password. Seeded demo accounts include:
- `aarav@linkedout.dev` — Demo Student
- `priya@techcorp.io` — Demo Recruiter

### Mock Store
An in-memory data store (`mockStore.js`) used as a graceful fallback when the MongoDB database is unavailable. Pre-seeded with demo Users, Career Twins, Jobs, Applications, and Assessments to ensure zero-downtime development and semester presentations.

---

## 7. Platform-Wide Concepts

### Profile Completeness
A metric (0–100%) shown to the **Student** indicating how fully they have populated their **Career Twin**. Encourages completion of all profile sections to improve job matching and recruiter visibility.

### ATS Resume (Applicant Tracking System Resume)
A resume format generated from the Student's **Career Twin** data, formatted to be parseable by Applicant Tracking Systems used by large companies. Referenced in application notes (e.g., *"Applied with ATS Resume"*).

### Smart Matching / Skill Overlap
The algorithm that compares a Student's **Career Twin** skills to a Job Listing's **Required Skills** and **Preferred Skills** to compute a **Match Score**. A 100% match means the student possesses all required and preferred skills.

### Job Type
The physical work arrangement for a role:
- `Remote` — Fully remote, no office requirement.
- `Hybrid` — Mix of in-office and remote work.
- `On-site` — Full-time on-site presence required.

### Employment Type
The contractual nature of engagement:
- `Full-time` — Permanent employment.
- `Internship` — Fixed-term internship (typically 2–6 months).
- `Contract` — Fixed-term contract engagement.
- `Part-time` — Part-time / reduced-hour employment.

### Experience Level
A seniority tier attached to a **Job Listing**:
- `Fresher / Entry` — 0 years experience; new graduates welcome.
- `1-3 Years` — Early career professionals.
- `3-5 Years` — Mid-level professionals.
- `Senior (5+ Years)` — Senior / leadership-level roles.

### Notice Period
The amount of time a **Student** needs before they can start a new role (e.g., *Immediate*, *1 Month*, *2 Months*). Stored under **Career Twin Preferences**.

### Recruiter Notes
Text annotations added by a **Recruiter** on an **Application** to document decisions, interview feedback, or status change reasons. Stored in the `notes` field and in each **Application Timeline** entry.

---

## 8. System / Technical Terms

### Bounded Contexts
LinkedOut is structured around the following bounded contexts:

| Context | Core Entities |
|---|---|
| **Identity & Auth** | User, JWT Token, Role |
| **Career Profile** | Career Twin, Skill, Experience, Education, Project, Certification, Preferences |
| **Job Market** | Job Listing, Eligibility Criteria, Required Skills, Preferred Skills |
| **Application Tracking** | Application, Application Lifecycle, Application Timeline, Match Score |
| **Skill Verification** | Assessment, Question, Assessment Result, Skill Verification |

### Timestamps
All major documents (`User`, `CareerTwin`, `Job`, `Application`, `Assessment`, `AssessmentResult`) carry automatic `createdAt` and `updatedAt` timestamps managed by Mongoose.

### Unique Constraints
- Each **User** email is globally unique.
- Each **Career Twin** belongs to exactly one User (`userId` is unique).
- Each **Assessment** targets exactly one **Skill Tag** (unique).
- Each **Application** is unique per `(userId, jobId)` pair — a student cannot apply to the same job twice.

### In-Memory Fallback
When MongoDB is unavailable, the system falls back to the **Mock Store** (`mockStore.js`) which holds seeded arrays of Users, Career Twins, Jobs, Applications, Assessments, and Assessment Results in process memory. Ensures the platform remains functional for demonstrations.

### Seed Data / Demo Data
Pre-populated records in the **Mock Store** used to demonstrate platform features without needing real user interactions. Includes a complete demo Student profile (*Aarav Sharma*) and a demo Recruiter (*Priya Mehta*).

---

*This document should be updated collaboratively whenever new domain concepts are introduced or existing terms are renamed. All team members — developers, designers, and PMs — must use these terms consistently in code, UI, APIs, and verbal communication.*
