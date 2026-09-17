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
| Toolhead probe | Original Anycubic LeviQ strain gauge | ⚠️ Retained; repeatability/homing reopened |
| Logic conversion | 4-channel bidirectional 3.3 V ↔ 5 V level shifter | ✅ |
| Extruder | Original Bowden extruder | ✅ |
| Hotend | Replacement hotend; latest model not documented | ⚠️ Further replacement reported; final recalibration unverified |
| Heated bed | Original Anycubic heated bed | ✅ |
| Bed power stage | Original external bed MOSFET | ✅ |
| X/Y/Z motors | Original Anycubic stepper motors | ✅ |
| Fans | Original toolhead fans | ✅ |
| Display | Original Anycubic display | ❌ |
| Accelerometer | Mellow FLY ADXL345 USB board | ✅ Used for X/Y calibration; final mounting/configuration not captured |
| USB expansion | USB OTG hub for Raspberry Pi Zero 2 W | ✅ Connected and working during calibration |
| Nozzle cleaner | BBL spare cleaner mounted on the bed margin | ⚠️ Initial operation reported; final macro/height not captured |

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

A separate wire bonds the X gantry to the chassis; it is not another motor or endstop pin in the X connector.

This combined harness was one of the most confusing parts of the conversion because all conductors are black.

## Y axis

The original Y motor is retained.

The Y axis uses one motor and a two-wire endstop.

In the 2026-08-02 tuning conversation I identified the photographed motor as the Y motor. Its visible model marking reads `42BYGH370L-B-89S80`. This records the physical label only; no current rating, torque specification or matching TMC Autotune model was verified from it. I did not identify the other motors as the same model.

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

The host installation was re-created afterward. The sequence supports a power-loss concern, but no filesystem diagnosis established corruption as the cause. The earlier responding IP address was also not conclusively identified as this Pi.

The Pi should be shut down cleanly before removing power:

```bash
sudo poweroff
```

A future power arrangement should allow the Pi to shut down safely or remain powered while the printer electronics are cycled.

## Network history and verification limits

Raspberry Pi OS Lite remained the operating system through the reinstallations; the earlier `3dHostOS` name was a hostname, not a different OS distribution. A later local login screen showed Debian GNU/Linux 13.

The built-in Wi-Fi was managed by NetworkManager. Recreating a connection profile restored access and autoconnect worked in the reported retest, but substantial latency and complete access outages later returned. An IoT-to-main-network change and disabling Wi-Fi power saving did not produce a durable fix.

