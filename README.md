# GrowRig Open Hardware (GROH)

Open hardware concepts for the GrowRig ecosystem — the physical devices that a
[GrowRig](https://github.com/growrig/growrig) installation runs on. This repo
holds **concepts, specs, and design notes**, one directory per project. Nothing
here is finished; it's where each device is thought through before it becomes a
schematic, an enclosure, or a product.

Everything is intended to be **open-source hardware**: open schematics, open
firmware, open enclosures, buildable from off-the-shelf parts wherever possible.

## Projects

| Project | Codename | What it is | Maturity |
|---|---|---|---|
| [Controller](controller/) | — | ESP32 board that runs a grow tent (fans, lights, plugs, sensors) | Concept |
| [Canopy Sensor](canopy-sensor/) | — | Wireless micro-climate probe for the canopy (temp, RH, light, CO₂) | Concept |
| [Hub](hub/) | — | Appliance computer running Grow Core + Home Assistant | Concept |
| [Gateway](gateway/) | — | Grow-focused router; isolates the grow network from the home LAN | Concept |
| [Circulation Fan](circulation-fan/) | — | Cheap, self-buildable canopy circulation fan | Concept |
| [Nutrient Mixer](nutrient-mixer/) | — | Weight-based precision dosing module | Concept |
| [Ark](ark/) | Ark | End-goal: fully automated growing appliance | Vision |

## How the pieces fit

```
                       ┌─────────────┐
        home LAN ──────┤   Gateway   ├────── grow LAN (isolated)
                       └──────┬──────┘
                              │
                        ┌─────┴──────┐
                        │    Hub     │  Grow Core + Home Assistant
                        └─────┬──────┘
                              │  (Wi-Fi / Matter / ESP-NOW / MQTT)
        ┌──────────────┬──────┴───────┬─────────────────┐
        │              │              │                 │
 ┌──────┴─────┐ ┌──────┴─────┐ ┌──────┴──────┐  ┌───────┴───────┐
 │ Controller │ │  Canopy    │ │ Circulation │  │   Nutrient    │
 │   Board    │ │  Sensor    │ │     Fan     │  │    Mixer      │
 └────────────┘ └────────────┘ └─────────────┘  └───────────────┘

        The Ark collapses this whole stack into one appliance.
```

## Per-project layout

Each directory starts as a single `README.md` concept doc and grows into:

```
<project>/
├── README.md        # concept: problem, approach, spec, open questions
├── hardware/        # schematics, PCB, BOM, enclosure (added later)
├── firmware/        # or a pointer to a firmware repo (added later)
└── docs/            # deeper design notes, decisions (added later)
```

## License

Design intent: open hardware. Firmware under a permissive/OSS license; hardware
designs under [CERN-OHL-S](https://ohwr.org/project/cernohl) or similar. See
[LICENSE](LICENSE).
