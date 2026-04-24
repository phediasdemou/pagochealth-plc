# Log — pagochealth-plc

Append-only audit trail of firmware changes, doc updates, and syncs to the company wiki. **Newest entry at the top.** Parseable with `grep "^## \[" docs/log.md`.

Ops: `feature`, `fix`, `refactor`, `doc`, `adr`, `sync` (when the change also touched `../pagochealth-wiki/`).

## [2026-04-24] schema | Updated Notion hub URL

- Swapped the Notion hub URL in `CLAUDE.md` to point at the pre-existing "Pagoc Health" Notion page (https://www.notion.so/phediasdemou/Pagoc-Health-a0342887d008491d85752b6a559bc1d3) instead of the freshly-created one. The pre-existing page already holds operational data; standing instruction is to `notion-fetch` and adapt to existing structure rather than impose a new one.

## [2026-04-24] schema | Added Notion to cross-repo sync policy

- Updated `CLAUDE.md` — extended the cross-repo sync trigger table with an "Also post to Notion?" column, pointing at the [Notion project hub](https://www.notion.so/phediasdemou/Pagoc-Health-Claude-OS-34c08dca8c3b80faa9cad41a45f1b230).
- When Notion MCP is connected, supplier choices / cross-system decisions / strategic shifts get posted to Notion automatically in the same turn as the wiki update. When disconnected, those are surfaced in the turn summary for manual posting.

## [2026-04-24] doc | Bootstrapped repo docs skeleton

- Created `CLAUDE.md` at repo root — code conventions, tech stack (Arduino OPTA + GIGA + GIGA Display Shield, PlatformIO, Modbus TCP, LVGL), `docs/` rules, cross-repo sync triggers with pointer to `../pagochealth-wiki/CLAUDE.md` as workspace authority.
- Created `docs/` skeleton: `index.md`, this `log.md`, `architecture/` (overview, io-map, communication, state-machine — all stub), `components/opta/`, `components/hmi/`, `decisions/` (all `.gitkeep`).
- Added `.gitignore` for PlatformIO build artifacts, macOS cruft, editor caches.
- No firmware code yet — this commit is scaffolding only.
