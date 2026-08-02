# Issues and troubleshooting notes

This document records problems encountered while converting the Anycubic Kobra Max to an SKR 3 EZ.

The goal is not only to list the final fix, but also to preserve the symptoms that made each fault confusing.

> [!NOTE]
> These fixes were verified on this machine.
>
> For connector and pin details, use [Wiring.md](./Wiring.md) as a reference.

## Quick index

| Issue | Main symptom | Resolution |
|---|---|---|
| LeviQ not reset | Probe values drift or trigger before touching the bed | Pulse PB14 LOW then HIGH before every probe attempt |
| Printhead cover touching load cell | Random probe triggers | Increase clearance around the LeviQ load-cell screws |
| Z motors fighting each other | Loud Z motion, barely moves in one direction | Reverse one coil pair on one Z motor |
| Mixed motor and signal wires | TMC shutdown or motor does not move | Re-map the complete harness and preserve coil pairs |
| Extruder UART on wrong pin | `Unable to read tmc uart 'extruder' register IFCNT` | Use E0 UART pin PC6 |
| Bed thermistor on wrong header | Invalid or missing bed temperature | Use TB / PA1 instead of TH1 |
| Raspberry Pi hard power loss | Ping works but SSH and Moonraker refuse connections | Reinstall corrupted Pi OS and avoid repeated hard shutdowns |


---

<details>
<summary><h1>LeviQ probe</h1></summary>

## Probe values drifted between measurements

### Symptoms

- The probe triggered at very different Z positions between attempts
- Some probe attempts triggered before the nozzle touched the build surface
- `PROBE_ACCURACY` initially showed very large ranges and steady drift

### Cause

The original LeviQ board requires its zero point to be reset before probing.

The probe output alone was functional, but without a reset the trigger point accumulated an offset between attempts.

### Fix

The original `SDA` wire was identified as the LeviQ reset input and connected to `PB14` through the logic-level converter.

The reset line is pulsed before every probe attempt:
```gcode
SET_PIN PIN=leviq_reset VALUE=0
G4 P100
SET_PIN PIN=leviq_reset VALUE=1
G4 P600
```
This is executed from the probe `activate_gcode`.

### Verification

After adding the reset pulse:

- Z homing became reliable
- Repeated probe measurements stabilized
- Bed meshing became repeatable

Related configuration:

- [`leviq_probe.cfg`](./config/leviq_probe.cfg)
- [LeviQ wiring](./Wiring.md#leviq-strain-gauge-probe)

</details>

---

<details>
<summary><h1>Printhead cover caused false triggers</h1></summary>

### Symptoms

- Probe status changed without the nozzle touching the bed
- Results appeared random even though the electrical signal was correct
- Very little external force was required to trigger the load cell

### Cause

The replacement 3D-printed printhead cover had insufficient clearance around the LeviQ load-cell screws.

When the cover was tightened, it applied a small mechanical force to the load cell.

### Fix

The printhead cover was modified to provide substantially more clearance around the LeviQ assembly.

### Verification

After modifying the cover:

- The probe returned consistently to its idle state
- False triggers stopped
- Probe repeatability improved

> [!TIP]
> If the probe behaves differently after tightening the printhead cover, remove the cover and test again before changing the electrical configuration.

</details>

---

<details>
<summary><h1>Stepper motors</h1></summary>

<details>
<summary><strong>Z motors moved against each other</strong></summary>

### Symptoms

- Each Z motor worked correctly when tested individually
- With both Z motors connected, Z motion was loud and weak
- The gantry barely moved in one direction
- The top synchronization belt skipped teeth
- The X gantry became skewed by several millimetres

### Cause

One Z motor connector had the opposite electrical direction.

The two motors share one TMC2209 driver and are also mechanically linked by the top belt, so they fought each other.

### Fix

One coil pair on one Z motor connector was reversed.

For example, swap either:

```text
1A ↔ 1B
```

or:

```text
2A ↔ 2B
```

Do not swap both pairs, because that leaves the motor direction unchanged.

### Verification

After reversing one coil pair:

- Both motors moved smoothly together
- Z motion worked in both directions
- `STEPPER_BUZZ STEPPER=stepper_z` completed normally

The gantry was then mechanically re-levelled as closely as practical.

</details>

---

<details>
<summary><strong>Motor wires were mixed with signal wires</strong></summary>

### Symptoms

- TMC drivers reported faults or shut Klipper down
- Motors did not move, vibrated or appeared disconnected
- UART communication still worked, making the fault look like a configuration problem

### Cause

The original harness connectors contain both motor wires and non-motor signals.

While transferring the mostly black wires to new connector housings, the original connector orientation was interpreted incorrectly and unrelated wires were mixed into motor connectors.

The X harness was particularly confusing because it carries:

- X motor
- Bowden extruder motor
- X endstop
- Filament runout sensor
- Chassis-ground wire

### Fix

Every harness was mapped again from the original board labels and traced to its physical destination.

Motor coil pairs were verified with a multimeter before being inserted into the SKR connector housings.

### Verification

After correcting the mapping:

- `DUMP_TMC` worked for every configured driver
- X, Y, Z and extruder passed `STEPPER_BUZZ`
- TMC shutdown faults stopped

See [Wiring.md](./Wiring.md) for the final connector mapping.

</details>

---

<details>
<summary><strong>Extruder TMC UART used the E1 pin</strong></summary>

### Symptom

Klipper reported:

```text
Unable to read tmc uart 'extruder' register IFCNT
```

### Cause

The extruder motor was physically connected to the SKR `E0M` output, but the configuration initially used the E1 UART pin.

### Fix

Use the complete E0 pin set:

```text
Step:   PD15
Dir:    PD14
Enable: PC7
UART:   PC6
```

### Verification

After changing the UART pin to `PC6`:

- `DUMP_TMC STEPPER=extruder` returned valid registers
- `STEPPER_BUZZ STEPPER=extruder` worked normally

</details>
</details>

---

<details>
<summary><h1>Temperature sensors</h1></summary>

## Bed thermistor connected to TH1

### Symptoms

- Bed temperature was invalid or missing
- The configured sensor pin did not match the physical header

### Cause

The bed thermistor connector was inserted into `TH1`.

The current configuration uses the dedicated `TB` input.

### Fix

Move the bed thermistor to:

```text
TB / PA1
```

### Verification

After moving the connector:

- Bed temperature showed a plausible room-temperature value
- Bed heating and PID calibration worked

</details>

---

<details>
<summary><h1>Raspberry Pi host</h1></summary>

## Ping worked but SSH and Moonraker did not

### Symptoms

- The Raspberry Pi still replied to ping
- SSH returned `Connection refused`
- Mainsail and Moonraker were unreachable
- Rebooting did not restore the services

### Cause

During wiring tests, the Raspberry Pi was powered from the printer and lost power every time the printer was switched off.

Repeated hard power cuts corrupted the Raspberry Pi installation.

### Fix

Raspberry Pi OS Lite, Klipper, Moonraker and Mainsail were reinstalled.

The Pi should be shut down cleanly before power is removed:

```bash
sudo poweroff
```

### Prevention

During development, avoid switching off the printer while the Pi is writing to its SD card.

A separate or controlled 5 V supply is preferable when repeated power cycling is expected.

</details>
