# Anycubic Kobra Max with BTT SKR3 EZ motherboard running Klipper
This repository documents my conversion of an original Anycubic Kobra Max to a BIGTREETECH SKR 3 EZ running Klipper.
The build retains most of the original printer hardware, including the Bowden extruder, motors, fans, external bed MOSFET and LeviQ strain-gauge probe.

> [!WARNING]
> This repository is very much a work in progress!
> The wiring and configuration have been tested on **my** Anycubic Kobra Max and SKR 3 EZ.  
> Anycubic used multiple board and harness revisions, so verify all wiring, voltages, polarities and motor coil pairs on your own machine.
> Do not blindly copy configuration values. Incorrect wiring or configuration can damage hardware.

## Project overview

| Component | Current setup |
|---|---|
| Printer | Original Anycubic Kobra Max |
| Mainboard | BIGTREETECH SKR 3 EZ |
| MCU | STM32H723 |
| Stepper drivers | 4 × BTT EZ2209 v1.1 using UART |
| Host | Raspberry Pi Zero 2 W |
| Interface | Mainsail and Moonraker |
| Firmware | Klipper |
| Extruder | Original Bowden extruder |
| Hotend | Original-style replacement hotend |
| Z probe | Original LeviQ strain gauge |
| Heated bed | Original bed and external MOSFET |
| Adaptive mesh | KAMP |

## What is documented

- Verified Anycubic-to-SKR wiring
- Original LeviQ probe output, reset and LED signals
- Logic-level conversion for the 5 V toolhead signals
- Stepper motor coil mapping
- Dual-Z wiring and synchronization issues
- External bed MOSFET wiring
- Split Klipper configuration files
- KAMP adaptive meshing and line purge
- Problems encountered during the conversion

## Documentation

| Document | Description |
|---|---|
| [Wiring](./docs/Wiring.md) | Verified connector mapping, voltages, motor coils and functional tests |
| [Configuration](./docs/config/config.md) | Purpose of each cfg file and values that must be changed or calibrated |
| [Issues](./docs/Issues.md) | Symptoms, causes and fixes encountered during the conversion |
| [Useful links](./Useful_links.md) | External projects, guides and discussions used during the build |
| [STL files](./docs/stl/) | Modified mounting and printhead parts used in this build |

## Verification status

| Function | Status | Notes |
|---|---|---|
| X/Y/Z motion | ✅ Verified | Original motors |
| Extruder motion | ✅ Verified | Original Bowden extruder |
| X/Y endstops | ✅ Verified | Original two-wire endstops |
| Original LeviQ | ✅ Verified | PB15 probe output and PB14 reset |
| Z homing | ✅ Verified | LeviQ used as virtual Z endstop |
| Hotend and thermistor | ✅ Verified | Original-style replacement hotend |
| Bed heater and MOSFET | ✅ Verified | Original external MOSFET retained |
| Original fans | ✅ Verified | Part-cooling and hotend fans |
| Toolhead LED | ✅ Verified | Controlled through PE5 |
| KAMP adaptive mesh | ✅ Verified | Adaptive mesh and line purge |
| Pressure Advance | ✅ Verified | Current value remains machine- and filament-specific |
| Input Shaper | ⏳ Pending | USB accelerometer setup not yet completed |
| Filament runout sensor | ⏳ Pending | Wiring mapped but not configured |

---

## Modifications

<details>
<summary><strong>Mainboard and chassis</strong></summary>

The original motherboard was replaced with a BTT SKR 3 EZ using four EZ2209 UART stepper drivers.

The board is mounted using a modified version of [this SKR 3 adapter](https://www.thingiverse.com/thing:6247028). My version is available here:

- [Kobra Max SKR 3 adapter plate](./docs/stl/Kobra%20Max%20SKR3%20Adapter%20plate.stl)

The modified plate moves the mounting holes and allows ordinary 3 mm screws instead of heat-set inserts.

The chassis was cut to provide access to the SKR microSD slot and USB-B connector. The exposed metal edges were covered after cutting.

</details>

<details>
<summary><strong>Wiring</strong></summary>

The original harnesses were mapped, depinned, re-crimped and moved into connector housings compatible with the SKR 3 EZ.

Most of the original wires are black, so every conductor was labelled before the original connectors were removed.

The complete verified mapping is documented in [Wiring.md](./docs/Wiring.md).

</details>

<details>
<summary><strong>Printhead and hotend</strong></summary>

The original printhead cover broke and was replaced with a heavily modified version of [this model](https://www.thingiverse.com/thing:5380573).

The modified files are available in [`docs/stl`](./docs/stl/).

The replacement cover required additional clearance around the LeviQ load cell. Insufficient clearance caused false probe triggers.

The original hotend was damaged and replaced with a similar original-style hotend.

</details>

<details>
<summary><strong>Display</strong></summary>

The original Anycubic display is not used in this build. It is proprietary and is not directly supported by the current Klipper configuration.

Mainsail is used as the primary interface.

</details>

## Current work

### Tuning

- Flow calibration
- Temperature calibration
- Input Shaper
- Maximum and quality-focused print speeds

### Accessories

- Filament runout sensor
- USB accelerometer
- Optional KlipperScreen display
- Raspberry Pi and USB-hub enclosures

### Possible future upgrades

- Quieter power-supply fan
- Nozzle cleaner
- Direct drive
- Different hotend or printhead
- PEI build surface
- Chassis LED strip
- Replacement X/Y/Z motors
- Home Assistant integration
- Eddy-current probe

## How-to guide

A full start-to-finish installation guide is not available yet.

For now, the repository should be treated as:

1. A verified wiring reference
2. A working configuration snapshot
3. A record of problems and solutions from the conversion

> [!CAUTION]
> Always confirm your own board revision, MCU model and wiring before following this repository.
