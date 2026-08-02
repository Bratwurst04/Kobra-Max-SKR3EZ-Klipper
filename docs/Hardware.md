# Hardware

This document describes the hardware used in this Anycubic Kobra Max conversion.

It separates:

- Original printer hardware that was retained
- Replacement hardware added during the conversion
- Parts that were modified
- Hardware that is planned but not yet fully integrated

> [!WARNING]
> This is a record of one specific Anycubic Kobra Max.
>
> Anycubic used multiple board, harness and toolhead revisions. Confirm the hardware installed in your own machine before using this repository as a reference.

## Hardware summary

| Category | Hardware | Status |
|---|---|---|
| Printer | Original Anycubic Kobra Max | ✅ |
| Original mainboard | Trigorilla Pro A V1.0.4 | ❌ Replaced |
| Replacement mainboard | BIGTREETECH SKR 3 EZ v1.0 | ✅ |
| Mainboard MCU | STM32H723 | ✅ |
| Stepper drivers | 4 × BTT EZ2209 v1.1 | ✅ |
| Host computer | Raspberry Pi Zero 2 W | ✅ |
| Toolhead probe | Original Anycubic LeviQ strain gauge | ✅ |
| Logic conversion | 4-channel bidirectional 3.3 V ↔ 5 V level shifter | ✅ |
| Extruder | Original Bowden extruder | ✅ |
| Hotend | Original-style replacement hotend | ✅ |
| Heated bed | Original Anycubic heated bed | ✅ |
| Bed power stage | Original external bed MOSFET | ✅ |
| X/Y/Z motors | Original Anycubic stepper motors | ✅ |
| Fans | Original toolhead fans | ✅ |
| Display | Original Anycubic display | ❌ |
| Accelerometer | Mellow FLY ADXL345 USB board | ⏳ Pending |
| USB expansion | USB OTG hub for Raspberry Pi Zero 2 W | ⏳ |

---

# Printer platform

## Anycubic Kobra Max

The base machine is the original Anycubic Kobra Max, not a Kobra 2 Max or another later Kobra revision.

The printer retains its original:

- Aluminium frame
- Large moving bed
- X gantry
- Dual Z motors
- Top Z synchronization belt
- Bowden extruder arrangement
- Heated bed
- External bed MOSFET
- Most of the original cable harnesses
- LeviQ strain-gauge probing system

The conversion changes the control electronics and some printed mounting parts while preserving the basic mechanical platform.

## Measured movement and printable area

The following values were measured on this machine and are not universal specifications.

| Area | X range | Y range | Notes |
|---|---:|---:|---|
| Approximate physical bed | Starts near X4 | Starts near Y8 | Physical edge, not recommended print origin |
| Intended printable area | X14 to X405 | Y23 to Y423 | Leaves margins for clips and clearance |
| Approximate mechanical travel | X0 to X415 | Y0 to Y438 | Used for maintenance and purge movement |

The configuration and slicer should distinguish between:

1. Mechanical travel
2. Printable area
3. Bed-mesh area
4. Purge and maintenance area

The current movement and mesh values are documented in the cfg files and remain machine-specific.

---

# Original control electronics

## Trigorilla Pro A V1.0.4

The printer originally used an Anycubic Trigorilla Pro A V1.0.4 mainboard.

The original board was removed, but its printed connector labels were used to reverse-engineer the harnesses.

Important observations from the original board:

- The X connector carries more than the X motor
- The E/toolhead connector carries most toolhead functions
- Two populated `ZR` connectors are used for the Z system
- The original `ZL` connector is not populated on this machine
- The heated bed uses a separate external MOSFET board
- The original LCD connection is proprietary and is not reused

The verified connector mapping is documented in [Wiring.md](./Wiring.md).

## Original display

The original Anycubic display is not used.

Reasons:

- It is proprietary
- It is not directly supported by the current Klipper configuration
- Mainsail provides the primary interface

A separate KlipperScreen-compatible display may be added later, but it is not part of the verified hardware snapshot.

---

