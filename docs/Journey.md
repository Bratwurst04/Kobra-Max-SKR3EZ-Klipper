# Project journey

This document records the development of the Anycubic Kobra Max to SKR 3 EZ Klipper conversion.

It is not a replacement for:

- [Wiring.md](./Wiring.md)
- [Configuration documentation](./config/config.md)
- [Issues and troubleshooting](./Issues.md)

Instead, it explains how the working conversion and subsequent investigations developed, including wrong assumptions, failed approaches and the tests that changed the direction of the project.

> [!NOTE]
> This journey is reconstructed from the build notes and troubleshooting history.
>
> It describes the order of discoveries rather than presenting a polished installation guide.

## Journey summary

| Phase | Main question | Result |
|---|---|---|
| 1. Planning | Was the available hardware enough? | Most required hardware was already available |
| 2. Original electronics | What did each original connector carry? | Original harnesses were mapped from board labels and continuity testing |
| 3. Mainboard firmware | Which MCU and firmware settings were correct? | Physical STM32H723 identified and Klipper USB firmware verified |
| 4. Basic motion | Could the SKR drive the original motors? | X, Y, Z and extruder verified with UART and `STEPPER_BUZZ` |
| 5. Heating and fans | Could the original thermal hardware be retained? | Hotend, fans, bed thermistor and external MOSFET all worked |
| 6. LeviQ | Could the original strain gauge work with Klipper? | Probe, reset and LED signals were identified and verified |
| 7. First print | Would the assembled configuration produce a part? | First calibration cube printed successfully |
| 8. Tuning | Could print quality be improved without changing hardware? | PID, extrusion and Pressure Advance were calibrated; Input Shaper was pending at that stage and completed in the follow-up below |
| 9. Documentation | How could the discoveries be preserved? | GitHub repository created with wiring, config and issue documentation |
| Host/network follow-up | Was restored remote access durable? | Profile recovery verified, but later LAN/Wi-Fi outages remained unresolved |
| LeviQ follow-up | Did early probe stability survive later tests? | Trigger shifts and Z homing reopened; tight warm single-point repeatability did not establish a complete fix |
| Tuning/upgrade follow-up | What followed KAMP and the USB-hub wait? | X/Y shaper calibration, a nozzle cleaner and an OEM hotend/sock were reported; extrusion/thermistor faults remained open |

---

# 1. Starting point

## Original goal

The original goal was to convert an original Anycubic Kobra Max to Klipper while reusing as much existing hardware as practical.

Available replacement hardware included:

- BTT SKR 3 EZ
- Four BTT EZ2209 v1.1 drivers
- Raspberry Pi Zero 2 W
- Several bidirectional 3.3 V ↔ 5 V level-shifter boards
- Mellow FLY ADXL345 USB accelerometer

The uncertain parts were:

- Whether four drivers were enough
- Whether an additional bed MOSFET was required
- Whether the original display could be used
- Whether the original LeviQ strain-gauge probe could work
- How the proprietary-looking original harnesses were wired

## Early assumptions

Several initial assumptions later changed:

| Initial assumption | Final finding |
|---|---|
| A fifth stepper driver might be required for dual Z | Both Z motors use one driver and two parallel outputs |
| A new bed MOSFET might be required | The original printer already had an external MOSFET |
| The original strain gauge might need replacement | The original LeviQ worked in initial tests after identifying probe/reset signals; later reliability was reopened |
| `SCL` and `SDA` probably formed an I²C bus | They function as probe output and reset in this build |
| SKR 3 EZ probably used STM32H743 | The physical board uses STM32H723 |
| The X connector only carried X-axis functions | It also carries the Bowden extruder motor and other signals |

These corrections became the main reason to document the conversion rather than publish only the final cfg files.

The early external-MOSFET discussion included whole-printer power estimates and an incorrect first reading of the photos as showing no separate board. The user identified the existing `CONTROL`/`HOTBED` board. No bed-only full-power current measurement was established by those power estimates; the retained external board is the configuration documented here.

---

# 2. Inspecting the original electronics

## Original mainboard

The original board was identified as:

```text
Trigorilla Pro A V1.0.4
```

Photographs were taken of:

- Front side
- Rear side
- Connector labels
- Connected harnesses
- External bed MOSFET
- Power-supply arrangement

The printed labels on the rear of the original board were written beside the corresponding connector positions in a spreadsheet.

This produced the first usable map of the harnesses.

## Why the harnesses were difficult

All conductors are black.

Several connectors combine unrelated systems.

The X harness, for example, carries:

- X motor
- Extruder motor
- X endstop
- Filament runout signal

A separate bonding wire connects the chassis and X gantry; later physical tracing distinguished it from the populated X-connector pins.

The E/toolhead harness carries:

