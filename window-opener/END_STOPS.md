# End-stop protection (REQUIRED before unattended use)

## The problem

The Tuya/Zigbee 2-channel controller is just two dumb relays — **no position feedback, no
current limit**. Full window travel is **~3 minutes**. The only thing stopping the motor is the
HA `delay`; if that's interrupted mid-travel (HA restart, Z2M drop) the relay stays latched and
the motor drives into the limit → burned motor / stripped gearing. Software timing is not a safe
end stop.

Mechanical limit switches were ruled out as too hard to fit. **Chosen direction: current-based
stall cutoff + a hard max-runtime timer.**

## Why it needs a little smarts (not just a dumb cutoff)

A DC motor's **inrush** at power-on briefly looks like a **stall** (both are high current). A naive
"cut when amps > X" circuit either nuisance-trips on every start, or is set so high it never
protects. So the cutoff must **blank the first ~1 s**, then trip when current stays in the stall
band. A hard **max-duration timer** (~3:30, just over the 3-min travel) is the outer failsafe.

## Recommended: ESP32 + ESPHome `current_based` cover

One small board does stall-cutoff + max-duration + obstacle detection, and gives HA a real cover
with position — replacing the Tuya module.

| Part | Purpose |
|------|---------|
| ESP32 | brain, runs ESPHome |
| Motor driver — BTS7960 (beefy, has current-sense) or DRV8871 | reversing H-bridge, sized to **stall** current |
| Current sensor — INA219 (I²C high-side) or ACS712 (ADC) | the cutoff signal |
| 12V→5V buck | powers the ESP |

ESPHome config sketch: `current_based` cover with `open_duration`/`close_duration` ≈ measured
travel, `max_duration: 3.5min`, current sensors for endstop, obstacle thresholds tuned above
moving-current but below stall, and a start-up current-ignore window. (Config to be written once
the driver + sensor are picked and stall current is measured.)

## Alternative: dumb overcurrent module + time-delay relay (no ESP)

Off-the-shelf DC overload module + a delay-off relay. Cheapest in parts, but: tricky to tune
around inrush, no HA feedback, no obstacle detection, and the Tuya relay can stay latched after
the cutoff opens (needs re-arm handling). Not recommended.

## Interim rule (until cutoff is fitted)

Every open/close is **supervised**, and `input_number.windowsopenerbedroom1_travel` is set
**shorter** than measured full travel so it always stops before the limit.
