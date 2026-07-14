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
  - Temp + RH + pressure: **[BME280](https://www.bosch-sensortec.com/products/environmental-sensors/humidity-sensors-bme280/)** (VPD; pressure is a bonus for DLI/vent logic).
    The SCD-41 also reports T/RH, so on the ideal variant the two cross-check each
    other — but the SCD-41 self-heats (its reading runs a few °C warm), so BME280 is
    the cleaner *ambient* source and the SCD-41's T/RH is the backup, not vice-versa.
  - Light: **[AS7343](https://ams-osram.com/products/sensor-solutions/ambient-light-color-spectral-proximity-sensors/ams-as7343-spectral-sensor)** — 14-channel spectral, enough to estimate **PAR and PPFD**
    and do basic spectral analysis, not just a lux proxy
  - CO₂: **[SCD-41](https://sensirion.com/products/catalog/SCD41)** (true photoacoustic NDIR, not eCO₂) on the mains-powered ideal build, or
    **[STCC4](https://sensirion.com/products/catalog/STCC4)** (thermal-conductivity CO₂, far lower average power) paired with an
    **[SHT-40](https://sensirion.com/products/catalog/SHT40)** for the battery-friendly CO₂ path — the SHT-40 supplies the accurate T/RH the
    STCC4 needs for humidity compensation, and doubles as a clean ±1.8 % ambient source in place of the BME280
- **Power:** LiPo with USB-C charging; target weeks per charge in duty-cycled mode.
  Optional 5 V always-on for the SCD-41 (NDIR/photoacoustic is power-hungry); the
  STCC4 path is the one that actually holds up CO₂ on battery.
- **Comms:** BLE / ESP-NOW / Thread depending on the MCU populated; reports to the
  Hub. No Wi-Fi creds needed on the low-power path.
- **Mount:** clip / magnet / hook for the tent pole or a plant stake; splash-resistant
- **Reporting:** temp, RH, VPD, PAR/PPFD/DLI, CO₂, battery — at an interval that
  trades battery vs. resolution

### Variants

| Variant | Base | Sensors | Notes |
|---|---|---|---|
| **No-CO₂ puck** | **[XIAO Logger Hat](https://www.seeedstudio.com/XIAO-LOG-p-6341.html)** + XIAO (nRF52840 / ESP32-C3 / S3) | BME280 + AS7343 | Logger Hat gives SD logging + battery/charge management + Grove I²C; cheap to scatter several |
| **Battery CO₂ puck** | XIAO nRF52840 or ESP32-C6 carrier | STCC4 + SHT-40 + AS7343 | Thermal-conductivity CO₂ at a fraction of the SCD-41's power, so real CO₂ survives on battery; SHT-40 replaces BME280 for tighter VPD. Trade-off: no self-heating T/RH cross-check |
| **Full / ideal** | XIAO nRF52840 or ESP32-C6 carrier | BME280 + SCD-41 + AS7343 | Adds true CO₂; higher power + cost, likely fewer per tent |

### CO₂ sensing options

The CO₂ choice is the one that drives power, cost and enclosure design, so it's worth
laying the candidates side by side. All figures are approximate — check the datasheets
before committing.

| Sensor | Principle | Selectivity | Accuracy (approx.) | Power | On-board T/RH | Size / cost |
|---|---|---|---|---|---|---|
| **[SCD-41](https://sensirion.com/products/catalog/SCD41)** | Photoacoustic NDIR | True CO₂, no VOC cross-talk | ±(50 ppm + 5 % of reading) | ~200 mA peak, tens of mA avg unless heavily duty-cycled | Yes, but self-heats (runs warm) | 10×10×6.5 mm, $$ |
| **[STCC4](https://sensirion.com/products/catalog/STCC4)** + **[SHT-40](https://sensirion.com/products/catalog/SHT40)** | Thermal conductivity | Physical CO₂, but sensitive to humidity/pressure/gas mix | Coarser; leans on SHT-40 compensation and periodic re-cal | Much lower avg — the reason it holds up on battery | Via the paired SHT-40 (±1.8 % RH, no self-heat) | Smaller combined footprint, $ |
| **MOX eCO₂** (e.g. [SGP41](https://sensirion.com/products/catalog/SGP41), CCS811) | Metal-oxide VOC proxy | *Not* real CO₂ — estimated from VOCs | Indicative trend only, not a ppm you can dose to | Low | No | Tiny, ¢ — but rejected here |

Takeaways:

- **SCD-41** is the accuracy/selectivity reference and the right call when 5 V is
  available. Its self-heating is why the BME280 stays the primary ambient T/RH source.
- **STCC4 + SHT-40** trades some CO₂ precision for the power budget that makes CO₂
  viable on a battery puck, and the mandatory SHT-40 gives cleaner VPD than the BME280.
  Thermal-conductivity readings drift with humidity and gas composition, so good
  compensation and baseline re-calibration matter more than with NDIR.
- **eCO₂ (MOX)** is listed only to be explicit about what's excluded: it infers CO₂
  from VOCs, so it can't give a trustworthy ppm for dosing decisions.

## Why it matters

Turns "the tent says 24 °C" into "the canopy is at 27 °C and 80 % RH in the back
corner." It's what makes closed-loop control actually track the plant.

## Open questions

- The variant split mostly answers "CO₂ on every unit?" — but is the no-CO₂ puck
  cheap/small enough to actually scatter several, or does the Logger Hat make it too bulky?
- **BME280 RH accuracy** (±3 %) is looser than SHT4x (±1.8 %) and drifts more.
  On the ideal variant the SCD-41's own T/RH gives a second reading to sanity-check
  against (once its self-heating offset is calibrated out) — is that redundancy
  enough, or is an SHT4x still worth it for clean canopy VPD? The battery CO₂ puck
  sidesteps this: the STCC4 already requires an SHT-40, so the accurate RH comes for free.
- nRF52840 (BLE/Thread, best battery) vs. ESP32-C6 (Wi-Fi + ESP-NOW, easier ecosystem)
  as the default MCU.
- Battery vs. permanently powered — most tents have 5 V nearby (matters most for SCD-41).
- Calibration & drift, especially CO₂ and translating AS7343 channels to PPFD across
  different light spectra (HPS vs. white vs. deep-red/far-red LED).
- Enclosure: airflow to the sensor + a clear window for the AS7343 without letting water/spray in.

## Related

Feeds the [Hub](../hub/) and closes the loop with the [Controller Board](../controller-board/).