- Hotend heater
- Hotend thermistor
- Two fans
- Toolhead logic supply
- LeviQ signals
- Toolhead LED

A visual assumption based only on connector position was therefore unreliable.

## Motor coil mapping

The original motor labels were:

```text
OA1
OA2
OB1
OB2
```

Continuity and resistance testing established that:

- `OA1` and `OA2` are one coil
- `OB1` and `OB2` are the other coil

The matching SKR coil groups are:

- `1A` and `1B`
- `2A` and `2B`

This became the base motor mapping used throughout the build.

## Discovery of the X harness functions

The duplicate motor-label groups in the X connector were initially confusing.

The final physical tracing showed:

- One four-wire group goes to the X motor
- One four-wire group goes to the Bowden extruder motor
- Two wires go to the X endstop
- Two wires go to the filament runout sensor
- One separate wire bonds the X gantry to chassis ground
- The 3.3 V endstop position is not populated

This explained why the X connector appeared to contain two complete motor-output groups.

---

# 3. Understanding the Z system

## Original connector arrangement

The original board has labels for `ZR` and `ZL`, but this machine uses:

- Two populated ZR-related motor connections
- No populated ZL harness
- One Z harness that also carries the original optical Z sensor

The two Z motors are also linked by a synchronization belt across the top of the frame.

## Decision to use one driver

The SKR 3 EZ provides:

- `ZAM`
- `ZBM`

These are parallel outputs from one Z driver.

The existing mechanical design already expects the two motors to move together, so the current build uses:

- One EZ2209 Z driver
- One motor connected to `ZAM`
- One motor connected to `ZBM`

This allowed the build to continue with four drivers:

```text
X
Y
Z
E0
```

## Original optical Z sensor

The original optical Z sensor is mounted near one Z motor.

It uses three wires:

- 3.3 V
- Ground
- Signal

It is not used in the final Klipper configuration.

The LeviQ probe became the virtual Z endstop instead.

---

# 4. Installing the Raspberry Pi host

## Initial host setup

The Raspberry Pi Zero 2 W was installed with:

- Raspberry Pi OS Lite 64-bit
- KIAUH
- Klipper
- Moonraker
- Mainsail

The Pi hostname changed during reinstallations. The user clarified that `3dHostOS` had been a hostname and that the OS had always been Raspberry Pi OS Lite. Hostnames affected how the host was reached over the network, not the printer's wiring or MCU pin assignments.

## Missing Python dependency

Klipper initially failed to start with:

```text
ModuleNotFoundError: No module named 'greenlet'
```

The service was auto-restarting before `klippy.log` was created. The system journal showed the failed import from `reactor.py` in the Python environment used by the service.

Restoring the Klipper Python dependencies was followed by the user's confirmation that the host connection worked. The instructions offered both the requirements file and a direct-package fallback; the exact branch taken was not reported. This was separate from printer wiring and MCU firmware.

