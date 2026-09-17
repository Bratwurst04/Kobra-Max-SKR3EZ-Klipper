# Configuration files

This directory contains the Klipper configuration used by this build.

The configuration is split by function so that wiring, motion, heating, probing and macros can be reviewed independently.

> [!WARNING]
> These files are a machine-specific reference snapshot. Initial functional tests were successful, but later LeviQ repeatability and Z homing were reopened; this is not a validated final fix.
>
> Do not copy them blindly. MCU identifiers, paths, movement limits, motor directions, temperatures, PID values, Z offset, mesh limits, motor currents and extrusion calibration may differ on another machine.

> [!NOTE]
> Some comments inside the cfg files are currently written in Swedish.

## Configuration overview

| File | Purpose |
|---|---|
| [`printer.cfg`](./printer.cfg) | Main file, MCU connection, includes and global printer limits |
| [`steppers.cfg`](./steppers.cfg) | X/Y/Z and extruder motion, endstops and extruder heater definition |
| [`tmc.cfg`](./tmc.cfg) | TMC2209 UART pins and current settings |
| [`bed.cfg`](./bed.cfg) | Heated-bed output, thermistor and temperature limits |
| [`fans.cfg`](./fans.cfg) | Part-cooling and hotend fans |
| [`leviq_probe.cfg`](./leviq_probe.cfg) | LeviQ reset, probe, safe Z home and bed mesh |
| [`accessories.cfg`](./accessories.cfg) | Toolhead LED output |
| [`macros.cfg`](./macros.cfg) | Print-start and LED startup macros |
| [`KAMP_Settings.cfg`](./KAMP_Settings.cfg) | KAMP adaptive mesh, purge and Smart Park settings |

The physical pin mapping is documented separately in [Wiring.md](../Wiring.md).

## Published snapshot and host state

The cfg files preserve a machine-specific reference, not a self-contained, immediately runnable installation. A static review of this supplied snapshot found the following distinctions:

