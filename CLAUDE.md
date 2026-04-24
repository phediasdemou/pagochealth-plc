# Pagoc Health PLC + HMI — Claude Code Instructions

This repo contains the firmware for the **PLC (Arduino OPTA)** and **HMI (Arduino GIGA R1 + GIGA Display Shield)** sub-systems of the Pagoc Health ice bath.

Read this file at the start of every session in this repo.

---

## Before cross-project work: read the wiki CLAUDE.md

Workspace-wide rules, cross-repo sync triggers, and the company wiki schema live in **`../pagochealth-wiki/CLAUDE.md`**. It is the authority for anything that crosses sub-system boundaries.

**When to read it**:
- Any time a supplier / vendor is chosen
- Any time a cross-sub-system architectural decision is made (e.g., protocol change, shared tag map update, product-line-wide choice)
- Any time a strategic shift is discussed (pricing, positioning, market)
- Any time a new business concept emerges
- Any time you're about to update the wiki

For purely **internal firmware work** (component documentation, firmware-specific ADRs, code refactors, bug fixes), you don't need to read the wiki — just follow the rules in this file.

---

## What this repo contains

- **`opta/`** — Arduino OPTA firmware (control logic, Modbus TCP slave, I/O)
- **`giga/`** — Arduino GIGA firmware (HMI via LVGL, network, Modbus TCP master)
- **`shared/`** — tag definitions and protocol structures used by both firmware targets (e.g., `protocol.h`)
- **`docs/`** — repo-local technical documentation (see below)

Future additions (not yet scaffolded): `tests/` for simulation scripts and test plans.

---

## Tech stack

| Target | Hardware | Platform | Key libraries |
|---|---|---|---|
| PLC | Arduino OPTA | PlatformIO | `ArduinoModbus` (slave) |
| HMI | Arduino GIGA R1 + GIGA Display Shield | PlatformIO | `ArduinoModbus` (master), `Arduino_H7_Video`, `lvgl` |

**Build system**: PlatformIO. Each firmware target has its own `platformio.ini`. Do not use the Arduino IDE — PlatformIO is the single source of truth for dependencies and build configuration.

**Inter-firmware protocol**: Modbus TCP. OPTA is the slave (exposes I/O as registers); GIGA is the master (polls state, writes commands). The authoritative tag map lives in `docs/architecture/communication.md`, and the corresponding C header in `shared/protocol.h`.

---

## Coding conventions

- **Language**: C / C++ via Arduino framework on PlatformIO.
- **Naming**:
  - Functions: `camelCase`.
  - Constants / `#define` macros: `SCREAMING_SNAKE_CASE`.
  - Modbus register names: `REG_<DOMAIN>_<NAME>` (e.g., `REG_PUMP_MAIN_STATE`).
  - Files: `snake_case.cpp` / `snake_case.h`.
- **Layout**: keep control logic modular — one function per responsibility. Long `loop()` bodies and inline state machines are discouraged; extract into functions or small classes.
- **No blocking calls in OPTA control paths.** The OPTA owns real-time physical control; do not let network operations or slow peripherals block the control loop.
- **Safety first.** Any change that touches E-stop behavior, safety interlocks, or hardware limits gets a dedicated ADR in `docs/decisions/`.

---

## `docs/` — repo-local documentation

Every non-trivial firmware change updates `docs/` in the same turn. Structure:

```
docs/
├── index.md                   # catalog of all doc pages
├── log.md                     # append-only audit trail of repo changes
├── architecture/
│   ├── overview.md            #   OPTA ↔ GIGA block diagram, Modbus topology
│   ├── io-map.md              #   every OPTA input/output and what it controls
│   ├── communication.md       #   Modbus TCP register map (authoritative)
│   └── state-machine.md       #   product state machine: fill / heat / cool / drain / idle
├── components/
│   ├── opta/                  #   one page per control function block
│   └── hmi/                   #   one page per HMI screen
└── decisions/                 #   firmware-specific ADRs
```

