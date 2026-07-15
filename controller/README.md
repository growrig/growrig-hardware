# GrowRig Controller

*Open, modular controller for monitoring and automating controlled-environment grows.*

**Status:** Concept

## Problem

A controlled grow contains many independent devices — fans, lights, pumps, valves, humidifiers, heaters and sensors. Growers often connect them through separate timers, speed controllers and Wi-Fi plugs that cannot coordinate with each other or continue safely when the network or Hub is unavailable.

GrowRig needs one open controller that can connect these devices, execute essential control locally and integrate with the wider GrowRig stack.

## Approach

The GrowRig Controller separates connectivity from physical control:

- **XIAO ESP32-C5** handles Wi-Fi, Bluetooth LE, 802.15.4, provisioning, MQTT and communication with the Hub.
- **RP2350** owns the physical inputs and outputs, deterministic timing, local automation and safety behaviour.
- The two processors communicate over SPI or UART with a heartbeat and explicit fault handling.
- The RP2350 continues operating safely while the XIAO reconnects, reboots or receives an update.

The controller is a low-voltage device. Mains equipment is switched through external certified relays, contactors, SSRs or compatible smart modules.

## Architecture

```text
                    GrowRig Hub
                         │
            Wi-Fi / MQTT / local API
                         │
              ┌──────────▼──────────┐
              │ XIAO ESP32-C5       │
              │ connectivity        │
              │ provisioning + OTA  │
              └──────────┬──────────┘
                         │ SPI/UART
                         │ heartbeat
              ┌──────────▼──────────┐
              │ RP2350              │
              │ real-time control   │
              │ local safety        │
              │ PWM / ADC / tach    │
              └──────────┬──────────┘
                         │
       fans / pumps / valves / sensors / external relays
```

## Draft specification

### Compute

