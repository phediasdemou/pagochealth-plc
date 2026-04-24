# Log — pagochealth-plc

Append-only audit trail of firmware changes, doc updates, and syncs to the company wiki. **Newest entry at the top.** Parseable with `grep "^## \[" docs/log.md`.

Ops: `feature`, `fix`, `refactor`, `doc`, `adr`, `sync` (when the change also touched `../pagochealth-wiki/`).

## [2026-04-24] doc | Bootstrapped repo docs skeleton

- Created `CLAUDE.md` at repo root — code conventions, tech stack (Arduino OPTA + GIGA + GIGA Display Shield, PlatformIO, Modbus TCP, LVGL), `docs/` rules, cross-repo sync triggers with pointer to `../pagochealth-wiki/CLAUDE.md` as workspace authority.
- Created `docs/` skeleton: `index.md`, this `log.md`, `architecture/` (overview, io-map, communication, state-machine — all stub), `components/opta/`, `components/hmi/`, `decisions/` (all `.gitkeep`).
- Added `.gitignore` for PlatformIO build artifacts, macOS cruft, editor caches.
- No firmware code yet — this commit is scaffolding only.