Update Manager had also shown `INVALID` entries. A refresh displayed ordinary installed/update versions before the missing-module diagnosis; no Git repair was established by that change. The detailed host issue is in [Issues.md](./Issues.md#klippy-exited-before-creating-its-log).

## Hard shutdown failure

During motor testing, the Pi was powered through the printer.

Every time the printer was switched off to avoid hot-plugging motor connectors, the Pi also lost power.

After several cycles:

- Ping still worked
- SSH returned `Connection refused`
- Moonraker was unreachable
- Mainsail was unreachable

The host installation was re-created and access returned. Corruption was suspected, not diagnosed by a filesystem check. The old responding IP was not conclusively tied to the Pi's later-observed WLAN MAC address, so the ping replies did not settle device identity either.

This became an important lesson:

> A responding IP address does not prove that SSH, Moonraker or the filesystem is healthy.

The incident is documented in [Issues.md](./Issues.md#ping-worked-but-ssh-and-moonraker-did-not).

---

# 5. Building and verifying SKR firmware

## Initial MCU confusion

Early documentation and examples suggested STM32H743 settings.

The actual chip marking on the physical SKR board showed:

```text
STM32H723
```

That physical marking became the source of truth.

## Final firmware settings

Klipper firmware was built using:

```text
Architecture:            STMicroelectronics STM32
Processor model:         STM32H723
Bootloader offset:       128 KiB
Clock reference:         25 MHz crystal
Communication interface: USB on PA11/PA12
```

## SD-card flashing confusion

The firmware file did not reliably rename from:

```text
firmware.bin
```

to:

```text
firmware.cur
```

This initially suggested that flashing had failed.

However, USB testing showed:

```text
VID_1D50
PID_614E
```

The Raspberry Pi also detected:

```text
usb-Klipper_stm32h723xx_...
```

Later MCU communication in the uploaded `klippy.log` confirmed running `stm32h723xx` Klipper firmware, so the SD-card filename had not been a reliable indication of failure.

The Windows partition screenshot showed a small FAT `RECOVERY` partition before the FAT32 firmware volume. The user could not remove it in the attempted workflow. This was a possible SD-bootloader compatibility issue, not a confirmed cause. SWD and DFU were discussed but no repair through either was documented. See [the firmware issue](./Issues.md#firmware-identification-and-sd-card-update-ambiguity).

## MCU serial path

Klipper required the complete path:

```text
/dev/serial/by-id/usb-Klipper_stm32h723xx_<complete-id>-if00
```

The initial `printer.cfg` still contained a placeholder serial path. It was replaced with the full detected identifier. A separate missing Python dependency still prevented Klippy from starting, so the early disconnected state was not attributable to the placeholder alone.

This reinforced another documentation rule:

> Never shorten machine identifiers inside copyable configuration examples unless they are clearly marked as placeholders.

---

# 6. Creating the first Klipper configuration

## Split configuration structure

The configuration was split by function:

```text
printer.cfg
steppers.cfg
tmc.cfg
bed.cfg
fans.cfg
leviq_probe.cfg
accessories.cfg
macros.cfg
KAMP_Settings.cfg
```

This made it easier to isolate errors without searching through one large file.

## Cartesian configuration requirement

When `kinematics: cartesian` was enabled, Klipper required complete X, Y and Z stepper sections.

It was not possible to configure only X and postpone Y/Z while keeping Cartesian kinematics active. The reported error was:

```text
Option 'endstop_pin' in section 'stepper_y' must be specified
```

The early motor-test configuration supplied the missing axis sections and temporarily referenced `PC0` for Z with no working Z sensor. That was a configuration-loading stage with homing explicitly postponed, not the later verified LeviQ homing setup.

## TMC UART verification

Each installed EZ2209 driver was tested with:

```gcode
DUMP_TMC STEPPER=stepper_x
DUMP_TMC STEPPER=stepper_y
DUMP_TMC STEPPER=stepper_z
DUMP_TMC STEPPER=extruder
```

A successful UART response confirmed communication with the driver, but did not prove that the motor coils were wired correctly.

That distinction became important during later faults.

---

# 7. Motor-wiring failures

## Y and Z shutdowns

X passed `STEPPER_BUZZ`, while Y and Z initially caused Klipper shutdowns or failed to move.

The user later found that the physical wire destinations had been identified incorrectly. The uploaded log captured Y `s2vsb=1(ShortToSupply_B!)` and `ola=1(OpenLoad_A!)`; it did not by itself prove which wire was wrong.

After an intermediate change, Y stopped shutting down but remained motionless. A further retrace was followed by the user's confirmation that both X and Y now buzzed. TMC UART had worked throughout, which made the fault appear more complicated than it was.

The final rule was simple:

```text
OA1 + OA2 = one coil
OB1 + OB2 = the other coil
```

Those pairs must remain intact when moved to the SKR connector.

## Extruder E0/E1 pin mistake

The extruder was connected physically to `E0M`, but the first configuration used pins belonging to the other extruder position.

The correct E0 pins were established as:

```text
Step:   PD15
Dir:    PD14
Enable: PC7
UART:   PC6
```

Before correcting UART, Klipper reported:

```text
Unable to read tmc uart 'extruder' register IFCNT
```

After changing UART to `PC6`:

- `DUMP_TMC` worked
- Extruder `STEPPER_BUZZ` worked

## Z motors fighting each other

Both Z motors worked perfectly when tested individually.

When connected together:

- Z became loud
- Movement was weak
- One direction barely worked
- The motors fought through the top belt
- The belt skipped
- The gantry became skewed

One coil pair on one motor connector was reversed.

Afterward both motors moved together normally.

The gantry was then mechanically re-aligned before relying on bed mesh.

---

# 8. Endstops, heaters and fans

## X and Y endstops

The original X and Y endstops are two-wire devices.

Their signal labels are:

- `X_SQ`
- `Y_SQ`

The first Klipper logic state was inverted:

- Idle appeared `TRIGGERED`
- Pressed appeared `open`

Adding inversion to the endstop pin configuration produced the correct behavior.

Both X and Y homing were then verified.

## Hotend

The hotend system was verified in stages:

1. Plausible room-temperature reading
2. Low target temperature
3. Correct heating response
4. Cooldown response
5. PID calibration
6. Successful extrusion and printing

## Fans

The original fans were retained.

Verified outputs:

```text
FAN0 / PB7 = part cooling
FAN1 / PB6 = hotend fan
```

Both fans worked correctly from Klipper.

## Heated bed

The original external MOSFET board was discovered during physical inspection.

This changed the plan: no replacement MOSFET was needed.

The bed thermistor was initially plugged into `TH1`.

The current configuration expected:

```text
TB / PA1
```

Moving the connector fixed the temperature reading.

The bed heater was then tested and PID-calibrated.

---

# 9. Reverse-engineering LeviQ

## Toolhead signal uncertainty

The original E/toolhead connector contained:

```text
5V
SCL
SDA
LEVE
GND
```

The labels initially suggested I²C.

External adapter-board information showed the likely toolhead functions:

- Probe output
- Reset
- LED

## Identifying the probe signal

A temporary digital input test was created.

One signal behaved as:

- Idle: HIGH
- Nozzle/load-cell force: LOW

This identified:

```text
Original SCL → probe output → PB15
```

The final Klipper probe input became:

```ini
pin: ^!PB15
```

`QUERY_PROBE` then returned the expected states.

## First successful Z homing

The LeviQ probe was configured as:

```text
probe:z_virtual_endstop
```

The first Z-homing test was performed with manual trigger testing before allowing the nozzle to approach the bed.

Z homing worked.

At this point all three axes could home successfully using the original Kobra Max sensors.

## Identifying reset

Early `PROBE_ACCURACY` results showed severe drift.

The remaining signal was tested and found to reset the load cell:

```text
Original SDA → LeviQ reset → PB14
```

A LOW/HIGH reset pulse before every probe attempt stabilized the measurements.

## Identifying the LED

The original `LEVE` signal was tested as an output.

It controlled the toolhead LED:

```text
PE5
1 = on
0 = off
```

A delayed macro was added to turn on or blink the LED after Klipper started.

## Mechanical false triggers

Even after the electrical signals were correct, the probe sometimes triggered with almost no nozzle force.

The replacement printhead cover was pressing on the load-cell assembly.

Additional clearance around the LeviQ screws fixed the false triggers.

This was a useful reminder that a digital probe fault can still have a mechanical cause.

---

# 10. Probe stability and bed geometry

## Probe repeatability

Before reset and mechanical clearance were fixed, probe measurements drifted by more than a millimetre.

Afterward, repeated measurements at a fixed point reached ranges in the approximate hundredths-of-a-millimetre region.

The probe was sufficiently stable for:

- Z offset
- Bed mesh
- KAMP adaptive mesh

## Cable and Bowden investigation

The Bowden tube and toolhead cable appeared heavy enough to influence the sensitive load cell.

Tests were performed:

- At multiple X positions
- With and without filament in the Bowden tube
- With temporary elastic support
- Before and after gantry correction

The resulting mesh shape was highly repeatable between runs.

Filament inside the Bowden tube did not significantly change the mesh result.

This suggested that much of the measured shape was real machine geometry or repeatable load, rather than random probe noise.

## Gantry skew

The X gantry was found to be visibly skewed after the earlier Z-motor conflict.

Mechanical correction reduced the measured left-to-right error substantially.

The remaining variation was left for bed mesh to compensate.

## Final approach

The project did not attempt to make every physical deviation zero.

Instead:

1. Correct major mechanical skew
2. Make the probe repeatable
3. Use bed mesh for repeatable remaining geometry
4. Avoid using mesh to hide unstable or random probe behavior

These were the conclusions at that stage. Later tests reopened the reliability assumption; the [LeviQ follow-up](#leviq-follow-up-from-this-troubleshooting-branch) preserves that recurrence rather than replacing the early successful results.

---

# 11. First print

## Start G-code failure

The first start G-code mixed:

- Absolute extrusion mode
- Relative extrusion settings in OrcaSlicer
- Large purge-line E values

Klipper stopped with:

```text
Move exceeds maximum extrusion
```

The purge sequence was rewritten using consistent relative extrusion.

## First calibration cube

After correcting the start G-code, the printer produced its first cube.

It was not perfect, but it proved that the complete system worked:

- Motion
- Homing
- LeviQ
- Bed mesh
- Heating
- Extrusion
- Fans
- Klipper host communication

This was the point where the project changed from hardware bring-up to tuning.

---

# 12. Calibration and tuning

## PID calibration

Both heaters were PID-calibrated:

- Extruder
- Heated bed

The resulting values are machine-specific.

## Extruder rotation distance

The Bowden extruder was commanded to feed 100 mm.

The first result under-extruded by approximately 1.45 mm.

The calibrated value became approximately:

```text
rotation_distance: 22.350
gear_ratio: 3:1
```

This value belongs to the current extruder and should not be treated as universal.

## Pressure Advance

A Pressure Advance test was printed for the long Bowden system.

The selected value was:

```text
pressure_advance: 0.44
```

This improved corner bulging on the tested filament and setup.

Pressure Advance remains material- and temperature-dependent.

The bowden tube is also not in the greatest shape, affecting the value.  

## Calibration cube dimensions

A later 20 mm cube measured approximately:

```text
X: 20.18 mm
Y: 20.10 mm
Z: 20.10 mm
```

The cube also showed:

- Corner bulging before Pressure Advance
- Some elephant foot
- Ringing on selected faces

These observations were treated as separate tuning issues rather than immediately changing axis scale.

## Input Shaper

The remaining visible ringing motivated Input Shaper calibration.

Hardware selected:

```text
Mellow FLY ADXL345 USB
```

A USB hub was ordered so the Raspberry Pi Zero 2 W could communicate with both:

- SKR 3 EZ
- USB accelerometer

Input Shaper was pending at this stage. The [later tuning follow-up](#tuning-and-hardware-upgrade-follow-up) records the completed X/Y calibration without treating its results as a validated final print profile.

---

# 13. KAMP and printable area

## Why KAMP was added

The Kobra Max has a large bed.

Meshing the entire bed before every small print is slow and unnecessary.

KAMP was installed to provide:

- Adaptive mesh around the current model
- Adaptive purge line
- Smart Park

## Mechanical travel vs printable area

Measurements showed that full mechanical travel extends beyond the desired print area.

Margins were reserved for:

- Bed clips
- Prime lines
- Nozzle cleaning
- Safe probing
- Maintenance movement

Current measured areas are documented in:

- [`steppers.cfg`](./config/steppers.cfg)
- [`leviq_probe.cfg`](./config/leviq_probe.cfg)
- [`KAMP_Settings.cfg`](./config/KAMP_Settings.cfg)

## Start macro

The slicer start G-code was simplified to call a Klipper macro.

The macro handles:

- Heating
- Homing
- Clearing old mesh
- Adaptive mesh
- Smart Park
- Final nozzle temperature
- Adaptive purge

This moved printer-specific behavior out of the slicer profile and into the repository configuration.

---

# 14. Creating the GitHub documentation

## Why the repository was created

During the conversion, several pieces of information were difficult to find or absent:

- Original connector functions
- X harness containing two motors
- Correct E0 pins
- Original LeviQ probe output
- LeviQ reset requirement
- Toolhead LED signal
- External MOSFET arrangement
- Dual-Z behavior on the SKR parallel outputs

The repository was created to preserve these findings.

## Documentation structure

The project documentation was separated into:

| File | Purpose |
|---|---|
| `README.md` | Project overview and current status |
| `Hardware.md` | Exact hardware used and retained |
| `Wiring.md` | Verified electrical mapping |
| `Journey.md` | Chronological development history |
| `Issues.md` | Symptom, cause, fix and verification |
| `config/config.md` | Configuration-file guide |
| `Useful_links.md` | External references |
| `docs/stl/` | Modified printable parts |

This separation prevents one document from becoming a mixture of:

- Wiring manual
- Personal build log
- Troubleshooting guide
- Configuration reference
- Hardware inventory

---

# Host and network follow-up from this chat branch

These host events overlapped motor bring-up and continued in later network troubleshooting. Their exact order relative to the heating, LeviQ and tuning work already documented from other branches is not established. They supplement that history rather than undoing those verified printer milestones.

## Profile recovery after reinstallation

After access returned following the host reinstall, a later startup again left the Pi unreachable. Local screen/keyboard access worked. NetworkManager reported `wlan0` disconnected and listed only loopback among connection profiles; WLAN was not blocked by `rfkill`.

The initial explanation blamed `[ifupdown] managed=false`, but that was not demonstrated as the cause. Creating/activating a Wi-Fi profile with `nmcli` restored the connection, and the user confirmed autoconnect working in the next reported check. This established a recovery, not why the earlier profile was absent from the listing.

## Latency persisted after access returned

Later tests showed hundreds of milliseconds of average gateway latency, several-second peaks and intermittent packet loss. Disabling Wi-Fi power saving and changing from the IoT SSID to the main SSID did not produce a lasting solution.

At the sampled times, CPU and memory use were modest, RSSI was around -50 to -55 dBm, and local self-pings were fast. These observations narrowed the investigation but did not clear every host, power or radio component.

## Other LAN traffic was affected too

The Windows PC, described as Ethernet-connected, also experienced packet loss and multi-second delays to the Internet and local gateway. This broadened the investigation beyond the printer's software. The exact PC-to-gateway path, including any wireless mesh segment, was not established.

The Pi sometimes became reachable again without intervention, then lost access again later. A subsequent local login screen still showed an IP while access failed, and `ip neigh` entries were reported as `STALE`. That state did not prove a broken neighbour table.

## Last supported outcome

The profile-recovery milestone remains valid, but long-term network reliability was **unresolved / reopened** at the end of the available network discussion. No returned hotspot comparison, definitive outage journal or confirmed Deco change established a permanent fix. The measured results and limits are owned by [Issues.md](./Issues.md#recurring-network-latency-and-loss-of-access).

---

# LeviQ follow-up from this troubleshooting branch

This investigation followed the earlier successful probing and printing milestones, but its exact calendar dates and its order relative to the separate host/network follow-up are not established. It reopens probe reliability without undoing the verified wiring or replacing the other branch's current status.

The detailed measurements and their limitations are kept in [Issues.md](./Issues.md#intermittent-trigger-shifts-after-the-initial-fixes). The history below records how the interpretation changed.

## Earlier stability did not last

The opening handover already described repeatability as good as roughly 0.0075–0.02 mm in some runs, alongside intermittent shifts of tenths of a millimetre and sometimes more than a millimetre. The same problem could occur with the Bowden completely disconnected.

Longer or repeated reset sequences, slower motion, different retract distances, alternating start heights and a 30-second passive wait had not eliminated it. Several consecutive nozzle contacts sometimes brought a point back toward a repeatable value. Moving local wires, slightly tightening load-cell screws and removing the sock had produced an improvement that did not last.

That was the starting point for this branch, not evidence that the original reset and cover-clearance fixes had never worked.

## Contact history appeared important

The first comparisons used A at X208 Y223 and B at X223.025 Y211.366. Visiting A without contact did not reproduce the shift that followed an actual probe at A.

The returned C-to-B test kept Y unchanged, and a D-to-B test kept X unchanged. Both could disturb the following B result. Repeating the Y comparison on the opposite side of B still gave negative shifts rather than a reliable reversal of direction. A reset-only visit to C did not reproduce the large change.

At this stage mechanical conditioning, stick-slip and changes in the load-cell force path became leading hypotheses. They were not proven causes: an actual probe also changes Z movement and timing, and the D baselines were still converging. Later events at the same XY point showed that visiting a different point was not necessary for the fault.

## Manual loading narrowed the questions, not the component

A first finger press on the bed was followed by a changed B value, but B had not settled beforehand. In a later no-touch versus finger comparison, both excursions changed the next reading by the same 0.01250 mm relative to the preceding contact. The large bed-only effect was not reproduced.

An upward load on the nozzle without bed contact shifted the next reading more than a separate load on the rigid carriage/body. That kept the local hotend/load-cell assembly under suspicion, but the forces were not calibrated and these were not repeated isolation experiments. A further proposed test directly on the moving load-cell mount was not reported as completed.

## A leaking hotend was not the whole explanation

I reported leakage at the nozzle and near the heatbreak and tightened the nozzle. The investigation then focused heavily on that joint. However, separate X200 Y200 contacts starting from Z2 still switched between very different trigger coordinates, and large changes also occurred before I had performed the proposed additional manual nozzle push.

I subsequently replaced the hotend again. Probing still failed with repeated tolerance retries, including a span of nearly a millimetre at X208 Y223. The replacement was a real hardware change, but the expected complete fix did not follow. Its exact model and final calibration were not documented in this branch.

## Motor-related flicker reopened the electrical question

After removing the cover, I could no longer home Z reliably and noticed indicator flicker during Z movement. I clarified that the LeviQ electronics were still mounted: it was the cover and associated parts that were off, not the entire electrically connected printhead removed from the machine.

The first suspicion was interference from the motors. `STEPPER_BUZZ` produced a blink on X and Z, mostly at the initial kick, but not on Y. Ordinary X moves remained quiet on the indicator even with high requested acceleration. A substantial manual Z jerk could also produce a blink. Enabling X, Y or Z without movement produced none.

These observations did not confirm EMI or a motor-enable fault. No motor-disconnected or mechanically decoupled comparison, changed-current result or electrical waveform was returned. Both mechanical excitation and electrical interference remained possible.

## A speed-associated symptom became reproducible

Controlled Z movements gave a clearer pattern: low speeds and 3–4 mm/s produced no reported flicker, 5 mm/s produced strong flicker during motion, and 6–8 mm/s mainly produced blinks at starts or stops.

A resonance-like explanation became plausible. It remained an explanation, not a measured resonance or proof that electronics were uninvolved. The dashboard's global acceleration setting also did not establish the actual Z motion profile.

The 5 mm/s value was especially relevant because the published probe lift, safe-home hop and first Z-homing speeds all used it. Slow 2 mm/s external Z moves and probe lifts were then used in the returned diagnostic logs. Temporary debug positioning and alternative homing settings had been discussed, but no complete final homing configuration or successful final home was supplied.

## The cleaner retract comparison contradicted the attractive explanation

The first 2 versus 5 mm/s comparison appeared to link a fast lift to a later bad reading. It also changed the external return-to-Z2 speed, so the retract was not the only changed movement.

A cleaner test held those external moves at 2 mm/s. Four fast-retract/readout pairs did not show large following shifts, while a large outlier occurred in the slow-lift part of the same log. The evidence therefore did not support calling the 5 mm/s symptom the single cause of the intermittent coordinate errors.

This preserved two separate findings: visible motion-associated flicker, and unreliable trigger coordinates that could also occur during slow-lift testing.

## Cold probing, possible filament and a very stable warm run

I then clarified that the recent tests were cold and that some filament might remain on the nozzle. A clean, unloaded nozzle was not confirmed by a returned comparison.

The final returned series was at a reported 220 °C nozzle and 50 °C bed. Ten independent contacts at X200 Y200, each starting from Z2 with 0.5 mm/s probing and 2 mm/s lift, had a total range of **0.00875 mm** around a mean of **-1.390875 mm**. All ten readings were included, not only the last few after conditioning.

That was a verified single-point repeatability result. It did not establish an accurate absolute Z value or a thermal shift relative to the earlier cold runs: an unchanged reference across those sessions, nozzle cleanliness and a controlled temperature-return cycle were not established.

## Last supported outcome

The original LeviQ remained installed. A further hotend replacement had not eliminated the fault, and the exact mechanical or electrical cause remained unresolved. The last warm series demonstrated that the system could still be very repeatable under one set of conditions, but no final homing, multi-point mesh or print result closed the investigation.

No electrical filter, rewiring, permanent driver change or new final calibration is recorded as completed. The diagnostic parameters in the console logs are not a replacement for the [published cfg snapshot](./config/config.md#later-leviq-diagnostics-versus-this-snapshot).

---

# Tuning and hardware-upgrade follow-up

These dated events come from the tuning history and its hardware-upgrade branch in August 2026. Their relative order within that conversation is clear; their order against the separate, undated LeviQ/network follow-ups above is not. They add missing milestones without closing those investigations or identifying the latest cross-branch hotend.

## PLA tuning and the high-flow limit, 2026-08-02 to 2026-08-03

After reporting KAMP working well, I ran a 190–230 °C temperature tower and preferred the appearance at 210–215 °C. Orca's YOLO flow test gave approximately 0.985, compared with the earlier 0.98. These are reported trial results, not an exported final filament profile or a record of which temperature was ultimately selected.

The max-flow trial still looked good around 22 mm³/s, but ended in a temperature-maintenance shutdown while no silicone sock was fitted. The missing sock was my suspected explanation, not a controlled diagnosis; [Issues.md](./Issues.md#temperature-loss-during-the-max-flow-trial) owns that limitation.

The retraction tower was harder to interpret. Around 7–8 mm looked best, with under-extrusion beyond that, but I suspected wet PLA and could not make a confident selection. Drying, a definitive retraction value and the discussed VFA/cornering tests were not reported completed here.

## Extensions and backups

I reported downloading Klipper-Backup and TMC Autotune, then reported placing a config copy on the computer and GitHub and having Klipper-Backup run frequently. This records reported backup activity, not a tested restore or identification of that GitHub copy with the supplied ZIP. No completed TMC Autotune configuration or resulting motor changes were returned; the snapshot still comments out its include.

## USB hub and X/Y Input Shaper, 2026-08-03

The hub arrived and I reported it working, with the Mellow FLY ADXL345 taped firmly to the printhead and enough cable slack across X travel. An unavailable dependency package initially blocked setup. After the revised instructions I reported OK, then supplied separate X and Y calibration logs.

The logs recommended MZV at 59.2 Hz for X and 26.6 Hz for Y. They establish completed analysis, including both values in the final Y output. The full results and the distinction between smoothing estimates, saved configuration and tested printing limits are kept in [Configuration](./config/config.md#input-shaper-and-nozzle-cleaner-versus-this-snapshot).

I explicitly said I had not yet test-printed immediately after calibration. A later "it works" reply followed the cleaner discussion, and referred to Input Shaper as calibrated; it did not provide a before/after ringing comparison or a captured final saved cfg. The later OEM-hotend print report did not supply that comparison either.

## Bed-mounted nozzle cleaner, 2026-08-03

I mounted a spare BBL cleaner outside the printable area and supplied its approximate position. A macro with temporary X-sweep acceleration and restoration afterward was discussed; the next reply reported that it worked. This records initial operation, not confirmation of every suggested macro default or the full start sequence. [Hardware.md](./Hardware.md#bed-mounted-nozzle-cleaner) owns the physical coordinates and their limits; the published macros contain no cleaner implementation.

## From upgrade ideas to recurring faults, 2026-08-03 to 2026-08-14

I moved the discussion toward hardware upgrades while tuning continued in other branches. Direct drive with the original extruder, Revo and Stealthburner were considered, with particular interest in retaining LeviQ and understanding the cost of extra X mass. No conversion was confirmed.

On 2026-08-07 a thermistor problem and an impractical repair attempt made hotend replacement the immediate concern. On 2026-08-09 I reported a new OEM hotend with a silicone sock that seemed to print okay. Later that day I described intermittent skipping and filament grinding despite a nozzle change and tension adjustments. Suspected worn extruder gears remained a hypothesis, and the alternative extruders/motors were not reported installed.

On 2026-08-14 I reported an ordered unit arriving with a 0.09 Ω "short", without the measurement details needed to identify the failed part. The discussion then compared more serviceable replacement hotends and cartridge thermistors. The assistant's preference moved from the preassembled HOCENWAY option to Super Print's bi-metal option after I clarified PLA, PETG and possibly TPU as the intended materials. That recommendation was not a purchase decision or completed installation. The supplied listing specifications and the unconfirmed alternatives are separated from installed hardware in [Hardware.md](./Hardware.md#upgrade-candidates-not-installed-hardware).

---

# 15. Current project status

## Verified

These are established hardware and build milestones. Later probing failures limit the homing/mesh milestones as noted below; they are not all claims of current end-to-end reliability.

- SKR 3 EZ with STM32H723
- Four EZ2209 drivers using UART
- X/Y/Z and extruder motion
- Original X/Y endstops
- Original LeviQ signal identification
- LeviQ reset function and early improvement
- Z homing in the initial working setup; later reliability reopened
- Earlier hotend operation; further replacement and missing final calibration documented
- Heated bed
- Original external MOSFET
- Original fans
- Toolhead LED
- Bed mesh in the initial working setup; later probe reliability reopened
- KAMP adaptive-mesh integration; not a final post-investigation validation
- PID calibration for the earlier hardware setup
- Extruder calibration
- Pressure Advance
- Successful test prints in earlier setups; an initial OEM-hotend print reported on 2026-08-09
- USB hub/accelerometer operation during X/Y shaper calibration
- Completed X/Y shaper analysis; final saved cfg and comparative print outcome not captured
- Bed-mounted nozzle cleaner with initial operation reported, final implementation not captured

## Pending

- Final saved Input Shaper/accelerometer configuration and comparative print validation
- Final nozzle-cleaner/start-sequence version
- Filament runout sensor configuration
- Final documentation photographs
- Raspberry Pi and USB-hub enclosure
- Long-term speed and flow tuning
- Full start-to-finish how-to guide

## Unresolved extrusion and hotend issues

The upgrade branch reported intermittent skipping/grinding after the initially successful OEM-hotend replacement, followed by a faulty delivered-unit report. No final cause, chosen upgrade or calibrated replacement closes those reports. Neither the max-flow trial nor the inconclusive retraction tower establishes a final filament profile. These findings supplement the separate LeviQ investigation below.

## Unresolved LeviQ issue

Intermittent trigger-coordinate shifts and later false-trigger/failed Z homing remain open. The 220/50 °C ten-contact result verifies single-point repeatability only. No confirmed electrical or mechanical root cause, final homing configuration, new mesh or successful concluding print was returned. Detailed evidence is in [Issues.md](./Issues.md#intermittent-trigger-shifts-after-the-initial-fixes).

## Unresolved host/network issue

Later remote-access outages remain unclosed in this branch. Working USB MCU communication and the successful printer tests above do not constitute a long-term Wi-Fi/LAN reliability test.

---

# Some lessons learned

## Verify the physical hardware

Documentation for a related board revision is not proof of the hardware installed on the machine.

The MCU marking on the board settled the H723/H743 question immediately.

## UART success does not prove motor wiring

`DUMP_TMC` confirms driver communication.

It does not confirm:

- Correct coil pairs
- Correct motor connector
- Correct motor direction
- Mechanical freedom

## Preserve motor coil pairs

Stepper motors became simple once the two original coil pairs were treated as indivisible groups.

## Combined harnesses must be traced physically

Connector names were not enough.

The X harness carrying both X and extruder motors was the clearest example.

## A digital probe can require analog thinking

The LeviQ output was digital, but its behavior depended on:

- Load-cell preload
- Mechanical cover clearance
- Reset timing
- Gantry geometry
- Cable and Bowden forces

Later investigation also recorded motion-speed-dependent indicator behavior and uncontrolled nozzle/temperature conditions. These did not establish a unique mechanical or electrical cause. A tight cluster at one point is a repeatability result, not proof of absolute accuracy or a permanent fix.

## Do not hot-plug connectors

Motor connectors were only changed with printer power off.

The Raspberry Pi power arrangement then created a different problem: repeated hard shutdowns.

Solving one safety problem can create another if the full system is not considered.

## Record failures while they are fresh

The incorrect assumptions were often more useful to document than the final pin number.

A final config says what works.

A journey explains why.

---

> [!NOTE]
> This document was drafted with substantial assistance from an LLM, based on my own measurements, testing, research and findings.
