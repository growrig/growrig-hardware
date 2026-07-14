# GrowRig Ark

> Codename: **Ark**

*The end-goal device: a fully automated growing box with a beautiful, minimalist
design — an appliance that turns small-scale home growing into something
mainstream.*

**Status:** Vision

## The idea

Everything else in this repo is a building block. The Ark is the destination: a
single, self-contained appliance that grows a plant start-to-finish with almost no
input from the owner. You choose what to grow, it handles climate, light, airflow,
water and feeding — and it looks like something you'd actually put in your kitchen
or living room, not a tent in a closet.

The bet: home growing goes mainstream the same way espresso, sous-vide and
bread-making did — when the gear stops looking like a hobbyist rig and becomes a
clean appliance with a great default experience.

## Design principles

- **Appliance, not equipment.** Minimalist, quiet, beautiful. Fits a home, not a
  grow room. Hidden complexity.
- **Truly automated.** Closed-loop climate, light, airflow and feeding out of the
  box. The owner picks a crop and gets guidance, not a control panel.
- **One integrated system.** The Ark internalizes the whole stack — Controller
  Board, Canopy Sensor, Circulation Fan, Nutrient Mixer, Hub — as subsystems, not
  boxes you wire together.
- **Open underneath.** Sealed and simple on the outside; open designs, firmware and
  Grow Core underneath. Repairable, hackable, not locked down.
- **Approachable.** Success on the first grow for someone who has never grown
  anything.

## What it absorbs

| Subsystem | Becomes |
|---|---|
| [Controller Board](../controller-board/) | Internal control + power board |
| [Canopy Sensor](../canopy-sensor/) | Built-in canopy sensing array |
| [Circulation Fan](../circulation-fan/) | Integrated airflow system |
| [Nutrient Mixer](../nutrient-mixer/) | Integrated reservoir + weight-based dosing |
| [Hub](../hub/) | Embedded compute running Grow Core |
| [Gateway](../gateway/) | On-device network isolation / connectivity |

## Rough shape (very early)

- Enclosed, insulated, light-sealed grow chamber with a real door/window
- Integrated LED with a proper spectrum + dimming
- Sealed climate loop: exhaust/intake or recirculating with dehumidify/heat
- Reservoir + weight-based dosing built in
- Camera + canopy sensors for monitoring and guidance
- Quiet, thermally sane for a living space
- Simple UI: pick crop → guided grow; deep control available but hidden by default

## Why it matters

It's the reason the other six projects exist. Each one is worth building on its
own, but the Ark is what makes GrowRig a category, not a kit.

## Open questions

- Form factor & size: countertop vs. furniture-scale.
- Recirculating vs. vented climate (odor, energy, water).
- Modular subsystems vs. a single integrated build.
- How much is one product vs. a small family (sizes/crops).
- The long path from these concepts to a manufacturable appliance.

## Related

The convergence point for every project in [this repo](../README.md).
