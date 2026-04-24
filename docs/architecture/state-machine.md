---
type: architecture
tags: [architecture, state-machine]
created: 2026-04-24
updated: 2026-04-24
---

# Product State Machine

The top-level state machine for the ice bath product. The OPTA owns this state; the GIGA reads it via `REG_STATE_MACHINE` (see [[communication]]).

## States

| ID | Name | Description |
|---|---|---|
| 0 | `IDLE` | Powered on, no active operation. Default after boot. |
| 1 | `FILL` | Filling the bath from water supply. Exits when target level reached. |
| 2 | `HEAT` | Heating water to setpoint (if heating is a feature). Exits when setpoint reached. |
| 3 | `COOL` | Cooling water to setpoint via refrigeration loop. Exits when setpoint reached. |
| 4 | `READY` | Bath is at target temperature; operator may enter. |
| 5 | `DRAIN` | Draining water. Exits when empty. |
| 99 | `FAULT` | Safety condition triggered (E-stop, over-temp, etc.). Requires operator acknowledgement. |

_State IDs are provisional — finalize when the control firmware is written._

## Transitions

```
          ┌────────┐
          │ IDLE   │◄─────────────────────┐
          └───┬────┘                      │
              │ CMD_FILL_START            │ CMD_IDLE / drain complete
              ▼                           │
          ┌────────┐                      │
          │ FILL   │                      │
          └───┬────┘                      │
              │ level target reached      │
              ▼                           │
          ┌────────┐      ┌────────┐      │
          │ COOL   │ or   │ HEAT   │      │
          └───┬────┘      └───┬────┘      │
              │ setpoint reached          │
              ▼                           │
          ┌────────┐                      │
          │ READY  │                      │
          └───┬────┘                      │
              │ CMD_DRAIN_START           │
              ▼                           │
          ┌────────┐                      │
          │ DRAIN  │──────────────────────┘
          └────────┘

Any state ──► FAULT on safety trigger
FAULT ──► IDLE on operator acknowledge
```

## Safety layer

`FAULT` can be entered from any state on:

- E-stop press
- Over-temperature
- Level sensor failure
- Loss of Modbus communication (optional — GIGA → OPTA heartbeat)

Exit from `FAULT` requires operator acknowledgement via the HMI. The OPTA does not auto-clear faults.

## Related

- [[overview]] — where this state machine lives (OPTA)
- [[communication]] — how GIGA reads this state
- [[io-map]] — the physical I/O that drives state transitions