# Replacement mainboard

## BIGTREETECH SKR 3 EZ v1.0

The replacement controller is a BIGTREETECH SKR 3 EZ v1.0.

### Verified MCU

The physical MCU fitted to this board is:

```text
STM32H723
```

This is important because SKR 3 documentation and firmware examples often also reference STM32H743 variants.

Firmware must be built for the MCU physically installed on the board.

### Current firmware interface

Klipper communicates with the SKR over USB.

The current firmware was built using:

```text
Architecture:            STMicroelectronics STM32
Processor model:         STM32H723
Bootloader offset:       128 KiB
Clock reference:         25 MHz crystal
Communication interface: USB on PA11/PA12
```

The complete `/dev/serial/by-id/` path is unique to each board and must not be copied from this machine.

## Mainboard mounting

The SKR is mounted using a modified version of an existing adapter plate.

Current STL:

- [Kobra Max SKR 3 adapter plate](./stl/Kobra%20Max%20SKR3%20Adapter%20plate.stl)

The modified adapter:

- Moves mounting holes to suit this installation
- Uses ordinary 3 mm screws
- Does not require heat-set inserts
- Positions the board for access to the required connectors

## Chassis modifications

The metal electronics enclosure was cut to provide access to:

- SKR microSD slot
- SKR USB-B connector

The cut edges were covered after modification.

---

# Stepper drivers

## BTT EZ2209 v1.1

Four BTT EZ2209 v1.1 drivers are installed.

| Driver position | Function | Status |
|---|---|---|
| X | X-axis motor | ✅ |
| Y | Y-axis motor | ✅ |
| Z | Both parallel Z motors | ✅ |
| E0 | Bowden extruder motor | ✅ |
| E1 | Not populated | ❌ |

The drivers use UART communication.

Verified UART pins:

| Driver | UART pin |
|---|---:|
| X | `PD5` |
| Y | `PD0` |
| Z | `PE1` |
| E0 | `PC6` |

The current driver-current values are working values for this machine, not universal motor ratings.

See:

