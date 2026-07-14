# GrowRig Canopy Sensor

*Wireless sensor for monitoring the canopy micro-climate: temperature, humidity,
light, CO₂.*

**Status:** Concept

## Problem

The number that matters for a plant is the climate **at the canopy**, not at the
tent wall where a controller happens to sit. VPD, DLI and CO₂ all vary by tens of
percent between the intake corner and the middle of the canopy. Growers need a
small, cheap probe they can clip anywhere in the tent — and a few of them — without
running wires.

## Approach

A battery or low-voltage puck that sits in the canopy and reports the four numbers
that drive a grow. Wireless-first so you can scatter several. Reports to the Hub
and computes VPD/DLI locally or upstream.

## Draft spec

- **Sensors:**
  - Temp + RH: SHT4x (VPD)
  - Light: AS7341 (spectral) or a simple PAR/lux sensor → DLI
  - CO₂: SCD4x (true NDIR, not eCO₂)
- **MCU:** ESP32-C3 (ESP-NOW + Wi-Fi + BLE) — low power, cheap
- **Power:** 18650 or LiPo with USB-C charging; target weeks per charge in
  duty-cycled mode. Optional 5 V always-on for CO₂ (NDIR is power-hungry).
- **Comms:** ESP-NOW to Controller Board / Hub by default (no Wi-Fi creds, mesh-y,
  low power); Wi-Fi/Matter as an option
- **Mount:** clip / magnet / hook for the tent pole or a plant stake; splash-resistant
- **Reporting:** temp, RH, VPD, PAR/DLI, CO₂, battery — at an interval that trades
  battery vs. resolution

## Why it matters

Turns "the tent says 24 °C" into "the canopy is at 27 °C and 80 % RH in the back
corner." It's what makes closed-loop control actually track the plant.

## Open questions

- CO₂ on every unit (cost + power) vs. a separate "CO₂ node" and cheap temp/RH/light pucks.
- Battery vs. permanently powered — most tents have 5 V nearby.
- Calibration & drift, especially CO₂ and the light sensor.
- Enclosure: airflow to the sensor without letting water/spray in.

## Related

Feeds the [Hub](../hub/) and closes the loop with the [Controller Board](../controller-board/).
