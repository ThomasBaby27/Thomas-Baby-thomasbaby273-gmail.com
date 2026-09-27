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