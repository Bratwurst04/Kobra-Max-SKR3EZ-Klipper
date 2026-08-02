# Project journey

This document records the development of the Anycubic Kobra Max to SKR 3 EZ Klipper conversion.

It is not a replacement for:

- [Wiring.md](./Wiring.md)
- [Configuration documentation](./config/config.md)
- [Issues and troubleshooting](./Issues.md)

Instead, it explains how the final solution was discovered, including wrong assumptions, failed approaches and the tests that changed the direction of the project.

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
| 8. Tuning | Could print quality be improved without changing hardware? | PID, extrusion and Pressure Advance were calibrated; Input Shaper remains pending |
| 9. Documentation | How could the discoveries be preserved? | GitHub repository created with wiring, config and issue documentation |

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
| The original strain gauge might need replacement | The original LeviQ works after identifying probe and reset signals |
| `SCL` and `SDA` probably formed an I²C bus | They function as probe output and reset in this build |
| SKR 3 EZ probably used STM32H743 | The physical board uses STM32H723 |
| The X connector only carried X-axis functions | It also carries the Bowden extruder motor and other signals |

These corrections became the main reason to document the conversion rather than publish only the final cfg files.

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
- Chassis grounding

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

The Pi hostname changed during reinstallations, but the hostname itself has no effect on printer functionality.

## Missing Python dependency

Klipper initially failed to start with:

```text
ModuleNotFoundError: No module named 'greenlet'
```

Reinstalling the Klipper Python requirements into the Klipper virtual environment fixed the service.

This was separate from the printer wiring and firmware.

## Hard shutdown failure

During motor testing, the Pi was powered through the printer.

Every time the printer was switched off to avoid hot-plugging motor connectors, the Pi also lost power.

After several cycles:

- Ping still worked
- SSH returned `Connection refused`
- Moonraker was unreachable
- Mainsail was unreachable

The host installation was re-created.

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

This proved that Klipper firmware was running even though the SD-card filename did not provide the expected confirmation.

## MCU serial path

Klipper required the complete path:

```text
/dev/serial/by-id/usb-Klipper_stm32h723xx_<complete-id>-if00
```

Using a shortened example path caused MCU connection failure.

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

It was not possible to configure only X and postpone Y/Z while keeping Cartesian kinematics active.

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

The motor coil groups had been interpreted incorrectly.

TMC UART still worked, which made the fault appear more complicated than it was.

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

Input Shaper is still pending in the current documentation snapshot.

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

# 15. Current project status

## Verified

- SKR 3 EZ with STM32H723
- Four EZ2209 drivers using UART
- X/Y/Z and extruder motion
- Original X/Y endstops
- Original LeviQ probe
- LeviQ reset
- Z homing
- Hotend
- Heated bed
- Original external MOSFET
- Original fans
- Toolhead LED
- Bed mesh
- KAMP adaptive mesh
- PID calibration
- Extruder calibration
- Pressure Advance
- Successful test prints

## Pending

- Mellow FLY ADXL345 USB integration
- Input Shaper
- Filament runout sensor configuration
- Final documentation photographs
- Raspberry Pi and USB-hub enclosure
- Long-term speed and flow tuning
- Full start-to-finish how-to guide

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

## Do not hot-plug connectors

Motor connectors were only changed with printer power off.

The Raspberry Pi power arrangement then created a different problem: repeated hard shutdowns.

Solving one safety problem can create another if the full system is not considered.

## Record failures while they are fresh

The incorrect assumptions were often more useful to document than the final pin number.

A final config says what works.

A journey explains why.

---

>[!NOTE]
>This document was written with heavy use from LLM, but based on my research and findings
