---
type: architecture
tags: [architecture, modbus, communication]
created: 2026-04-24
updated: 2026-04-24
---

# Communication — Modbus TCP Register Map

Authoritative register map for OPTA ↔ GIGA communication. **This is the single source of truth**; both firmwares derive from this (ideally via the shared header in `../../shared/protocol.h`).

## Roles

- **OPTA**: slave. Listens on Modbus TCP port 502 (or configured port). Exposes state registers and command registers.
- **GIGA**: master. Polls OPTA for state; writes commands based on operator input.

## Network configuration

- **OPTA IP**: _TBD_ (recommend static)
- **GIGA IP**: _TBD_ (recommend static)
- **Port**: 502 (Modbus TCP standard)
- **Polling cadence**: _TBD_ (typical: 100–500 ms for state; commands are event-driven)

## Register conventions

| Range | Purpose |
|---|---|
| `0x0000` – `0x00FF` | Reserved (status / identification) |
| `0x0100` – `0x01FF` | State registers (read-only from GIGA) |
| `0x0200` – `0x02FF` | Command registers (writable from GIGA) |
| `0x0300` – `0x03FF` | Configuration / setpoints |
| `0x0400` – `0x04FF` | Alarms / events |

Ranges above are a starting convention — adjust as the map matures.

## State registers (0x0100–0x01FF)

| Register | Name | Type | Description |
|---|---|---|---|
| _TBD_ | `REG_STATE_MACHINE` | uint16 | Current state (see [[state-machine]]) |
| _TBD_ | `REG_WATER_TEMP` | int16 | Water temperature × 10 (e.g., 52 = 5.2°C) |

## Command registers (0x0200–0x02FF)

| Register | Name | Type | Description |
|---|---|---|---|
| _TBD_ | `REG_CMD_FILL_START` | uint16 | Write non-zero to initiate fill |
| _TBD_ | `REG_CMD_DRAIN_START` | uint16 | Write non-zero to initiate drain |

## Alarms (0x0400–0x04FF)

| Register | Name | Type | Description |
|---|---|---|---|
| _TBD_ | `REG_ALARM_ESTOP_ACTIVE` | uint16 | Non-zero if E-stop pressed |
| _TBD_ | `REG_ALARM_OVERTEMP` | uint16 | Non-zero on over-temperature |

## Changelog

- 2026-04-24: Stub created. No registers populated yet — fill as firmware is written.

## Related

- [[overview]] — why Modbus TCP
- [[io-map]] — the physical I/O that backs these registers
- [[state-machine]] — state encoding used in `REG_STATE_MACHINE`