- **Connectivity module:** socketed or surface-mounted [Seeed Studio XIAO ESP32-C5](https://wiki.seeedstudio.com/xiao_esp32c5_getting_started/)
  - Dual-band 2.4/5 GHz Wi-Fi
  - Bluetooth LE
  - 802.15.4 for Thread/Zigbee
  - External antenna connection
  - USB-C service and firmware port
- **Control processor:** [RP2350](https://www.raspberrypi.com/products/rp2350/)
  - Owns all actuator and sensor I/O
  - Generates PWM and measures fan tachometer signals
  - Runs local control loops and safety rules
  - Remains operational independently of the network processor
- **Inter-processor link:** SPI or UART with CRC, sequence numbers, heartbeat, interrupt and reset lines

### Power

- **Primary input:** USB-C Power Delivery
  - Target profile: 20 V / 3 A, up to 60 W
  - Standalone PD negotiation so the board does not depend on MCU firmware to receive power
- **Optional input:** 12–24 V DC terminal or locking connector
- **Optional backup battery:** protected single-cell 3.7 V LiPo
- Protected input stage with fuse, reverse-polarity protection, transient suppression and current limiting
- On-board conversion to 12 V, 5 V and 3.3 V rails
- Dedicated charger and power-path controller for seamless switching between external power and the LiPo
- Battery voltage, charging state and external-power state available to the control firmware
- Actuator outputs remain disabled until the input voltage and power budget are valid
- Higher-power loads use a separate GrowRig Power Module or their own power supply

### Backup battery

The optional 3.7 V LiPo is intended to keep the controller electronics alive during a power outage. It is not intended to power grow lights, heaters, pumps or normal 12/24 V actuator loads.

When external power fails, the controller should:

1. Immediately disable high-power outputs.
2. Record the outage and the previous output state.
3. Continue reading essential low-power sensors.
4. Notify the Hub while the local network remains available.
5. Continue local fault monitoring and maintain the real-time clock.
6. Enter a reduced-power mode if the outage continues.
7. Shut down cleanly before the battery reaches its safe discharge limit.

The backup supply should use a dedicated power-path and charging circuit rather than relying on the XIAO battery charger to power the complete controller. A buck-boost regulator should maintain the logic rail as the LiPo varies from its charged voltage down toward its discharge limit.

An optional low-power emergency output could remain available for a small alarm, communication device or ventilation override, but this must have a separately defined power budget. Full actuator backup should be provided by an external UPS or a separate GrowRig Power Module.

The battery should be replaceable and use:

- a protected LiPo cell or on-board battery protection;
- temperature-aware charging where practical;
- a keyed connector;
- a physical location away from heat-producing power components;
- configurable minimum battery voltage and shutdown thresholds.

### Outputs

Initial target:

- 2× four-pin fan outputs with PWM control and tachometer feedback
- 2× 0–10 V or open-drain PWM control outputs for compatible EC fans and lights
- 4–6× protected 12/24 V outputs for DC fans, pumps, valves and solenoids
- 3–4× low-voltage control outputs for external relays, SSRs or contactors
- Per-channel current or fault feedback where practical
- Hardware pull-downs so dangerous outputs remain off during startup and reset

### Inputs and sensors

- I²C headers for local environmental sensors and displays
- 1-Wire bus for temperature sensors
- Analog inputs for soil, pressure, level and other sensors
- Pulse/frequency inputs for flow meters and fan tachometers
- Digital inputs for float switches, leak detectors and door sensors
- CAN expansion port for remote GrowRig sensor and actuator modules
- Optional RS-485 support for Modbus devices

External sensor buses should use protected connectors. Long cables should use CAN or RS-485 rather than I²C.

### Display and local interface

Optional header for:

- small I²C OLED;
- low-power e-ink display;
- status LEDs;
- buzzer;
- buttons or rotary encoder.

The local interface can show environmental values, output states, faults and Hub/network status without opening the application.

### Safety and offline operation

- RP2350 remains responsible for outputs even if the XIAO or Hub is unavailable
- Independent hardware watchdog
- Global hardware `OUTPUT_ENABLE` line
- Configurable high-temperature and water-leak shutdown inputs
- Safe default state for every output
- Communication timeout behaviour defined per channel
- Pumps, heaters and lights do not automatically resume after an unexpected power loss unless explicitly configured
- Exhaust and circulation fans can use configurable emergency fallback speeds
- Fault and reset causes stored for later diagnostics

## Firmware model

The controller has two cooperating firmware layers:

### Connectivity firmware

Runs on the XIAO ESP32-C5 and provides:

- provisioning;
- Wi-Fi and local network communication;
- MQTT and GrowRig protocol support;
- Hub discovery;
- configuration transfer;
- telemetry forwarding;
- OTA updates;
- optional Home Assistant and ESPHome integration.

### Control firmware

Runs on the RP2350 and provides:

- input and output drivers;
- local schedules and control loops;
- safety limits;
- fail-safe behaviour;
- watchdog supervision;
- persistent configuration;
- a versioned command and telemetry protocol.

The Hub may coordinate the complete grow, but essential safety and environmental control must continue locally.

## Design boundaries

The GrowRig Controller is not intended to run Linux, databases, camera processing or AI workloads. Those belong on the GrowRig Hub or another application-compute device.

The controller should remain small, deterministic and recoverable. Adding more application compute must not make basic fan, pump or safety operation dependent on Linux.

## Why it matters

The GrowRig Controller is the dependable physical control layer of a GrowRig installation. It combines the accessibility of a replaceable XIAO module with a dedicated real-time processor, local fail-safe operation and an open expansion model.

It can be assembled as a DIY controller while also serving as the basis for optimized GrowRig hardware revisions.

## Open questions

- Exact number and current rating of output channels
- RP2350 package and whether external flash is required
- SPI versus UART for the inter-processor protocol
- CAN only versus CAN plus isolated RS-485
- USB-C PD only versus USB-C PD plus a locking DC input
- LiPo capacity, connector and whether the battery is standard or optional
- Whether any emergency output should remain powered from the backup battery
- Socketed XIAO for all versions versus surface-mounted XIAO in production
- Connector family for sensors and actuators
- Whether RTC and FRAM should be standard or optional
- Final enclosure, cable retention and environmental protection
- Certification requirements for assembled products and kits

## Related

Driven by the [Hub](../hub/); pairs with [Canopy Sensor](../canopy-sensor/) and [Circulation Fan](../circulation-fan/). Superseded, eventually, by the [Ark](../ark/).