- [`tmc.cfg`](./config/tmc.cfg)
- [Stepper wiring](./Wiring.md#stepper-coil-naming)
- [Stepper-related issues](./Issues.md#stepper-motors)

---

# Motion system

## X axis

The original X-axis motor is retained.

The original X harness also carries:

- Bowden extruder motor
- X endstop
- Filament runout sensor
- Chassis-ground wire

This combined harness was one of the most confusing parts of the conversion because all conductors are black.

## Y axis

The original Y motor is retained.

The Y axis uses one motor and a two-wire endstop.

## Z axis

The original dual-Z arrangement is retained.

It consists of:

- Two Z motors
- Two lead screws
- One synchronization belt across the top of the frame
- One TMC2209 driver
- Two parallel SKR motor outputs: `ZAM` and `ZBM`

The motors are not independently controlled.

This means:

- Klipper cannot run `Z_TILT` with the current four-driver arrangement
- Both motors receive the same commands
- Mechanical synchronization depends on motor direction, lead screws and the top belt

One Z motor connector required one coil pair to be reversed so both mirrored motors moved in the same mechanical direction.

The original optical Z sensor remains physically present but is not used by Klipper.

The LeviQ probe acts as the virtual Z endstop.

---

# Raspberry Pi host

## Raspberry Pi Zero 2 W

A Raspberry Pi Zero 2 W runs:

- Raspberry Pi OS Lite 64-bit
- Klipper host process
- Moonraker
- Mainsail
- KIAUH-managed extensions

The Pi Zero 2 W is sufficient for the current configuration, including KAMP and normal Klipper operation.

## Power arrangement

During early testing, the Pi was powered through the printer/SKR arrangement.

This caused the Pi to lose power every time the printer was switched off.

Repeated hard shutdowns were followed by a host failure where:

- Ping still worked
- SSH returned `Connection refused`
- Moonraker and Mainsail were unavailable

The host installation was re-created afterward.

The Pi should be shut down cleanly before removing power:

```bash
sudo poweroff
```

A future power arrangement should allow the Pi to shut down safely or remain powered while the printer electronics are cycled.

## USB connections

The Pi must communicate with:

- SKR 3 EZ
- Mellow FLY ADXL345 USB accelerometer

Because the Pi Zero 2 W has limited USB connectivity, a USB OTG hub is required for simultaneous use.

The hub is not yet part of the fully verified hardware snapshot.

---

# Toolhead

## Original toolhead PCB

The original toolhead PCB is retained.

It distributes:

- Hotend heater power
- Hotend thermistor
- Part-cooling fan
- Hotend fan
- LeviQ power and signals
- Toolhead LED

The original toolhead labels do not describe their final functions reliably.

Verified examples:

| Original label | Actual function |
|---|---|
| `SCL` | LeviQ probe output |
| `SDA` | LeviQ reset |
| `LEVE` | Toolhead LED |

See [Wiring.md](./Wiring.md#toolhead--original-e-connector) for the complete mapping.

## Printhead cover

The original printhead cover broke and was replaced with a modified printed cover based on:

- [Thingiverse model 5380573](https://www.thingiverse.com/thing:5380573)

Repository files:

- [`Kobra max head 2.stl`](./stl/Kobra%20max%20head%202.stl)
- [`Kobra max head 2 infill.stl`](./stl/Kobra%20max%20head%202%20infill.stl)

The cover required additional clearance around the LeviQ load-cell assembly.

Without enough clearance, tightening the cover applied force to the load cell and caused false probe triggers.

## Toolhead LED

The original LED is retained.

It is controlled through:

```text
PE5
```

Verified behavior:

- `1` = on
- `0` = off

The current macro turns the LED on after Klipper has started and loaded the configuration.

---

# LeviQ strain-gauge probe

## Original probe retained

The original Anycubic LeviQ strain-gauge system is retained as:

- Z homing sensor
- Virtual Z endstop
- Bed-mesh probe

## Verified signal roles

| Toolhead signal | Function | SKR pin |
|---|---|---:|
| Original `SCL` | Probe output | `PB15` |
| Original `SDA` | Probe reset | `PB14` |
| Original `LEVE` | Toolhead LED | `PE5` |
| `5V` | Toolhead/probe logic power | 5 V supply |
| `GND` | Common logic ground | GND |

## Level shifting

The original toolhead can use 5 V logic.

The STM32 GPIO uses 3.3 V logic.

A bidirectional 4-channel logic-level converter is therefore used between the toolhead and the SKR for:

- Probe output
- Reset signal

One module is used in the current build. Additional modules originally purchased remain spare.

## Reset requirement

The probe signal worked without reset, but the measured trigger point drifted heavily between attempts.

A LOW/HIGH pulse on the reset line before every probe attempt made the probe stable enough for:

- `QUERY_PROBE`
- Z homing
- `PROBE_ACCURACY`
- Bed meshing
- KAMP adaptive meshing

The reset sequence is documented in:

- [`leviq_probe.cfg`](./config/leviq_probe.cfg)
- [Issues.md](./Issues.md#probe-values-drifted-between-measurements)

---

# Extruder and filament path

## Original Bowden extruder

The original Bowden extruder is retained.

Current verified features:

- Original extruder motor
- 3:1 gear ratio
- Calibrated `rotation_distance`
- Pressure Advance configured for the tested filament setup

Current values remain machine- and material-specific.

The long Bowden tube results in a higher Pressure Advance value than a typical direct-drive system.

## Filament runout sensor

The original filament runout wiring has been mapped.

Current status:

```text
Mapped but not yet configured and functionally verified
```

The intended SKR input is:

```text
E0-DET / PC2
```

---

# Hotend

## Original-style replacement hotend

The original hotend was damaged and replaced with a similar original-style unit.

The current system uses:

- 24 V heater
- 100 kΩ-style thermistor configuration
- Original toolhead PCB
- Original harness arrangement
- Original-style mechanical mounting

The hotend has been verified through:

- Plausible room-temperature reading
- Controlled heating
- Cooldown response
- PID calibration
- Successful prints

PID values are machine-specific and are not hardware specifications.

---

# Fans

The original toolhead fans are retained.

| Fan | Function | SKR output | Status |
|---|---|---|---|
| Part-cooling fan | Print cooling | `FAN0 / PB7` | ✅ |
| Hotend fan | Hotend cooling | `FAN1 / PB6` | ✅ |
| Mainboard fan | Electronics cooling | Not fully documented | ⚠️ Pending |
| Power supply fan | Electronics cooling | Not fully documented | ⚠️ Pending |


The toolhead PCB provides the shared +24 V supply while the SKR controls the separate switched returns.

---

# Heated bed and power stage

## Original heated bed

The original Kobra Max heated bed is retained.

## Original external MOSFET

The original external bed MOSFET is retained.

This means:

- The SKR `HB` output controls the external MOSFET
- The SKR does not carry the bed's full high-current load
- The original bed high-current wiring remains on the MOSFET board

The bed thermistor is connected to:

```text
TB / PA1
```

The bed system has been verified through:

- Room-temperature reading
- Controlled heating
- Cooldown
- PID calibration
- Successful prints

## Original power supply

The original 24 V printer power supply is retained.

The exact PSU model and label data are not currently documented in this repository.

---

# Build plate and bed hardware

The original build plate and bed assembly are retained.

The practical printable area is smaller than the full mechanical travel because margins are reserved for:

- Bed clips
- Edge clearance
- Purge movement
- Nozzle cleaning
- Safe probing

The measured area used by the current configuration is documented in:

- [`steppers.cfg`](./config/steppers.cfg)
- [`leviq_probe.cfg`](./config/leviq_probe.cfg)
- [`KAMP_Settings.cfg`](./config/KAMP_Settings.cfg)

Bed geometry, gantry alignment and mesh values remain machine-specific.

---

# Printed and modified parts

| Part | Purpose | Repository file |
|---|---|---|
| SKR adapter plate | Mounts SKR 3 EZ in electronics enclosure | [`Kobra Max SKR3 Adapter plate.stl`](./stl/Kobra%20Max%20SKR3%20Adapter%20plate.stl) |
| Modified printhead cover | Replaces broken original cover and clears LeviQ | [`Kobra max head 2.stl`](./stl/Kobra%20max%20head%202.stl) |
| Printhead infill/support part | Companion part for modified cover | [`Kobra max head 2 infill.stl`](./stl/Kobra%20max%20head%202%20infill.stl) |

---

# Retained, replaced and pending hardware

## Retained

- Printer frame
- Heated bed
- External bed MOSFET
- Power supply
- X/Y/Z motors
- Dual-Z synchronization belt
- Bowden extruder
- Toolhead PCB
- LeviQ strain gauge
- Toolhead fans
- Toolhead LED
- Endstops
- Most original wiring

## Replaced

- Original Trigorilla mainboard
- Original display interface
- Damaged hotend
- Broken printhead cover
- Mainboard mounting plate

## Added

- SKR 3 EZ
- Four EZ2209 drivers
- Raspberry Pi Zero 2 W
- Logic-level converter
- Printed mainboard adapter
- Printed printhead cover
- Klipper, Moonraker, Mainsail and KAMP

## Pending or optional

- USB OTG hub
- Mellow FLY ADXL345 USB integration
- Input Shaper
- Filament runout sensor configuration
- Optional KlipperScreen
- Raspberry Pi and USB-hub enclosure
- Improved Raspberry Pi power handling

--- 

# Related documentation

- [Wiring](./Wiring.md)
- [Configuration](./config/config.md)
- [Issues and troubleshooting](./Issues.md)
- [Useful links](../Useful_links.md)

This document describes what hardware is present and how the build is structured.

Use `Wiring.md` for electrical connections and `Issues.md` for symptoms, causes and fixes.

---

> [!NOTE]
> This document was drafted with substantial assistance from an LLM, based on my own measurements, testing, research and findings.
