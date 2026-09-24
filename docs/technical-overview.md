# Classify AI — Technical Overview

This document keeps the deeper implementation notes that are intentionally omitted from the main README so the repository landing page stays concise and recruiter-friendly.

## Platform model

Classify AI is a multi-role academic platform with five role layers:

- **Admin** — global platform-level control
- **Assistant** — campus-level operations and user management
- **Teacher** — attendance, assignments, resources, announcements, analytics
- **HOD** — timetable and schedule authority, implemented as an extended teacher designation
- **Student** — attendance, timetable, assignments, resources, announcements, grades, and AI-assisted tools

A campus can maintain its own assistant, users, academic configuration, geolocation rules, timetable, events, announcements, and operational settings.

## Core stack

- Next.js 15
- React 19
- TypeScript
- PostgreSQL
- Prisma ORM
- Tauri 2 / Rust wrapper
- Pusher
- Firebase
- Cloudinary
- face-api.js
- Redis / Upstash
- OpenAI and Google GenAI integrations

The current app/package version is **2.4.0**.

## Attendance architecture

The attendance flow is designed to reduce proxy attendance through layered verification.

Typical flow:

1. A teacher starts an attendance session.
2. A student accesses or scans the session/QR flow.
3. The system validates the student identity and session state.
4. One-time/session token checks are performed.
5. Face verification can be used as an identity layer.
6. Latitude/longitude and geofencing rules can be checked for campus presence.
7. Attendance is stored only after required validation succeeds.

Attendance-related capabilities include:

- teacher-managed attendance sessions
- one-time/session token validation
- face verification
- geolocation/geofencing support
- attendance history
- subject-wise attendance percentage
- attendance analytics
- attendance report export
- low-attendance warning workflows
- audit logging for attendance edits

Implementation notes from ongoing development have included synchronizing student attendance statistics with teacher actions, simplifying ambiguous Prisma aggregation logic, and preserving consistent attendance date fields across history views.

## Assignment workflow

Teacher-side capabilities include:

- create and edit assignments
- set due dates
- track submitted, late, and missing submissions
- grade submissions
- text feedback
- audio feedback where configured
- graded PDF workflows
- digital-signature support where configured
- AI/plagiarism indicators where enabled

Student-side capabilities include:

- view assignments
- submit text or supported files
- late-submission handling
- view grades and feedback
- access graded copies where supported

## Timetable system

HOD functionality extends the Teacher role and provides department/class timetable authority.

Implemented timetable capabilities include:

- configure working days and day timings
- create, edit, and delete timetable slots
- assign teacher, subject, semester, section, room, and notes
- support lecture, lab, tutorial, extra class, lunch, break, free, exam, and event slot types
- validate teacher conflicts
- validate semester/section conflicts
- validate room conflicts
- validate teacher-subject assignments

Teachers and students can view schedules generated from HOD-created slots.

A previous display issue where saved timetable times could shift because of timezone formatting was addressed by using timetable-safe time formatting.

## Real-time campus chat

The platform includes a role-aware real-time chat layer for Admin, Assistant, Teacher, and Student users.

Implemented capabilities include:

- session-based identity resolution
- RBAC-aware communication rules
- real-time conversations
- private channel support
- typing indicators
- read receipts
- message reactions
- message edit/delete
- pinned messages
- new-message notifications

Targeted communication patterns include:

- Student ↔ Teacher
- Teacher ↔ Class/Section
- Teacher groups
- Student groups
- Teacher ↔ Assistant
- Admin ↔ Assistant

A previous identity-resolution issue caused by relying on localStorage role IDs was replaced with server-side session-based user resolution.

### Ongoing chat hardening

Some deeper API cleanup remains appropriate: chat-related endpoints should consistently derive actor identity from the server-side session instead of trusting frontend-provided identifiers such as `userId`, `senderId`, `creatorId`, `requesterId`, or `uploadedBy`.

Offline sync and direct module-integrated message actions are not currently presented as completed features.

## Assistant / campus operations

Campus Assistants can handle operational tasks such as:

- campus setup and verification
- students and teachers
- semester/section-related configuration
- events and holidays
- announcements
- attendance overviews
- premium-plan visibility/status
- recent activity and audit logs
- campus-level analytics
- CSV export workflows

Campus setup can include identity, logo, geolocation, and Wi-Fi/network reference information.

## Teacher workflows

Teacher modules cover:

- attendance
- assignment creation and grading
- assignment analytics
- announcements
- academic resources
- class/timetable views
- attendance analytics
- report/export workflows

See [`teacher-dashboard-features.md`](./teacher-dashboard-features.md) for an additional implementation checklist.

## Student workflows

Student-facing features include:

- attendance marking and history
- subject-wise attendance percentage
- daily and weekly timetable views
- assignments and grades
- teacher feedback
- resources
- announcements
- exams and academic activity views
- premium tools such as Bunk Manager and AI Study Planner where enabled

## AI-assisted features

AI-assisted modules can include:

- study planning
- syllabus analysis
- expected-question generation
- resource summarization where configured
- multi-provider AI integration

AI modules can be separated or disabled for deployments that do not require them.

## Notifications, storage, and supporting services

The project uses or integrates services for:

- push notifications
- real-time events
- cloud media/storage
- email/OTP workflows
- PDF generation and viewing
- QR generation/scanning
- payments/subscriptions where configured
- Redis-backed workflows where configured

## Current status

Classify AI is under active development. Major modules are implemented, while some areas are still being hardened, simplified, or prepared for controlled production use.

For institutional deployment, the safer rollout path is:

1. deploy a controlled staging/demo version
2. review required modules with the institution
3. disable experimental/unneeded features
4. define production scope and responsibilities
5. perform security, configuration, and operational review before full rollout

## Security and configuration notes

- Never commit production environment variables, API keys, database credentials, or service secrets.
- Prefer server-side session identity for privileged operations.
- Treat frontend-provided identity fields as untrusted unless validated server-side.
- Review geolocation, biometric/face data, notification, and storage handling before institutional production deployment.

## Usage notice

Classify AI is shared publicly for demonstration, portfolio, and educational review purposes.

Commercial, institutional, or production use requires prior written permission from the author.

**Author:** Vaibhav Mali