The host and USB-connected controller remain the hardware described here. No replacement Wi-Fi adapter, wired host network connection or changed power arrangement was confirmed as a final solution. Details and measurement limits are recorded in [Issues.md](./Issues.md#recurring-network-latency-and-loss-of-access).

## USB connections

The Pi must communicate with:

- SKR 3 EZ
- Mellow FLY ADXL345 USB accelerometer

Because the Pi Zero 2 W has limited USB connectivity, a USB OTG hub is required for simultaneous use.

On 2026-08-03 I reported the hub connected and working, with the accelerometer taped firmly to the printhead and its cable slack throughout X travel. X and Y calibration results followed. The hub model, port layout and final Y sensor mounting were not recorded; this establishes operation during calibration, not a long-term USB or network reliability test. See [the calibration record](./config/config.md#input-shaper-and-nozzle-cleaner-versus-this-snapshot).

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

In the later diagnostic branch, I removed the cover and clarified that the LeviQ electronics were still installed. The cover and associated fan/light/printed parts were off during that phase; this was not an electrically disconnected printhead test. No final reassembly state was documented. The retained-parts inventory is not a claim that every part was fitted during every diagnostic run.

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

Those were initial verification results. Later trigger shifts and homing failures recurred despite reset-timing experiments, and a further hotend replacement did not eliminate them. The original LeviQ element and electronics were not reported replaced. Their exact contribution remains unresolved; see [the follow-up investigation](./Issues.md#intermittent-trigger-shifts-after-the-initial-fixes).

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

The tube was completely disconnected for some later probe-isolation tests, and large errors persisted. Subsequent motion tests again mentioned Bowden behavior, so the whole investigation must not be labelled Bowden-free. No direct-drive conversion was reported. Later skipping and filament grinding are recorded in [Issues.md](./Issues.md#intermittent-extruder-skipping-and-filament-grinding); suspected gear wear was not confirmed by inspection.

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

The established hotend configuration uses:

- 24 V heater
- 100 kΩ-style thermistor configuration
- Original toolhead PCB
- Original harness arrangement
- Original-style mechanical mounting

The earlier hotend installation was verified through:

- Plausible room-temperature reading
- Controlled heating
- Cooldown response
- PID calibration
- Successful prints

PID values are machine-specific and are not hardware specifications.

### Further replacement during LeviQ diagnosis

I later reported leakage from the nozzle and up near the heatbreak and tightened the nozzle. A further hotend replacement was then reported, but tolerance failures and unstable probe coordinates remained. This was not a replacement of the LeviQ electronics or confirmation that the load-cell assembly was fault-free.

The latest replacement's model, nozzle geometry and exact heater/thermistor components were not documented. The repository's existing heater configuration therefore remains the published reference, not an independently verified specification for that replacement. The final returned probing series was reported at 220 °C nozzle / 50 °C bed, but no new PID calibration, Z-offset calibration or successful post-investigation print was supplied. See [Issues.md](./Issues.md#leakage-tightening-and-a-further-hotend-replacement).

### OEM replacement and silicone sock in the upgrade branch

On 2026-08-07 I reported a thermistor problem and an attempted repair that was too fiddly to continue. On 2026-08-09 I reported a new OEM hotend and silicone sock, saying that it seemed to print okay. Later that day, intermittent extrusion failure was still reported. On 2026-08-14 I reported an ordered unit arriving with a "short" reading of 0.09 Ω, without identifying the measured component or test arrangement.

These dated reports do not identify the model of the separate LeviQ branch's further replacement or establish which hotend was ultimately retained across branches. No exact OEM part number, final sensor specification or new PID/Z-offset record accompanies them. The earlier inventory and the unresolved cross-branch replacement status are therefore preserved. Detailed failure limits are in [Issues.md](./Issues.md#hotend-thermistor-failures-in-the-upgrade-branch).

### Upgrade candidates, not installed hardware

The discussion considered an original-extruder direct-drive mount, Revo, Stealthburner and alternative extruders, followed by easier-to-service V5/Volcano-style replacement hotends. The intended materials were PLA, PETG and possibly TPU. No final purchase choice or installation of these upgrades was confirmed.

| Candidate | What the supplied material actually establishes | Status on this machine |
|---|---|---|
| Original extruder moved to direct drive | A specific [mount reference](../Useful_links.md#upgrade-comparison-reference) was discussed | Not reported printed, fitted or tested |
| BMG-type, Redrex dual-gear kit with motor, or Super Print dual-gear extruder | Alternatives discussed after skipping/grinding; the current motor was only tentatively described as about 42 × 42 × 33 mm with an approximately 20 mm D shaft | No replacement reported installed; shaft fit, motor identity and worn gears unconfirmed |
| HOCENWAY, ASIN `B0CM29QYN1` | Supplied listing: 24 V / 45 W, 3 mm OD NTC100K cartridge sensor, preinstalled PTFE/heater/sensor; no Beta value stated | Advertised specification only; not a confirmed installed sensor or heater |
| Super Print All Metal V2.0, ASIN `B0DDCKWGQH` | Supplied listing: 24 V / 40 W, bi-metal heatbreak, 3 × 5.5 mm NTC100K B3950 sensor; self-assembly required | Discussed option; no confirmed purchase, assembly or print |
| EPLZON, ASIN `B0DK746PN4` | Supplied listing selected "For Vyper"; preassembled, 0.4 mm brass nozzle; exact sensor curve and cartridge dimensions not specified | Compatibility and component details not verified on this printer |

The product PDFs describe offers considered in the conversation, not measurements or fit checks. Revo/Stealthburner compatibility, retained LeviQ operation with a new mount and any performance benefit remained proposals. The existing extruder already has `gear_ratio: 3:1` in the published cfg; discussion of another geared extruder does not establish a changed ratio or calibration.

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

The original PSU-label photograph from the build identifies:

| Label field | Printed value |
|---|---|
| Manufacturer | MOSO |
| Model | `MS-TB100J240-500C0` |
| Input | 100–240 V AC, 50/60 Hz, 6 A max. |
| Output | 24.0 V DC, 21.0 A, 504.0 W |

These are nameplate ratings, not measured printer consumption or measured bed current. The early whole-printer power estimates did not establish the bed's current when fully powered. Retaining the existing external MOSFET avoided treating those estimates as approval to connect the bed directly to the SKR.

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

## Bed-mounted nozzle cleaner

On 2026-08-03 I reported mounting a spare BBL nozzle cleaner on the bed outside the print area. Its reported span was approximately X34 to X76 at Y431. At X34 Y431 Z2 I still described roughly a millimetre of clearance, so Z2 is a reported approach coordinate, not a verified wiping-contact height. No mount drawing, STL or exact cleaner model was supplied.

The next reply reported that it worked after a wiping macro had been proposed. This supports initial operation only; the exact final macro and integration into `PRINT_START` were not returned. See [the snapshot distinction](./config/config.md#input-shaper-and-nozzle-cleaner-versus-this-snapshot).

## Repository parts

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
- Damaged hotend, followed by a further replacement reported during LeviQ diagnosis
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
- USB OTG hub and Mellow FLY ADXL345 used for calibration
- Bed-mounted nozzle cleaner, with initial operation reported
- Silicone sock reported with the 2026-08-09 OEM hotend; later assembly state not established

## Pending or optional

- Final saved accelerometer/Input Shaper configuration and post-calibration print validation
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
