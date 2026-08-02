# Configuration files

This directory contains the Klipper configuration used by this build.

The configuration is split by function so that wiring, motion, heating, probing and macros can be reviewed independently.

> [!WARNING]
> These files are a verified reference for one specific printer.
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

Klipper will probably fail to load if an included file does not exist.


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

The generated block was removed from the repository so another printer does not inherit calibration values from this machine.

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

They are working values for this machine, not *guaranteed* limits for another printer.


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

The current values are conservative working values, not universal recommendations:

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

PID values are not included in the repository as they are printer specific. Run bed PID calibration and save your own result.


## `fans.cfg`

This file defines:

- Part-cooling fan on `PB7`
- Hotend fan on `PB6`

The current values are:

```ini
kick_start_time: 0.5
off_below: 0.10
```

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

Without it, the load-cell trigger point drifted severely.

### Probe sampling

The current setup uses:

```ini
samples: 2
samples_result: median
sample_retract_dist: 5
samples_tolerance: 0.05
samples_tolerance_retries: 3
```

These values were stable on this machine after adding the reset pulse.

### Safe Z home

```ini
home_xy_position: 200, 200
```

This location is appropriate for the current measured bed coordinates but should be checked on another printer.

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

### LED startup macro

The delayed LED macro runs after the Klippy host process has started and loaded the configuration.

It provides a visible indication that Klipper is running when no physical screen is connected.

### NOTE
> It does not indicate that Raspberry Pi OS has finished every background task or that the entire printer has passed a self-test.  
> My testing reveals a roughly 1 second delay from light on, to mainsail initializing.


## `KAMP_Settings.cfg`

This file enables:

- Adaptive meshing
- Adaptive line purge
- Smart Park

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
  
