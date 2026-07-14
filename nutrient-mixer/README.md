# GrowRig Nutrient Mixer

*Inexpensive weight-based precision dosing module.*

**Status:** Concept

## Problem

Feeding is where home growers make the most mistakes: eyeballing mL of A/B/Cal-Mag
into a reservoir, chasing pH/EC by hand. Commercial dosers are peristaltic,
expensive, and drift as tubing wears. There's room for a cheap module that doses
**by weight** — the one measurement that doesn't lie.

## Approach

A dosing module that sits on (or feeds) the reservoir and doses each nutrient by
measuring mass on a load cell rather than trusting pump volume. Cheap pumps become
precise because the scale is the source of truth: dose until Δmass is hit. Driven
by the Hub with target EC/pH per crop stage from Grow Core.

## Draft spec

- **Metrology:** load cell(s) + HX711/NAU7802 24-bit ADC; dose to a target grams-delta
- **Pumps:** low-cost diaphragm or peristaltic pumps, 3–6 channels (A, B, Cal-Mag,
  pH down, …); accuracy comes from the scale, not the pump. Prefer **pinch valves**
  where possible so nutrient only ever touches replaceable tubing, not the mechanism.
- **Feedback:** inline EC + pH probes to close the loop and verify the dose
- **MCU:** ESP32 (Wi-Fi/ESP-NOW), local dosing routine so it's safe if the Hub is away
- **Mixing:** small circulation/stir pump so readings settle before the next dose
- **Safety:** hard per-dose and per-day caps; leak/overflow detection; empty-bottle
  detection via flow-vs-mass mismatch
- **Recipes:** targets pulled from Grow Core per species/stage

### Start simple: water first, feed later

The lowest-maintenance path is to **automate plain water and feed the soil manually**:
fertilized/slow-release soil plus an occasional measured top-dress, with only plain
water in the automated reservoir. Liquid fertigation is the higher-maintenance mode —
mixed nutrients can precipitate and clog drip tubing, and the reservoir then needs
monitoring and periodic flushing. So the first useful thing GrowRig can do here isn't
dosing at all; it's tracking the manual feed and reminding you when the next one is due:

```
Product | Dose per pot | Stage | Repeat interval | Last feeding | Next feeding | Notes
```

Full auto-dosing is the next step up, not the starting point.

### Dosing architectures

Two ways to build the doser; both keep the **load cell as the source of truth** —
pressure or pump-time only stabilize flow, they never measure volume directly
(viscosity, temperature, tube/nozzle size and liquid level all move the flow rate).

- **A — Cheap pump + weight (default):** a low-cost pump per channel dispensing into a
  mixing vessel on a load cell; dose until Δmass is hit. Simplest, fewest parts.
- **B — Time-pressure + weight:** one shared **air pump → pressure sensor + mechanical
  relief valve → small accumulator** pressurizes a *sealed* nutrient cartridge; a
  normally-closed pinch valve dispenses into the mixing vessel while the load cell
  watches Δmass, then the cartridge vents back to atmosphere. The air pump, sensor,
  accumulator and relief valve are shared across all cartridges; each cartridge only
  needs a sealed cap, outlet tube, an air-selection/check valve and its pinch valve.
  More valves = more cost/complexity than option A, but it shines later with
  purpose-built sealed GrowRig cartridges instead of original bottles.
  **Always include a mechanical relief valve** — FDM-printed caps and stock bottles
  aren't pressure-rated — and keep nutrient liquid away from the pressure sensor
  (dry air only).

  Pressure-sensor options, cheapest-prototype to best-fit:
  - **QMP6988** — usable but it's an *absolute* barometric sensor with no hose barb;
    put it in a small sealed dry-air chamber, baseline atmospheric pressure after
    venting, and read gauge = current − baseline. Good relative resolution, worse
    absolute accuracy — fine for baseline-subtracted low-pressure work (~1–3 kPa).
  - **MPX5010** — 0–10 kPa, matched to gentle cartridge pressure.
  - **HX710B + MPS20N0040D** — ~0–40 kPa *gauge* module with a hose port; the natural
    fit. Caveats: clone chips/pin variance between batches, HX711-like (not I²C)
    protocol, and its data line may not be 3.3 V-safe — level-shift for the ESP32
    until verified, and zero-calibrate with the manifold vented.

## Why it matters

Turns "feeding" from a nervous manual chore into a number, and does it with cheap
parts by leaning on weight instead of pricey metering hardware.

## Open questions

- Weigh the **bottles** (dose = source bottle loses X g) vs. weigh the **reservoir**
  vs. weigh a dedicated **mixing vessel** each dose lands in.
- Peristaltic vs. diaphragm pumps for cost/accuracy/priming — and whether the
  time-pressure architecture (option B) is worth its extra valves for a home rig, or
  only once sealed cartridges exist.
- Pressure-sensor choice if going time-pressure: QMP6988 vs. MPX5010 vs. HX710B module.
- pH via dosing acid vs. leaving pH manual at first (chemistry + safety).
- Food-contact materials and cleaning/flush cycles.
- Calibration UX for load cells and probes.

## Related

Driven by the [Hub](../hub/) / Grow Core recipes; a subsystem the [Ark](../ark/) absorbs.
