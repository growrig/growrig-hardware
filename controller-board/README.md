# GrowRig Controller Board

*ESP32-based board for controlling a grow tent.*

**Status:** Concept

## Problem

A grow tent is a handful of dumb loads — exhaust fan, intake fan, light, humidifier,
heater, pump — plus a few sensors. Today growers wire up a pile of mains timers,
inline speed controllers and cheap Wi-Fi plugs, none of which talk to each other.
There's no single, safe, open board that a Hub can drive.

## Approach

One board that switches and modulates the loads in a tent and speaks to the rest
of the GrowRig stack. ESP32 for Wi-Fi/BLE/ESP-NOW and enough GPIO/PWM. Firmware
built on ESPHome-compatible YAML so it drops straight into Home Assistant / Grow
Core, with a native GrowRig device profile in the [catalog](https://github.com/growrig/growrig-catalog).

## Draft spec

- **MCU:** ESP32-S3 (Wi-Fi + BLE, USB-C native, room for local control loops)
- **AC switching:** 3–4 relay channels (light, humidifier, heater, aux), triac or
  relay; mains isolation is the hard part — see open questions
- **Fan control:** 2× 0–10 V **or** PWM outputs for EC fans; tach input for RPM
  feedback
- **Low-voltage:** 4–6× 12/24 V PWM channels (DC fans, pumps, solenoids)
- **Sensor I/O:** I²C + 1-Wire headers (temp/RH, soil, water temp), 2× analog in
- **Power:** single 24 V DC barrel/terminal in, on-board buck to 5 V/3.3 V
- **Local safety:** hardware watchdog, high-temp cutoff, fail-safe relay states on
  MCU crash / Wi-Fi loss
- **Comms:** Wi-Fi primary, ESP-NOW to sensors as fallback, MQTT to Hub

## Why it matters

It's the workhorse. Everything else in a tent hangs off this board, and being
open + ESPHome-native means people can trust it with mains and extend it.

## Open questions

- Mains on-board vs. keep the board low-voltage and switch AC via external SSR
  modules (safer, cheaper certification story)?
- Certification path (CE/UL) for a mains device sold as a kit vs. assembled.
- Screw terminals vs. connectors — field-wireable but idiot-proof.
- How much control logic lives on-board vs. in the Hub (offline resilience).

## Related

Driven by the [Hub](../hub/); pairs with [Canopy Sensor](../canopy-sensor/) and
[Circulation Fan](../circulation-fan/). Superseded, eventually, by the [Ark](../ark/).