| Area | What is actually in the snapshot | Documentation consequence |
|---|---|---|
| MCU identity | `printer.cfg` contains a placeholder serial path | The working USB identity from the build must be supplied locally |
| Mainsail include | `[include mainsail.cfg]` is active, but that file is not bundled here | It belongs to the host-side installation |
| KAMP | The include in `printer.cfg` is commented out; `KAMP_Settings.cfg` references an external `KAMP/` directory not present here | Verified KAMP use on the machine is not the same as KAMP being enabled by this snapshot |
| Start macro | `PRINT_START` calls `SMART_PARK` and `LINE_PURGE` | Those commands depend on the matching external KAMP setup being available |
| Probe offset | `leviq_probe.cfg` contains only `#z_offset: 0`; no generated offset block is included | The distributed files do not supply the required effective probe offset |
| Heater PID | Numeric PID values remain in `steppers.cfg` and `bed.cfg`, labelled as starting values | They are not absent, and are not a substitute for the machine's own calibration |
| Optional extensions | TMC Autotune is commented out and its cfg is not bundled | Download was reported; no completed autotune configuration was returned |
| Input Shaper | No accelerometer MCU, `[adxl345]`, `[resonance_tester]` or `[input_shaper]` section is included | X/Y calibration completed in the conversation, but that live setup and saved values are absent here |
| Nozzle cleaner | No `NOZZLE_WIPE` macro or call is present | Initial operation was reported after a proposed macro; the final applied version is not in this snapshot |
| Later LeviQ diagnostics | Probe speed, lift, retract and sampling overrides in the returned logs differ from these files | See [published-versus-tested settings](#later-leviq-diagnostics-versus-this-snapshot); no cfg update or final reliable homing/mesh sequence is established |
| Later safe Z home | Snapshot still has `home_xy_position: 200, 200` | A later live change to approximately X208 Y223 and a new warm Z-offset were reported; this snapshot was not edited to match |
| Later hotend sensor profile | Snapshot already uses `EPCOS 100K B57560G104F` | A live `Generic 3950` trial caused severe extrusion resistance at displayed 210 °C and was reverted to EPCOS-style curve; retuning remained incomplete |
| Filament runout | `accessories.cfg` contains only the toolhead LED | PC2 wiring is mapped and a start check/automatic pause was planned, but no functional runout test was returned |
| Later motion tuning | Snapshot still uses X/Y 64, Z/E 16 microsteps and 0.70/0.40 A run/hold currents on XYZ | A later live experiment used X/Y/Z/E run currents about 0.80/0.90/0.80/0.80 A, 64/64/32/32 microsteps, no separate hold current, X/Y interpolation enabled and Z/E interpolation disabled; not written into this snapshot |
| Later pause/cancel behavior | Snapshot does not contain the custom recovery macros | User reported improved behavior after changing pause/cancel parking for position-loss cases; exact final live macro was not returned |

The required `z_offset` is confirmed by the [upstream probe reference](https://www.klipper3d.org/Config_Reference.html#probe), checked during this documentation review. No cfg file was changed and no startup test of the distributed snapshot was performed as part of this update.

### Input Shaper and nozzle cleaner versus this snapshot

The 2026-08-03 user-pasted calibration output records:

| Axis | Recommended shaper | Frequency | Fitted smoothing | Suggested maximum acceleration to avoid excessive smoothing |
|---|---|---:|---:|---:|
| X | MZV | 59.2 Hz | ~0.058 | ≤10300 mm/s² |
| Y | MZV | 26.6 Hz | ~0.288 | ≤2100 mm/s² |

The console identified `/tmp/calibration_data_x_20260803_115007.csv` and `/tmp/calibration_data_y_20260803_120122.csv`. These are historical output paths; the CSV files themselves are not included in the supplied repo or readable attachments. The final Y output listed both X and Y values with `damping_ratio: 0.100000` per axis. These are fitted shaper results, not independently measured structural eigenfrequencies or validated motor/print acceleration limits.

The X log said `SAVE_CONFIG` would update the configuration; that message is not confirmation that it was subsequently run. The conversation described Input Shaper as calibrated, but did not return a complete saved live cfg or a comparative print. Likewise, `max_accel: 1800` and `square_corner_velocity: 5` were suggested after calibration, not explicitly confirmed final settings. The published `printer.cfg` retains `max_accel: 3000` and no explicit `square_corner_velocity`; neither file is changed here.

The proposed accelerometer file and pin example are not reproduced as an installed configuration because no complete final file was returned. Calibration success establishes that a live setup worked during those tests, not that the supplied ZIP contains it. The Y sensor relocation was instructed but its final physical mounting was not separately reported.

A `NOZZLE_WIPE` macro and a revised `PRINT_START` were subsequently proposed for the [bed-mounted cleaner](../Hardware.md#bed-mounted-nozzle-cleaner). The next user reply reported success, but did not return the actual final macro or which defaults/test command were used. Initial operation is supported; exact wiping height, acceleration, pass count, restoration behavior and full start-sequence validation remain undocumented. The published `macros.cfg` has neither that macro nor its calls, and no new cfg file is supplied in this documentation package.

### Extrusion and thermal reports versus this snapshot

The PLA tower preference of 210–215 °C, approximate YOLO flow result of 0.985 and inconclusive 7–8 mm retraction observation belong to the [tuning history](../Journey.md#tuning-and-hardware-upgrade-follow-up). They do not replace `rotation_distance: 22.350`, `gear_ratio: 3:1` or `pressure_advance: 0.44` in this published snapshot.

A later Bowden-tube replacement was followed by a reported Pressure Advance result around **0.8**. Another hotend/thermistor change then reopened extrusion calibration, so neither 0.44 nor 0.8 should be presented as a universal or final current value. No final Orca filament profile is bundled here.

The published hotend still uses `sensor_type: EPCOS 100K B57560G104F`. The OEM replacement report and the later product comparisons do not establish the identity/curve of the final installed sensor. An advertised NTC100K value alone is not confirmation of this exact configured sensor model. No proposed cartridge sensor, heater wattage, PID value, temperature limit or post-replacement Z offset was applied to these cfg files.


A later live comparison tested `Generic 3950`. At a displayed 210 °C the extruder struggled badly enough to grind filament; after reverting to the published `EPCOS 100K B57560G104F` curve, stationary extrusion at the same displayed temperature was reported as much better. This is a functional rejection of `Generic 3950` for the current installed sensor, not proof of the thermistor's exact physical part number.

The same follow-up reported moving safe Z home to about X208 Y223, completing a warm Z-offset, and disabling TMC Autotune because the available `ldo-42sth48-2504ah` motor profile was not verified for the installed OEM motor. The supplied snapshot already comments out the autotune include. Final PID, flow, max-flow and PA retests after the thermistor rollback were still pending.

Klipper-Backup activity and a separate computer/GitHub config copy were reported, but no restore test or exact backup revision was supplied. Those reports do not make this ZIP the final live backup or provide its missing host-side files.

### Host startup versus printer configuration

The early `ModuleNotFoundError: No module named 'greenlet'` occurred in `/home/admin/klippy-env/bin/python` before Klippy could process the configuration. Restoring the host's Python dependencies resolved that reported startup failure; changing MCU pins or rebuilding the SKR firmware was not the repair. See [the recorded issue](../Issues.md#klippy-exited-before-creating-its-log).

Wi-Fi profiles, autoconnect settings and power-saving settings are host/network configuration, not `printer.cfg` settings. Their later unresolved history is documented in [Issues.md](../Issues.md#recurring-network-latency-and-loss-of-access); no working NetworkManager profile or `moonraker.conf` is included in this cfg snapshot.

---

<details>
  <summary><strong>Before using the configuration</strong></summary>

## 1. Change the MCU serial path

The value in `printer.cfg` is only a placeholder:

```ini
[mcu]
serial: /dev/serial/by-id/usb-Klipper_stm32h.........
```

Find the correct path on your own Raspberry Pi:

```bash
ls -l /dev/serial/by-id/
```

Copy the complete result into `printer.cfg`.

Do **not** shorten the identifier.


## 2. Change the virtual SD-card path

This path is user-specific:

```ini
[virtual_sdcard]
path: /home/admin/printer_data/gcodes
```

Replace `admin` if your Linux username or Moonraker data path is different.


## 3. Disable unavailable extensions

Comment out includes for extensions that are not installed.

For example:

```ini
#[include KAMP_Settings.cfg]
#[include autotune_tmc.cfg]
```

Klipper will fail to load if an included file does not exist.


## 4. Verify board and MCU revision

This build uses:

```text
BIGTREETECH SKR 3 EZ
STM32H723
```

Do **not** assume that firmware or pin mappings for an STM32H743 board are identical.

</details>

---

<details>
  <summary><h1>Machine-specific values</h1></summary>
The following values should be measured or calibrated on each printer.

| Setting | File | Why it varies |
|---|---|---|
| MCU serial path | `printer.cfg` | Unique MCU identifier |
| Virtual SD-card path | `printer.cfg` | Linux username and installation |
| `position_min` / `position_max` | `steppers.cfg` | Actual mechanical travel |
| Motor direction `!` | `steppers.cfg` | Connector orientation and motor wiring |
| Extruder `rotation_distance` | `steppers.cfg` | Extruder and filament path |
| `pressure_advance` | `steppers.cfg` | Extruder, Bowden tube, material and temperature |
| TMC `run_current` | `tmc.cfg` | Motor model, load and cooling |
| Heater PID values | Saved configuration | Heater, sensor, airflow and mounting |
| `min_temp` / `max_temp` | `steppers.cfg`, `bed.cfg` | Environment and safe hardware limits |
| Probe `z_offset` | Saved configuration | Nozzle, hotend and load-cell mounting |
| `mesh_min` / `mesh_max` | `leviq_probe.cfg` | Measured printable area |
| Bed mesh | Saved configuration or KAMP | Bed shape and operating temperature |
| KAMP margins and purge values | `KAMP_Settings.cfg` | Printable area and purge preference |


### IMPORTANT
> `mesh_min` and `mesh_max` are machine-specific.
>
> The current values match the measured printable area of this machine, not the full mechanical travel area.
>
> It currently matches with the white painted square on the buildplate.

</details>

---

<details>
  <summary><h1>Generated calibration data</h1></summary>

  Klipper writes calibrated values below a block similar to:

```ini
#*# <---------------------- SAVE_CONFIG ---------------------->
#*# DO NOT EDIT THIS BLOCK OR BELOW. The contents are auto-generated.
```

This block may contain:

- PID values
- Probe Z offset
- Bed-mesh profiles
- Other saved calibration data

The generated block was removed from the repository so another printer does not inherit calibration values from this machine. This does not remove all numeric calibration-related settings: heater PID starting values remain in the ordinary sections, as detailed below. The omitted probe offset also means the published files alone lack a required value.

Run the required calibrations and use:

```gcode
SAVE_CONFIG
```

to generate your own values.

### NOTE
> If I mistakenly don't remove the generated block, it's *still* printer specific

</details>

---

<details>
  <summary><h1>File notes</h1></summary>

## `printer.cfg`

This is the main configuration file.

It:

- Includes the functional cfg files
- Defines the MCU serial path
- Defines global kinematics and speed limits
- Defines the virtual SD-card path
- Enables pause/resume, display status and object exclusion

Review these values before use:

```ini
max_velocity: 300
max_accel: 3000
max_z_velocity: 10
max_z_accel: 100
```

These are the published snapshot values, not validated final print limits. The later Input Shaper results and the unconfirmed suggestion to lower global acceleration are recorded [above](#input-shaper-and-nozzle-cleaner-versus-this-snapshot).

The first X-only bring-up attempt with `kinematics: cartesian` failed because complete X/Y/Z sections were required. The subsequent early test log used 16 microsteps for X/Y, X/Y travel limits of 400, and a temporary `PC0` Z-endstop entry while homing was postponed. Those historical settings do not replace the later values in the published `steppers.cfg`, including its LeviQ virtual endstop.


## `steppers.cfg`

This file contains:

- X-axis motion and endstop
- Y-axis motion and endstop
- Z-axis motion using LeviQ as the virtual endstop
- Extruder motor
- Extruder heater and thermistor
- Extrusion calibration values

### Motor direction

A `!` inverts a pin:

```ini
dir_pin: !PD3
```

If an axis moves in the wrong direction, verify the wiring first and then add or remove `!` from the direction pin if appropriate.

Do **not** change stepper connector wiring while the printer is powered.

### Extruder calibration

The included values are calibrated for the current original Bowden extruder:

```ini
rotation_distance: 22.350
gear_ratio: 3:1
pressure_advance: 0.44
```

Treat them as a starting point only.

Recalibrate:

- `rotation_distance` for the actual extruder
- Pressure Advance for the filament and print temperature

The file also contains:

```ini
max_extrude_cross_section: 5
```

This is required by the current KAMP line-purge setup, *YMMV*.

### Extruder temperature limits

```ini
min_temp: 18
max_temp: 280
min_extrude_temp: 170
```

These are Klipper safety limits, not normal operating temperatures.

`min_temp: 18` may be too high for a cold garage or workshop and can prevent Klipper from starting.


## `tmc.cfg`

This file defines the four EZ2209 UART connections and their current settings.

The current values are machine-specific working values, not universal recommendations or independently verified motor-current ratings:

```ini
run_current: 0.70
hold_current: 0.40
```

The extruder currently uses:

```ini
run_current: 0.80
hold_current: 0.40
```

Verify motor temperatures and tune currents for the actual motors and mechanical load.

The early test log used `run_current: 0.80` for X/Y and `0.90` for Z with `stealthchop_threshold: 999999`. The published file instead uses `stealthchop_threshold: 0` and the values above. This update preserves the published settings rather than restoring the earlier test configuration. The single Z-driver entry controls both parallel outputs; it does not provide independently adjustable current or direction for each Z motor.


A still later live experiment moved to roughly 0.80 A X, 0.90 A Y, 0.80 A Z and 0.80 A extruder, removed the separate hold-current settings, used 64 microsteps on X/Y and 32 on Z/E, and ended with interpolation enabled on X/Y but disabled on Z/E. X/Y interpolation had briefly been disabled when a high-pitched motor sound was noticed. The later values were used during aggressive acceleration testing but were not returned as a complete final cfg snapshot, so the files distributed here remain unchanged.

The same testing reached about 90,000 mm/s² at a 200 mm/s slicer speed cap before Y skipped steps at 100,000 mm/s². That is a short limit-finding result, not a quality recommendation or a replacement for the much lower Input Shaper smoothing guidance.

The configured UART pins are verified for the current SKR layout:

| Driver | UART pin |
|---|---:|
| X | `PD5` |
| Y | `PD0` |
| Z | `PE1` |
| E0 | `PC6` |


## `bed.cfg`

This file defines:

- Bed MOSFET control through `PD7`
- Bed thermistor through `PA1`
- Thermistor type
- Temperature safety limits

The current limits are:

```ini
min_temp: 18
max_temp: 100
```

The original Anycubic operating limit is lower than the configured Klipper shutdown limit.

The configured maximum is intended as a fault cutoff, not a target temperature.

As with the extruder, `min_temp: 18` may be unsuitable in a cold environment.

The file contains PID starting values, despite the earlier documentation saying they were absent:

```ini
pid_Kp: 70
pid_Ki: 1
pid_Kd: 1000
```

Likewise, `steppers.cfg` contains extruder PID starting values `33.01`, `3.25` and `83.84` for Kp, Ki and Kd. Both files label these values as machine-specific starting values. The removed generated calibration block does not make these ordinary-section values disappear or prove them to be the final live calibration.


## `fans.cfg`

This file defines:

- Part-cooling fan on `PB7`
- Hotend fan on `PB6`

The part-cooling `[fan]` section currently uses:

```ini
kick_start_time: 0.5
off_below: 0.10
```

Both configured fans use `kick_start_time: 0.5`; `off_below: 0.10` is explicitly set only for the part-cooling fan.

They work on this machine but can be adjusted if a fan:

- Fails to start at low duty cycle
- Stalls
- Runs when it should be off
- Needs a longer startup pulse


## `leviq_probe.cfg`

This file defines:

- LeviQ reset output on `PB14`
- Probe input on `PB15`
- Probe sampling settings
- Safe Z home
- Bed-mesh limits
- LeviQ reset before every probe attempt

The reset sequence is essential for this build:

```gcode
SET_PIN PIN=leviq_reset VALUE=0
G4 P100
SET_PIN PIN=leviq_reset VALUE=1
G4 P600
```

Without it, the load-cell trigger point drifted severely in the initial investigation. Later failures despite reset experiments showed that this early improvement was not a complete resolution of the intermittent fault.

### Probe sampling

The published snapshot uses:

```ini
samples: 2
samples_result: median
sample_retract_dist: 5
samples_tolerance: 0.05
samples_tolerance_retries: 3
```

These values describe the earlier snapshot associated with successful tests after adding reset. They are not a verified final cure for the later recurrence; the returned diagnostic commands used different overrides.

### Safe Z home

The published snapshot contains:

```ini
home_xy_position: 200, 200
```

A later live change moved the home point to approximately:

```ini
home_xy_position: 208, 223
```

after measuring the practical bed centre. A new warm Z-offset was also reported. The cfg file in this repository remains unchanged, so the later value is documented here as a live-machine follow-up rather than silently written into the published snapshot.

### Later LeviQ diagnostics versus this snapshot

**Status: published values preserved; branch-specific live settings and console overrides are documented separately.** The later branch reopened probe repeatability and Z homing. Its single-point successes do not make this directory a validated final fix.

The troubleshooting handover described the then-current probe configuration approximately as `speed: 1`, `lift_speed: 5`, `samples: 3`, median sampling, `sample_retract_dist: 1`, `samples_tolerance: 0.03`, `samples_tolerance_retries: 4`, with a 200 ms LOW reset pulse followed by a 700 ms post-HIGH wait. Those values differ from the cfg snapshot published here and were later overridden explicitly in many console tests.

| Item | Published snapshot | Evidence from the later troubleshooting branch |
|---|---|---|
| Probe input and reset | `^!PB15` and `PB14` | The handover retained the same mapping; no replacement pin mapping or completed rewiring was reported |
| Probe speed | `speed: 3` | The handover described approximately `speed: 1`; returned isolated-contact tests explicitly used `PROBE_SPEED=0.5` |
| Lift speed | `lift_speed: 5` | The handover also described 5; both `LIFT_SPEED=5` and `LIFT_SPEED=2` appear in logs, and the final warm series used 2 |
| Retract distance | `sample_retract_dist: 5` | The handover described 1 mm; diagnostic commands explicitly used `SAMPLE_RETRACT_DIST=1` |
| Sampling | 2 samples, median, tolerance 0.05, 3 retries | The handover described approximately 3 samples, median, tolerance 0.03 and 4 retries; isolated-contact logs explicitly override `SAMPLES=1`. Later retry logs do not establish one final active sampling setup |
| Reset sequence | LOW 100 ms, then HIGH and 600 ms wait | The handover described LOW 200 ms, then HIGH and 700 ms wait. Several timing variants failed to eliminate the fault, and the returned reset-only control explicitly used 200/700 ms |
| Safe Z home | X200 Y200, hop 10 mm at 5 mm/s | Diagnostic contacts used X200 Y200 and several other points. Suggested changed hop/homing settings were not returned as a complete confirmed final cfg |
| Z homing in `steppers.cfg` | First speed 5, second speed 2 mm/s; retract distance 3 mm | Later false-trigger/failed-homing reports reopened reliability. No final successful homing sequence after the speed investigation was supplied |
| Z motion limits | `max_z_velocity: 10`, `max_z_accel: 100`; global `max_accel: 3000` | A dashboard value of 10000 was reported during acceleration tests, not a captured new Z-axis limit. High requested feedrates alone do not establish attained Z speeds |
| Z driver in `tmc.cfg` | Run 0.70 A, hold 0.40 A, interpolation enabled, `stealthchop_threshold: 0` | Enable-only tests produced no observed LED blink. Proposed current or driver-mode changes were not confirmed by returned results |
| Debug movement | No `[force_move]` section in `printer.cfg` | Debug positioning was discussed and Z movements were subsequently reported, but the exact applied command/config state is not available as a complete transcript |

The late repeatability logs start each isolated contact at Z2 and use F120 for the separate Z repositioning. They establish the command parameters actually shown, not a permanent edit of `leviq_probe.cfg` or a verified absolute Z reference. Temporary debug coordinates are not documented here as a calibration or a ready-to-use homing workaround.

The speed-dependent LED observation and the failed attempt to explain every offset jump through 5 mm/s retracts are recorded in [Issues.md](../Issues.md#retract-speed-control-did-not-establish-a-complete-fix). The final 220/50 °C result is limited to [one stable single-point series](../Issues.md#cold-nozzle-condition-and-the-final-warm-series).

No probe offset, PID value, mesh profile, motor setting or homing speed was updated in the cfg files as part of this documentation change.

### Bed mesh

The current mesh area is:

```ini
mesh_min: 14,23
mesh_max: 405,423
```

These coordinates represent the intended printable area, not the full physical travel.

The configuration is compatible with KAMP adaptive meshing.


## `accessories.cfg`

This file currently contains the toolhead LED output:

```ini
[output_pin nozzle_led]
pin: PE5
```

The original `LEVE` toolhead wire is used as the LED control signal.

The original filament runout sensor is mapped to `E0-DET / PC2`, but this snapshot does not define a `[filament_switch_sensor]`. A later start-sequence check and automatic pause behavior were proposed, but no returned sensor query or runout event verified the active polarity or completed integration.


## `macros.cfg`

This file contains:

- `PRINT_START`
- Delayed LED startup indication

### Slicer start G-code

The current `PRINT_START` macro expects bed and nozzle temperatures as parameters.

For OrcaSlicer, use:

```gcode
M117
PRINT_START BED=[bed_temperature_initial_layer_single] EXTRUDER=[nozzle_temperature_initial_layer]
```

`M117` is intentionally placed before `PRINT_START` so Moonraker/KAMP object information is available before adaptive meshing begins.

### Temperature sequencing and later reports

The published `PRINT_START` contains `M104 S150` before homing and `M109 S{EXTRUDER}` after `SMART_PARK`. It does not contain a pre-probing `M109 S150` / `G4 P2000` sequence.

The separate live snapshot `config(1).zip`, attached earlier in this troubleshooting chat and inspected there, contained `M109 S150` followed by `G4 P2000` before the second Z home. That is a branch-specific live-machine difference from the cfg files published in this repository. Its exact ordering relative to the later documentation branches is not established, so this documentation records the divergence without silently replacing `macros.cfg`.

The opening handover also reported `extruder not hot enough` after some failed KAMP/mesh attempts. Continuing toward purge was a suspected secondary issue, not a demonstrated control-flow diagnosis or completed macro fix. No cfg or macro was changed in this documentation update.

### Proposed temporary saved-mesh fallback

While the LeviQ root cause remained unresolved, a temporary printability workaround was discussed. The proposed full-bed reserve mesh used **10 samples per point**, median sampling, `PROBE_SPEED=0.5`, `SAMPLE_RETRACT_DIST=1`, `LIFT_SPEED=5`, `SAMPLES_TOLERANCE=0.03` and a substantially higher retry allowance, with 20 retries used as the example. The intended normal-print path was then to load that saved profile instead of requiring a fresh adaptive KAMP mesh on every start.

The purpose of the higher retry count was to give a point more chances to settle while still rejecting a large spread. Simply increasing `samples_tolerance` enough to accept the observed 0.1–0.5 mm or larger shifts was explicitly not the preferred workaround because it could preserve bad measurements in the mesh.

No returned log or updated cfg showed that this reserve profile, the example `TEMP_SAFE` name, ten-sample settings, or an automatic fallback from a failed KAMP calibration to the saved mesh was actually applied. They are therefore documented as a proposal only. The supplied KAMP component source is also not present in this repo snapshot, so this review does not claim how that external wrapper handles every runtime probe parameter.

A later position-loss issue changed pause/cancel behavior outside this snapshot. After skipped XY steps, an absolute park toward the far corner could crash because the logical and physical positions no longer matched. A custom pause/cancel/crash-abort approach was reported as much better, but the final macro body was not returned and is therefore not recreated here.

### LED startup macro

The delayed LED macro runs after the Klippy host process has started and loaded the configuration.

It provides a visible indication that Klipper is running when no physical screen is connected.

### NOTE
> It does not indicate that Raspberry Pi OS has finished every background task or that the entire printer has passed a self-test.  
> My testing reveals a roughly 1 second delay from light on, to mainsail initializing.


## `KAMP_Settings.cfg`

When included, this file enables the external KAMP components for:

- Adaptive meshing
- Adaptive line purge
- Smart Park

The supplied `printer.cfg` currently comments out that include. The external component files are not bundled in this directory; see [snapshot limitations](#published-snapshot-and-host-state).

The settings are working values for the current printable area.

Review:

```ini
variable_mesh_margin
variable_fuzz_amount
variable_purge_height
variable_purge_margin
variable_purge_amount
variable_flow_rate
```

KAMP also requires:

- `[exclude_object]` in `printer.cfg`
- Object labels from the slicer
- Moonraker object processing
- `max_extrude_cross_section` large enough for the selected purge

</details>

---

<details>
  <summary><h2>Validation order</h2></summary>

  After adapting the configuration to another printer, validate in roughly this order:

1. MCU connection
2. TMC UART communication
3. Stepper motion with `STEPPER_BUZZ`, every stepper
4. Endstops with `QUERY_ENDSTOPS`, every endstop
5. Thermistor readings
6. Fans
7. Low-temperature heater tests
8. LeviQ signal with `QUERY_PROBE`
9. Manual LeviQ trigger during the first Z-homing test
10. Probe repeatability
11. Z offset
12. Bed mesh
13. PID calibration
14. Extruder calibration
15. Pressure Advance
16. Input Shaper

Do **not** start with a full print before the safety-critical functions have been verified.
  
</details>
