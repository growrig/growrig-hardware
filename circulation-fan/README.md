# GrowRig Circulation Fan

*Cheap circulation fan for your canopy that you can build yourself.*

**Status:** Concept

## Problem

Canopy air movement prevents hot/humid microclimates, strengthens stems and cuts
mold risk — but "clip fans" are flimsy, loud, dumb, and die in a season. There's
no cheap, controllable, DIY-friendly circulation fan that the rest of the stack can
actually speak to.

## Approach

A self-buildable fan around a standard PC-style 120/140 mm EC/PWM fan: a printed
mount and shroud, a tiny driver, and PWM speed control from the
[Controller Board](../controller-board/) — or a standalone ESP node. Silent,
cheap, repairable, and it participates in the grow (ramp with VPD, gust cycles,
night quiet mode).

## Draft spec

- **Fan:** off-the-shelf 120–140 mm 4-pin PWM fan (24 V or 12 V), quiet high-static
  variant
- **Mount:** printable clamp/gooseneck for tent poles; printable shroud to focus flow
- **Control:**
  - **Simple:** PWM + tach passthrough to a Controller Board channel
  - **Smart:** small ESP32-C3 driver node (ESP-NOW/Wi-Fi) for standalone speed curves
- **Power:** shares the 24 V grow bus or a USB-C/12 V brick
- **Behaviors:** VPD-linked ramp, gust/oscillation cycles, quiet night mode, stall
  detection via tach

## Why it matters

The cheapest, most approachable "I built a GrowRig device" on-ramp — a printed
mount and a PC fan — while still being a first-class, controllable node.

## Open questions

- Fixed direction vs. printed oscillation mechanism (more parts, more failure).
- Single fan vs. a small array on one driver.
- 12 V vs. 24 V to match the Controller Board bus.
- How much lives in a shared "ESP driver node" reused by other DIY devices.

## Related

Driven by the [Controller Board](../controller-board/); reacts to the
[Canopy Sensor](../canopy-sensor/).
