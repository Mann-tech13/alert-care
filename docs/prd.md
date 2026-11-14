# CareCircle — PRD Amendment v1.1

**Amendment:** Frontend, backend, database, authentication, and notification architecture  
**Date:** 13 November 2026  
**Status:** Approved direction for technical planning

## Updated Technology Stack

| Component | Technology |
|---|---|
| Web frontend | React.js + TypeScript + Vite |
| Android application | Capacitor wrapper around the shared React application |
| UI | Responsive, mobile-first React components |
| Backend framework | NestJS on Node.js |
| API style | REST |
| Primary database | PostgreSQL |
| ORM | Prisma |
| Authentication | Email OTP |
| Email provider | Resend |
| Parent reminders | Capacitor local notifications on Android |
| Family follow-ups | Server-side scheduled jobs and push notifications |
| Push delivery | Firebase Cloud Messaging |
| Job processing | PostgreSQL-backed jobs initially, with Redis/BullMQ as an optional later implementation |
| Monorepo | pnpm workspaces |

## Frontend Requirements

The application must maintain a shared React source code for the browser-based dashboard and the packaged Android application.

Capacitor will provide access to supported native Android features through plugins.

Platform-specific functionality must be isolated behind service abstractions.

The Android application must not depend on an active WebView session to execute server-side family follow-up logic.

Browser notification support is not guaranteed to match native Android notification behavior.

## Backend Requirements

NestJS will be used as a modular monolithic backend.

Required domain modules include:

- Authentication
- Users
- Family Workspaces
- Workspace Memberships
- Parent Profiles
- Consent and Access Grants
- Medications
- Medication Schedules
- Dose Events
- Notifications
- Audit Logs

All protected API operations must enforce server-side authorization and profile-level access checks.

## Database Requirements

PostgreSQL will be the system of record.

Prisma will manage the relational schema, migrations, and standard database operations.

The schema must support:

- Workspace-scoped roles
- Parent-profile-level authorization
- Medication schedules
- Individual scheduled dose occurrences
- Immutable response and audit history
- Notification attempts
- Consent records
- Idempotent follow-up processing

Flexible metadata may be stored using PostgreSQL JSONB where justified.

## Authentication Requirements

Users will authenticate using email OTP delivered through Resend.

Requirements include:

- Cryptographically secure OTP generation
- Short OTP expiration
- Hashed OTP storage
- Single-use verification
- Rate limiting and attempt limits
- Secure session management
- Email verification before account activation
- Session revocation and logout
- Protection against account enumeration

Email addresses must be verified before they can be used to accept family invitations.

## Notification Requirements

The Android application will use Capacitor local notifications for routine medication reminders.

The backend will evaluate family follow-up rules and use FCM for eligible notifications.

Notification scheduling must account for:

- Android permissions
- Exact-alarm restrictions
- Battery optimization
- Device restarts
- Offline periods
- Schedule edits
- Duplicate jobs
- Stale device tokens
- Consent revocation
- Failed delivery attempts

The product must not guarantee exact delivery times or interpret an unconfirmed notification as a missed medication.

## Updated Platform Assumptions

Phase 1 supports:

- Responsive browser-based React application
- Android application packaged with Capacitor
- Email OTP authentication
- One linked parent per family workspace
- Shared frontend business logic
- Platform-specific notification adapters

Phase 1 excludes:

- React Native
- iOS application distribution
- SMS OTP
- WhatsApp authentication
- Advanced offline-first data synchronization
- Microservices architecture

## Architecture Decision Rationale

PostgreSQL is selected because the product requires relational consistency, permission boundaries, auditability, and transactional dose-event updates.

NestJS is selected for its modular structure, dependency injection, validation support, and suitability for growing domain complexity.

Capacitor is selected to preserve a shared React web/mobile UI while providing access to native Android notification capabilities.

Resend is selected as a low-cost transactional email provider for the initial pilot.

**This amendment supersedes conflicting technical stack and authentication references in PRD v1.0.**