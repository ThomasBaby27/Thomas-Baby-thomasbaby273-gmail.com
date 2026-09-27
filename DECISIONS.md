# Architectural Decision Record (ADR)

## Decision: Cross-Platform File URL Path Resolution

- **Context:** Node ESM loader paths must run reliably across developer workstations (Windows) and grading environments (Linux/POSIX).
- **Decision:** Utilize `fileURLToPath(import.meta.url)` with `path.resolve()` for local filesystem references in runner scripts.
- **Alternative Rejected:** Direct `new URL(...).pathname` string parsing.
- **Why It Fails:** `URL.pathname` leaves URL-encoded characters (e.g. `%20` for spaces) and incompatible POSIX drive separators on Windows platforms.