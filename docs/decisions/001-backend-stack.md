# ADR-001: Backend Stack — Supabase + Dexie.js + Google Drive

**Status:** Revised
**Date:** 2026-03-29
**Revised:** 2026-04-07

## Context

The app needs a backend for data persistence, authentication, file storage, and eventually offline support. The original plan was Google Sheets as a starting point, but we reconsidered given the friction of Sheets as a database (rate limits, no auth, no real-time, no file storage) and the availability of free-tier BaaS platforms that provide all of this out of the box.

**Revision (2026-04-07):** File storage was originally planned as Supabase Storage (1GB free) with a migration path to Cloudflare R2 (10GB free). This was revised to use **Google Drive** instead — see Decision table and Rationale below.

## Options Considered

| Option | Pros | Cons |
|--------|------|------|
| **Google Sheets** | Sofía knows it, zero setup | Rate limits, no auth, no file storage, no offline, not a real DB. Teaches nothing transferable. |
| **Firebase** | Excellent offline support (Firestore), hosting included, all-in-one | NoSQL (less transferable), high vendor lock-in, proprietary |
| **Supabase** | PostgreSQL (industry standard SQL), open source, low lock-in, great TypeScript SDK, auth + storage included | No built-in offline support, 1GB file storage on free tier |
| **Appwrite** | Open source, self-hostable, SQL | Smaller community, no advantage over Supabase for this use case |
| **PocketBase** | Single binary, dead simple | Too simple, would outgrow quickly |

## Decision

**Supabase + Dexie.js + Google Drive.**

| Concern | Tool | Cost |
|---------|------|------|
| Database | Supabase (PostgreSQL) | $0 |
| Auth | Supabase Auth | $0 |
| File storage | Google Drive (15 GB free, via Google Drive API) | $0 |
| Offline | Dexie.js (IndexedDB) + custom sync to Supabase | $0 |
| Hosting | Vercel or Netlify (PWA) | $0 |
| Serverless functions | Supabase Edge Functions or Vercel Functions | $0 |

## Rationale

- **Learning value:** PostgreSQL and SQL are the most transferable database skills. Supabase's TypeScript SDK and Row Level Security are industry-relevant patterns.
- **Offline support:** Dexie.js provides IndexedDB-based local storage. Building a custom sync layer teaches real-world offline-first architecture, which is more valuable than Firebase's "magic" approach.
- **Zero cost:** All tools are free at single-user scale. Supabase free tier: 500MB DB, 50K auth MAUs.
- **Google Drive for file storage (revised):** 15 GB free vs. Supabase Storage's 1 GB — 15x more headroom. Sofía already wants a Drive backup for all receipts and documents, so it doubles as both primary storage and backup. Files are private to her Google account. Supabase holds only file metadata (`drive_file_id`, filename, mime type, size) — no binary blobs in the database. Tradeoff: requires Google OAuth setup, which adds complexity and introduces an "unverified app" warning for new users (acceptable for single-user MVP).
- **Low vendor lock-in:** PostgreSQL is portable. Dexie.js is a client-side library with no backend dependency. File storage is abstracted behind a repository interface — Drive can be swapped for S3, Cloudflare R2, or any other provider.
- **Repository pattern:** All data access goes through repository interfaces. The Supabase implementation can be swapped for any other backend without touching domain or UI code.

## Consequences

- We need to build the sync layer ourselves (Dexie.js ↔ Supabase). This is more work than Firebase but higher learning value.
- IndexedDB data is unencrypted on the device. Acceptable for single-user MVP; revisit if multi-user.
- Google OAuth is required for Drive integration. Token refresh must be handled silently and robustly. New users will see a "This app isn't verified" consent screen warning — acceptable for Sofía as MVP's sole user; requires Google verification process if app expands to other users.
- Supabase holds no file binaries — only Drive metadata. If a user's Google account is near capacity (15 GB shared across Gmail, Photos, Drive), uploads may fail. Warn users proactively when Drive storage is low.
- RLS must be enabled on every Supabase table from day one — this is a hard security rule.
