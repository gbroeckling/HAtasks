# window-opener

Motorized window opener integrated with Home Assistant as a `cover` entity (open / close / position).

## Goal

Drive a window via a geared shaft/actuator and control it from HA — open, close, and (if feasible) intermediate position, with safe end-stop handling.

## Decisions — TBD

These are open and will be filled in as we scope the build:

- **Control approach:** ESPHome device (likely) vs HA custom integration
- **Board:** e.g. ESP32 (which variant)
- **Actuator:** geared linear/rotary actuator on a splined shaft — exact model TBD
- **Driver:** motor driver / relay H-bridge, current/stall sensing for end stops
- **Position feedback:** none (timed) vs limit switches vs encoder/Hall
- **Power:** 12V/24V supply, fusing
- **HA exposure:** `cover` template/entity, device class `window`

## Status

🚧 Scaffolding only — repo and folder created. Hardware + approach to be decided next.
