# Architectural Decision Record (ADR)

## Decision: Cross-Platform File URL Path Resolution

- **Context:** Node ESM loader paths must run reliably across developer workstations (Windows) and grading environments (Linux/POSIX).
- **Decision:** Utilize `fileURLToPath(import.meta.url)` with `path.resolve()` for local filesystem references in runner scripts.
- **Alternative Rejected:** Direct `new URL(...).pathname` string parsing.
- **Why It Fails:** `URL.pathname` leaves URL-encoded characters (e.g. `%20` for spaces) and incompatible POSIX drive separators on Windows platforms.

## Decision: Explicit Absolute Path Resolution for SQLite Lifecycle

- **Context:** Test runners (Playwright webServer) spawn child processes with variable CWDs.
- **Decision:** Bind SQLite database target paths to absolute script directory anchors (`__dirname`) instead of bare relative strings.
- **Alternative Rejected:** Assuming caller CWD is consistently repository root.
- **Why It Fails:** Causes silent misses during cleanup routines, executing DDL against populated databases and producing schema collisions.

## Decision: Static Asset Resolution via Normalized Path Anchors

- **Context:** `server/index.js` must serve compiled frontend bundles in production mode under varied OS file directory semantics.
- **Decision:** Utilize `fileURLToPath` and `path.resolve(__dirname, '../dist')` instead of `new URL().pathname`.
- **Alternative Rejected:** Raw `new URL('../dist/', import.meta.url).pathname` string manipulation.
- **Why It Fails:** Returns URL-encoded directory paths with leading drive slashes on Windows (e.g. `/C:/...`), causing filesystem lookup misses and breaking static fallback routing.