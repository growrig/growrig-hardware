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
  pH down, …); accuracy comes from the scale, not the pump
- **Feedback:** inline EC + pH probes to close the loop and verify the dose
- **MCU:** ESP32 (Wi-Fi/ESP-NOW), local dosing routine so it's safe if the Hub is away
- **Mixing:** small circulation/stir pump so readings settle before the next dose
- **Safety:** hard per-dose and per-day caps; leak/overflow detection; empty-bottle
  detection via flow-vs-mass mismatch
- **Recipes:** targets pulled from Grow Core per species/stage

## Why it matters

Turns "feeding" from a nervous manual chore into a number, and does it with cheap
parts by leaning on weight instead of pricey metering hardware.

## Open questions

- Weigh the **bottles** (dose = source bottle loses X g) vs. weigh the **reservoir**.
- Peristaltic vs. diaphragm pumps for cost/accuracy/priming.
- pH via dosing acid vs. leaving pH manual at first (chemistry + safety).
- Food-contact materials and cleaning/flush cycles.
- Calibration UX for load cells and probes.

## Related

Driven by the [Hub](../hub/) / Grow Core recipes; a subsystem the [Ark](../ark/) absorbs.
