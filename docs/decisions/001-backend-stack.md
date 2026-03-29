# ADR-001: Backend Stack — Supabase + Dexie.js

**Status:** Accepted (open to revision)
**Date:** 2026-03-29

## Context

The app needs a backend for data persistence, authentication, file storage, and eventually offline support. The original plan was Google Sheets as a starting point, but we reconsidered given the friction of Sheets as a database (rate limits, no auth, no real-time, no file storage) and the availability of free-tier BaaS platforms that provide all of this out of the box.

## Options Considered

| Option | Pros | Cons |
|--------|------|------|
| **Google Sheets** | Sofía knows it, zero setup | Rate limits, no auth, no file storage, no offline, not a real DB. Teaches nothing transferable. |
| **Firebase** | Excellent offline support (Firestore), hosting included, all-in-one | NoSQL (less transferable), high vendor lock-in, proprietary |
| **Supabase** | PostgreSQL (industry standard SQL), open source, low lock-in, great TypeScript SDK, auth + storage included | No built-in offline support, 1GB file storage on free tier |
| **Appwrite** | Open source, self-hostable, SQL | Smaller community, no advantage over Supabase for this use case |
| **PocketBase** | Single binary, dead simple | Too simple, would outgrow quickly |

## Decision

**Supabase + Dexie.js**, with Cloudflare R2 as a future file storage upgrade path.

| Concern | Tool | Cost |
|---------|------|------|
| Database | Supabase (PostgreSQL) | $0 |
| Auth | Supabase Auth | $0 |
| File storage | Supabase Storage (1GB) → Cloudflare R2 (10GB) when needed | $0 |
| Offline | Dexie.js (IndexedDB) + custom sync to Supabase | $0 |
| Hosting | Vercel or Netlify (PWA) | $0 |
| Serverless functions | Supabase Edge Functions or Vercel Functions | $0 |

## Rationale

- **Learning value:** PostgreSQL and SQL are the most transferable database skills. Supabase's TypeScript SDK and Row Level Security are industry-relevant patterns.
- **Offline support:** Dexie.js provides IndexedDB-based local storage. Building a custom sync layer teaches real-world offline-first architecture, which is more valuable than Firebase's "magic" approach.
- **Zero cost:** All tools are free at single-user scale. Supabase free tier: 500MB DB, 1GB file storage, 50K auth MAUs. File storage can migrate to Cloudflare R2 (10GB free) when needed.
- **Low vendor lock-in:** PostgreSQL is portable. Dexie.js is a client-side library with no backend dependency. If we outgrow Supabase, we migrate the Postgres DB elsewhere.
- **Repository pattern:** All data access goes through repository interfaces. The Supabase implementation can be swapped for any other backend without touching domain or UI code.

## Consequences

- We need to build the sync layer ourselves (Dexie.js ↔ Supabase). This is more work than Firebase but higher learning value.
- IndexedDB data is unencrypted on the device. Acceptable for single-user MVP; revisit if multi-user.
- File storage is limited to 1GB on free tier (~1-1.5 years of receipts for one user). Migration path to R2 is documented.
- RLS must be enabled on every Supabase table from day one — this is a hard security rule.
