# Build Log

## 2026-09-27 08:50 IST - Repository Setup & Baseline
- Cloned repository from template fork into local environment.
- Identified core documentation files: BRIEF.md, AUTH_DATA_MODEL.md, PERMISSIONS.md, and starter directories.
- Initialized build log and architectural decision record.
- Next step: review schema specifications in AUTH_DATA_MODEL.md and inspect server/auth.js.

## 2026-09-27 09:15 IST - Windows Path Resolution Fix for DB Seeder
- Issue: `npm run db:load` failed on Windows with ENOENT (`C:\C:\Users\...`).
- Cause: `new URL(p, import.meta.url).pathname` produced a URL-encoded path with a leading slash `/C:/...`, which Node's `fs` misinterpreted on Windows.
- Solution: Refactored `scripts/load-db.js` to use `fileURLToPath(import.meta.url)` alongside Node's `path.resolve` and `path.dirname`.
- Result: Database seeded successfully (`app.db` initialized with 2 orgs, 6 users, 8 memberships, 19 permissions).

## 2026-09-27 09:30 IST - JWT Validation Verification
- Executed `node scripts/check-jwt.js`.
- Verified all 43 test assertions passed (token round-trips, malformed inputs, algorithm substitution defense, constant-time signature comparison, half-open exp expiration, issuer/aud validation, refresh token handling).
- Next: Inspect `server/permissions.js` and ensure dynamic database querying for roles and permissions rather than hardcoded sets.

## 2026-09-27 09:40 IST - Playwright Setup & Initial Test Execution
- Installed Playwright browser binaries via `npx playwright install chromium`.
- Ran test suite to establish an end-to-end baseline against seeded fixtures.

## 2026-09-27 10:25 IST - Playwright WebServer SQLite Collision Fix
- Bug: `npm test` threw `SqliteError: table roles already exists`.
- Root Cause: Relative `DATABASE_FILE` resolution inside `scripts/load-db.js` resolved against the caller's working directory rather than the package root, skipping deletion of stale SQLite files before running `schema.sql`.
- Fix: Bound `DB_FILE` to `resolve(__dirname, '../app.db')` with `force: true` removal of DB, WAL, and SHM segments.

## 2026-09-27 10:55 IST - Backend Core Verification
- Executed `node scripts/check-permissions.js`: 35/35 test cases passed (role baselines, auditor/operator parity, device-scoped grants, org-wide vs device-level precedence, compound checks, grandfathering, and suspended memberships).
- Executed `node scripts/check-api.js`: 66/66 test cases passed (auth lifecycle, cross-org opacity/404s, exclusive session concurrency, pagination limits, invite flows, and audit log immutability).
- Total verified backend test assertions: 144/144 passed across JWT, Permissions, and API test suites.
- Next: Debug web server boot in dev/test environment for Playwright UI validation.