# GrowRig Hub

*Specialized appliance computer that runs Grow Core + Home Assistant.*

**Status:** Concept

## Problem

GrowRig's brain — Grow Core plus Home Assistant — has to run *somewhere*, always
on, close to the plants, without the grower spinning up a server. Running it on a
laptop or a random Pi image is fragile and off-putting to the mainstream grower
this ecosystem is for. It needs to be an appliance you plug in and forget, the way
Home Assistant Green / Yellow made HA itself an appliance.

## Approach

A dedicated small computer, likely RPi-based (CM5 or a Pi 5 class SBC), that ships
with a signed GrowRig OS image: Grow Core + Home Assistant + the local MQTT/Matter
stack, auto-updating, backed up, recoverable. Think "Home Assistant Yellow, but
purpose-built for growing."

## Draft spec

- **Compute:** Raspberry Pi CM5 (or Pi 5) — 4–8 GB RAM
- **Storage:** onboard NVMe/eMMC (SD cards die); slot for backup drive
- **Radios:** Wi-Fi, BLE, and a **Thread/Zigbee** radio for Matter + local devices;
  ESP-NOW bridge to sensors
- **Ports:** 2.5 GbE to the [Gateway](../gateway/), USB for cameras / dongles
- **Power:** single USB-C PD or barrel; low idle draw for 24/7
- **Software:** GrowRig OS image — Grow Core, Home Assistant, Mosquitto, Matter
  server; OTA updates; scheduled encrypted backups
- **Enclosure:** fanless, passively cooled, small and quiet enough to live in a
  living space

## Why it matters

This is what makes GrowRig a product instead of a project. One box, plug it in,
it finds the tent hardware and runs the grow. It's also the natural precursor to
folding everything into the [Ark](../ark/).

## Open questions

- CM5 carrier board (own design) vs. shipping on HA Yellow-style existing hardware.
- Bundle a Thread/Zigbee radio, or stay Matter-over-Wi-Fi and lean on the Gateway.
- How much of Home Assistant is exposed vs. hidden behind Grow Core's UI.
- Local-first guarantees: what still works with the internet down.

## Related

Runs the whole stack; sits behind the [Gateway](../gateway/) and drives the
[Controller Board](../controller-board/), [Canopy Sensor](../canopy-sensor/),
[Circulation Fan](../circulation-fan/) and [Nutrient Mixer](../nutrient-mixer/).
