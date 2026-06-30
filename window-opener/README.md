# window-opener

Motorized window opener integrated with Home Assistant as a `cover` entity (open / close).

## Goal

Drive a window via a worm-geared motor coupled to the window's splined drive shaft, controlled from HA — open / close, with safe end-stop handling. Worm gear is self-locking, so the window holds position with no power applied.

## Bill of materials (ordered 2026-06-30)

| Part | Role | Specs / choice | Link |
|------|------|----------------|------|
| JGY370-style worm geared DC motor (reversible CW/CCW, "window/door opener") | Drive | DC 12V, **5 RPM** (slow, high torque), worm gear = **self-locking** | [AliExpress](https://www.aliexpress.com/item/1005009615399277.html) |
| Tuya / Zigbee 2-channel motor controller (RF433 + Zigbee) | Driver + HA bridge | 7–32V (12/24V), 2 relays = forward/reverse, **requires Zigbee hub** | [AliExpress](https://www.aliexpress.com/item/1005009435022266.html) |
| GTRIC D25L35 flexible jaw/spider coupling (aluminum) | Motor shaft → window shaft | bore 5–14mm (fits **6–12mm splined shaft**), 12 N·m max | [AliExpress](https://www.aliexpress.com/item/1005006031199563.html) |

Still needed: 12V DC power supply (sized to motor stall current), mounting bracket, wiring, and end-stop hardware (see open items).

## Architecture

```
12V PSU ──► Tuya/Zigbee 2-ch controller ──► worm geared motor (5 RPM) ──► D25L35 coupling ──► window splined shaft (6–12mm)
                       │ Zigbee
                       ▼
              Z2M (SLZB-06 @ 192.168.1.180) ──► Home Assistant `cover`
```

Single reversible motor wired across the two channels: CH1 = open polarity, CH2 = close polarity (interlocked). No ESP board — control is Zigbee via the existing Z2M coordinator.

**HA entity:** `windowsopenerbedroom1`, area **Bedroom** (naming scheme implies multiple units — bedroom1, bedroom2, …).

## Open items / decisions

- **End stops** — BOM is open-loop (no limit switch/encoder). Worm gear *holds* position but won't stop a stall at the travel limits. Pick one: inline **limit switches**, controller **travel-time calibration**, or **stall-current** cutoff. Affects wiring — decide before mounting.
- **HA representation** — confirm how the controller pairs in Z2M: native **cover** (with calibration) vs **two switches** (then wrap in a `cover` template + travel time). Verify exact model after pairing.
- **Power** — confirm 12V PSU current headroom for motor stall.
- **Mechanical** — confirm coupling bore vs the 6–12mm shaft; mounting/alignment for the flexible coupling.

## Status

🛒 Parts ordered (2026-06-30), delivery ~Jul 8–15. Approach decided: Zigbee/Tuya → Z2M → HA cover. Build pending parts arrival.
