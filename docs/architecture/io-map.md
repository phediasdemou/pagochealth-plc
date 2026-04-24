---
type: architecture
tags: [architecture, io, opta]
created: 2026-04-24
updated: 2026-04-24
---

# OPTA I/O Map

Authoritative mapping of every OPTA physical input and output to what it controls. Update this page whenever wiring changes or I/O is added/removed — the firmware should never have an I/O reference that isn't listed here.

## OPTA hardware inventory (to be filled)

Arduino OPTA variants differ in I/O count; record the actual unit used in production.

- **Model**: _TBD_
- **Digital inputs**: _TBD_
- **Analog inputs**: _TBD_
- **Relay outputs**: _TBD_
- **Expansion modules**: _TBD_

## I/O table

| Pin | Direction | Signal | Connected to | Notes |
|---|---|---|---|---|
| _TBD_ | input | _e.g., TEMP_WATER_IN_ | PT100 probe, water inlet | 4–20 mA loop |
| _TBD_ | output | _e.g., RELAY_PUMP_MAIN_ | Main circulation pump contactor | 24 VDC |

_Populate this table as physical wiring is finalized._

## Safety I/O

Safety-critical I/O (E-stop, level switches, over-temp) should be called out explicitly:

- **E-stop**: _TBD_
- **High-level float switch**: _TBD_
- **Over-temperature cutoff**: _TBD_

## Related

- [[overview]] — firmware architecture
- [[communication]] — Modbus register map (maps these physical I/O to GIGA-visible registers)