### What goes where

| Type of knowledge | Destination |
|---|---|
| How a specific function block works, inputs/outputs, dependencies | `docs/components/opta/<Block>.md` |
| How a specific HMI screen renders and what it shows | `docs/components/hmi/<Screen>.md` |
| Physical I/O mapping (OPTA pins ↔ physical hardware) | `docs/architecture/io-map.md` |
| Modbus register definitions (OPTA ↔ GIGA) | `docs/architecture/communication.md` |
| Product state machine (fill, heat, cool, drain, idle) | `docs/architecture/state-machine.md` |
| Firmware-specific ADR (e.g., "chose library X over Y", "refactored Z module") | `docs/decisions/YYYY-MM-DD-title.md` |
| Supplier chosen, cross-system protocol change, business concept | **Wiki, not here** — see cross-repo sync policy |

### When to write to `docs/`

- Adding a new function block → add a page under `docs/components/opta/`.
- Adding a new HMI screen → add a page under `docs/components/hmi/`.
- Changing a Modbus register → update `docs/architecture/communication.md`.
- Making a firmware-local architectural decision (library choice, code-org change) → create an ADR.
- Appending to `docs/log.md` happens with **every** non-trivial change (new feature, fix, refactor that merits tracking).

---

## `docs/index.md` & `docs/log.md` conventions

### `index.md`

One line per doc page: `- [[Page Name]] — one-line summary`. Sections match the folder structure. Update on every doc change.

### `log.md`

Append-only, **newest at the top**. Parseable with `grep "^## \[" docs/log.md`.

```
## [YYYY-MM-DD] <op> | <short title>

- 2–4 bullets: what changed, which files touched, any open threads.
```

Ops: `feature`, `fix`, `refactor`, `doc`, `adr`, `sync` (when a change also synced to wiki).

---

## Cross-repo sync (what triggers a wiki update)

When a change in this repo satisfies any of these triggers, **also update the wiki in the same turn** (see `../pagochealth-wiki/CLAUDE.md` for the full policy):

| Trigger | Wiki destination |
|---|---|
| Supplier / vendor chosen | `pagochealth-wiki/entities/` |
| Cross-sub-system decision (protocol, shared tag map, product-line architecture) | `pagochealth-wiki/decisions/` |
| Strategic shift (pricing, positioning, market) | `pagochealth-wiki/strategy/` |
| Business concept emerges | `pagochealth-wiki/concepts/` |

After writing to the wiki, append a `sync` entry to **both** `docs/log.md` (here) and `pagochealth-wiki/log.md`.

---

## Commit and PR rules

- **Commit messages**: imperative mood ("add OPTA pump control", not "added…"). First line ≤72 chars. Body explains *why* if non-obvious.
- **Keep commits scoped.** One logical change per commit. Separate firmware changes from doc changes when reasonable, but doc-together-with-code is acceptable when they're tightly coupled (e.g., adding a feature + its component doc).
- **Never skip hooks** (`--no-verify`) unless Phedias explicitly requests it.
- **PRs**: for any change touching safety interlocks, E-stop, or Modbus protocol-level behavior. For routine feature additions on a solo branch, direct commits to `main` are fine until collaborators are added.

---

## What does NOT belong here

- **Business / strategic knowledge** — lives in the wiki.
- **Supplier / vendor information** — wiki `entities/`.
- **Cross-sub-system decisions** — wiki `decisions/`.
- **Personal reflection or non-project material** — Phedias Brain (separate vault).
- **Secrets / credentials** — never committed; use `.env` (git-ignored) or a secrets manager.

---

## Special behaviors

- **On session start**, read this file and skim `docs/log.md` (last 5 entries) to understand recent state.
- **Don't narrate the workflow** on every turn. Just do the work, summarize what changed in 2–4 lines.
- **Evolve this file.** When a convention works, document it here. When one's wrong, remove it.
