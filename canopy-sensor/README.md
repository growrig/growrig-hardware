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

Built around the **Seeed XIAO** form factor so the same carrier works across
several MCUs and the sensor set can be populated by variant.

- **MCU (XIAO module, swappable):** prefer **[XIAO nRF52840](https://www.seeedstudio.com/Seeed-XIAO-BLE-nRF52840-p-5201.html)** (BLE, very low power,
  great for battery pucks) or **[XIAO ESP32-C6](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C6-p-5884.html)** (Wi-Fi 6 + BLE + 802.15.4/Thread,
  ESP-NOW). ESP32-C3/S3 also fit the socket for the no-CO₂ variant.
- **Sensors:**
  - Temp + RH + pressure: **BME280** (VPD; pressure is a bonus for DLI/vent logic)
  - Light: **AS7343** — 14-channel spectral, enough to estimate **PAR and PPFD**
    and do basic spectral analysis, not just a lux proxy
  - CO₂: **SCD-41** (true photoacoustic NDIR, not eCO₂) — ideal-version only
- **Power:** LiPo with USB-C charging; target weeks per charge in duty-cycled mode.
  Optional 5 V always-on for the SCD-41 (NDIR/photoacoustic is power-hungry).
- **Comms:** BLE / ESP-NOW / Thread depending on the MCU populated; reports to the
  Hub. No Wi-Fi creds needed on the low-power path.
- **Mount:** clip / magnet / hook for the tent pole or a plant stake; splash-resistant
- **Reporting:** temp, RH, VPD, PAR/PPFD/DLI, CO₂, battery — at an interval that
  trades battery vs. resolution

### Variants

| Variant | Base | Sensors | Notes |
|---|---|---|---|
| **No-CO₂ puck** | **[XIAO Logger Hat](https://www.seeedstudio.com/XIAO-LOG-p-6341.html)** + XIAO (nRF52840 / ESP32-C3 / S3) | BME280 + AS7343 | Logger Hat gives SD logging + battery/charge management + Grove I²C; cheap to scatter several |
| **Full / ideal** | XIAO nRF52840 or ESP32-C6 carrier | BME280 + SCD-41 + AS7343 | Adds true CO₂; higher power + cost, likely fewer per tent |

## Why it matters

Turns "the tent says 24 °C" into "the canopy is at 27 °C and 80 % RH in the back
corner." It's what makes closed-loop control actually track the plant.

## Open questions

- The variant split mostly answers "CO₂ on every unit?" — but is the no-CO₂ puck
  cheap/small enough to actually scatter several, or does the Logger Hat make it too bulky?
- **BME280 RH accuracy** (±3 %) is looser than SHT4x (±1.8 %) and drifts more —
  acceptable for VPD, or worth an SHT4x on the ideal variant?
- nRF52840 (BLE/Thread, best battery) vs. ESP32-C6 (Wi-Fi + ESP-NOW, easier ecosystem)
  as the default MCU.
- Battery vs. permanently powered — most tents have 5 V nearby (matters most for SCD-41).
- Calibration & drift, especially CO₂ and translating AS7343 channels to PPFD across
  different light spectra (HPS vs. white vs. deep-red/far-red LED).
- Enclosure: airflow to the sensor + a clear window for the AS7343 without letting water/spray in.

## Related

Feeds the [Hub](../hub/) and closes the loop with the [Controller Board](../controller-board/).
