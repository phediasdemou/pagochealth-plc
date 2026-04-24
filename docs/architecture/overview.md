---
type: architecture
tags: [architecture, opta, giga, modbus]
created: 2026-04-24
updated: 2026-04-24
---

# Firmware Architecture — Overview

Block-diagram view of the OPTA + GIGA firmware and how they connect. For product-level architecture across all sub-systems, see `../../../pagochealth-wiki/architecture/overview.md`.

## Block diagram

```
┌────────────────────────────────────┐         ┌────────────────────────────────────┐
│  Arduino OPTA (PLC)                │         │  Arduino GIGA R1 + Display Shield  │
│                                    │         │  (HMI)                             │
│  - Modbus TCP slave                │◄───────►│  - Modbus TCP master               │
│  - I/O to physical hardware        │  Modbus │  - LVGL UI on touch display        │
│  - Safety interlocks               │  TCP    │  - Network (WiFi / Ethernet)       │
│  - Real-time control loop          │         │  - IoT uplink (future)             │
│                                    │         │                                    │
└────────────────────────────────────┘         └────────────────────────────────────┘
```

## Responsibilities

### OPTA (PLC)

- Owns all physical I/O: valves, pumps, sensors, relays.
- Runs the real-time control loop. **No blocking calls** (no slow network ops, no user interaction).
- Enforces safety interlocks in firmware (not delegated to HMI).
- Exposes state and accepts commands via Modbus TCP registers.

### GIGA (HMI)

- Renders the operator UI on the Display Shield via LVGL.
- Polls the OPTA over Modbus TCP for state; writes commands on user action.
- Owns the network stack (WiFi / Ethernet).
- Reserved for future: IoT uplink, companion-app connection.

## Why Modbus TCP

- Industrial standard — robust, well-documented, survives network blips.
- Native support in Arduino's `ArduinoModbus` library on both OPTA and GIGA.
- Clean separation: OPTA stays deterministic; GIGA stays responsive to the user.

See [[communication]] for the register map and [[io-map]] for physical I/O.

## Related

- [[io-map]] — physical OPTA I/O
- [[communication]] — Modbus TCP register map
- [[state-machine]] — product state machine
