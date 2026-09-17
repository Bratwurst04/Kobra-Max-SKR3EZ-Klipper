# Issues and troubleshooting notes

This document records problems encountered while converting the Anycubic Kobra Max to an SKR 3 EZ.

The goal is not only to list the final fix, but also to preserve the symptoms that made each fault confusing.

> [!NOTE]
> This page includes verified fixes, partially verified recoveries and unresolved investigations.
> A temporary return of service is not necessarily a confirmed root cause or permanent fix.
>
> For connector and pin details, use [Wiring.md](./Wiring.md) as a reference.

## Quick index

| Issue | Main symptom | Resolution |
|---|---|---|
| LeviQ not reset, initial investigation | Probe values drift or trigger before touching the bed | PB14 reset improved early tests; later recurrence was not fully resolved |
| [Intermittent LeviQ trigger shifts](#intermittent-trigger-shifts-after-the-initial-fixes) | Large same-point deviations mixed with very tight series | Unresolved / reopened; final warm single-point repeatability verified only |
| [Z-motion-associated indicator flicker](#z-motion-associated-indicator-flicker-and-homing-failure) | Strong flicker near a requested 5 mm/s and unreliable Z homing | Speed association observed; mechanical versus electrical cause unconfirmed |
| Printhead cover touching load cell | Random probe triggers | Increase clearance around the LeviQ load-cell screws |
| Z motors fighting each other | Loud Z motion, barely moves in one direction | Reverse one coil pair on one Z motor |
| Mixed motor and signal wires | TMC shutdown or motor does not move | Re-map the complete harness and preserve coil pairs |
| Extruder UART on wrong pin | `Unable to read tmc uart 'extruder' register IFCNT` | Use E0 UART pin PC6 |
| Bed thermistor on wrong header | Invalid or missing bed temperature | Use TB / PA1 instead of TH1 |
| [Hotend thermistor failures](#hotend-thermistor-failures-in-the-upgrade-branch) | Repair difficulty and a reported 0.09 Ω "short" on a delivered unit | OEM replacement briefly printed; final faulty component/setup not established |
| [Generic 3950 profile mismatch](#generic-3950-profile-caused-apparent-underheating) | Displayed 210 °C but filament was very difficult to extrude and was ground by the extruder | Revert to `EPCOS 100K B57560G104F`; manual 210 °C extrusion improved, exact thermistor identity still unverified |
| [Intermittent extrusion failure](#intermittent-extruder-skipping-and-filament-grinding) | Skipping, grinding and eventual loss of extrusion | No confirmed cause or lasting fix |
| [Pause/cancel after skipped XY steps](#pause-or-cancel-could-crash-after-skipped-xy-steps) | Parking move headed toward the far X/Y corner after position was lost | Custom pause/cancel behavior reduced the risk; exact final macro not captured |
| [High-flow thermal shutdown](#temperature-loss-during-the-max-flow-trial) | Shutdown around 22 mm³/s despite acceptable-looking output | Thermal limit encountered; missing sock suspected, no controlled retest |
| [Input Shaper package setup](#input-shaper-dependency-installation) | `libatlas-base-dev` had no installation candidate | Setup subsequently reported OK and calibration completed |
| SD flashing confirmation | `firmware.bin` remains unchanged | Klipper USB identity and later MCU communication verified; SD rename cause unresolved |
| Missing Klippy dependency | Service exits before `klippy.log` is created | Restore the Klippy virtual environment's Python dependencies |
| Raspberry Pi hard power loss | Old IP replies but SSH and Moonraker refuse connections | Reinstallation restored access; corruption and responder identity were not established |
| No Wi-Fi profile shown | `wlan0` disconnected; only loopback listed | Recreate the profile; connection and an autoconnect retest succeeded |
| Recurring network outages | High latency, packet loss and loss of remote access | Unresolved; later tests also found problems from an Ethernet-connected PC |


---

<details>
<summary><h1>LeviQ probe</h1></summary>

## Probe values drifted between measurements

### Symptoms

- The probe triggered at very different Z positions between attempts
- Some probe attempts triggered before the nozzle touched the build surface
- `PROBE_ACCURACY` initially showed very large ranges and steady drift

### Cause

The initial investigation identified missing reset as a contributor to drift. The probe output alone was functional, and introducing a reset improved the measurements.

That result did not prove that every later offset was caused by missing reset. The recurrence and unsuccessful timing variants are documented below.

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

In the initial tests after adding the reset pulse:

- Z homing became reliable
- Repeated probe measurements stabilized
- Bed meshing became repeatable

Related configuration:

- [`leviq_probe.cfg`](./config/leviq_probe.cfg)
- [LeviQ wiring](./Wiring.md#leviq-strain-gauge-probe)

## Intermittent trigger shifts after the initial fixes

**Status: unresolved / reopened; machine-specific.** The signal mapping and the initial successful prints remain valid milestones. They did not establish lasting probe reliability.

### Symptoms and evidence scope

Later tests alternated between very tight repeated measurements and large trigger-coordinate changes at the **same XY point**. Reported errors included `Probe triggered prior to movement`, `Probe samples exceed tolerance. Retrying...` and `Probe samples exceed samples_tolerance`.

The record below combines the opening troubleshooting handover with the subsequent pasted console logs and my physical-test reports. Clock times identify log excerpts, not calendar dates. The starting conditions were not identical across every session. Absolute Z values from different points, temperatures or homing sessions are not treated as a measurement of the same reference.

Most isolated-contact tests used `SAMPLES=1`, `PROBE_SPEED=0.5`, `SAMPLE_RETRACT_DIST=1` and initially `LIFT_SPEED=5`. Later tests explicitly used `LIFT_SPEED=2`. A console result of `range 0.000000` for **one** sample says nothing about repeatability; the relevant ranges below are calculated across separate contacts.

### What the opening handover had already tested

| Investigation | Reported outcome | Limit on the conclusion |
|---|---|---|
| Bowden completely disconnected | Large errors still occurred | Bowden was not necessary for those failures; this does not describe every later test's assembly state |
| Cover and toolhead connector manipulation | No reproducible trigger from pushing/twisting the cover or harness connector | No demonstrated gross connector fault; small preload or intermittent faults were not excluded |
| Local mechanical changes | Moving heater/thermistor wires aside, very slight tightening of load-cell screws and removing the silicone sock were followed by a temporary improvement | Several things changed; recurrence prevented assigning a lasting fix to one of them |
| Reset timing | A wait before reset, a longer LOW pulse, a double pulse, a 5 s post-reset wait, and one reset before an entire series did not eliminate the problem | The tested timing changes were not sufficient; unwanted electrical resets were not measured |
| Probe speed | Slow probing could reach ranges of 0.0075–0.01125 mm, but large failures also returned at 0.5 mm/s | Good resolution in a stable state did not explain intermittent shifts |
| Acceleration and retract | Reduced acceleration did not remove the fault; 1 mm retract could work well | Neither higher acceleration nor an insufficient retract distance was established as the sole cause |
| Alternating start Z2 and Z0.7 at one point | Results progressed from about -0.2275 toward -0.1000 rather than forming two height-dependent groups | No consistent two-level start-height effect in that comparison |
| Passive waiting | After 30 s without contact, the first measurement was about -0.3475; following contacts were around -0.135 | Waiting alone did not reproduce the improvement associated with repeated contacts |

These were reported historical tests, not new settings applied to the published cfg files.

### Contact-history comparisons

The principal points were:

| Label | X | Y |
|---|---:|---:|
| A | 208 | 223 |
| B | 223.025 | 211.366 |
| C | 208 | 211.366 |
| D+ | 223.025 | 223 |
| D- | 223.025 | 199.732 |

In the opening handover, visiting A without probing it left B near its conditioned level, whereas probing A before returning to B produced B values of -0.30875, -0.22750 and -0.25000 mm after a baseline near -0.09125 mm. This was an A control, not a separate completed C-only control.

The subsequent returned logs added these comparisons:

| Test / log excerpt | B reference before the excursion | B after the other operation | Observation |
|---|---|---|---|
| C contact, 22:53–22:54 | Last three B contacts: -0.10000, -0.10250, -0.10000 | -0.21125, -0.19000, -0.20875 | Mean change -0.10250 mm; changing Y was not required |
| C reset without contact, 22:58 | Last three: -0.09250, -0.09750, -0.09250 | -0.08250, -0.08875, -0.08750 | Mean change +0.00792 mm; the reset-only excursion did not reproduce the large negative shifts |
| D+ contact, 23:01–23:02 | B was still progressing from -0.11750 to -0.08125 | -0.57875, -0.35250, -0.32750 | Large changes occurred without changing X |
| D- contact, 23:04–23:05 | B was still progressing from -0.22625 to -0.10125 | -0.47875, -0.33500, -0.37500 | The other Y direction also produced negative shifts, not a consistent reversal of sign |

The C reset-only sequence explicitly used LOW for 200 ms followed by HIGH and 700 ms waiting. The D baselines were still trending, so they are not represented as fully settled references. The D comparisons weakened a simple opposite-direction rocking explanation but did not exclude the bed or prove a particular toolhead component faulty.

These results supported **history-dependent probing**, often described during the investigation as conditioning or hysteresis. A physical probe cycle also changes Z motion and timing compared with a no-contact visit. The comparisons therefore did not isolate contact force from every other part of the cycle, nor distinguish mechanical hysteresis from electronics responding to load or motion.

### Manual load comparisons

The forces and contact locations in these manual tests were not calibrated. The differences in this table use the **immediately preceding B measurement**, not averages across different sessions.

| Operation / log excerpt | B before | B after | Change |
|---|---:|---:|---:|
| First bed-only finger pressure, 23:08–23:09 | -0.16375 | -0.22250 | -0.05875 mm |
| Later no-touch excursion, 23:11 | -0.10750 | -0.12000 | -0.01250 mm |
| Later bed-only finger pressure, 23:12 | -0.09625 | -0.10875 | -0.01250 mm |
| Upward nozzle load without bed contact, 23:15–23:16 | -0.09625 | -0.17250 | -0.07625 mm |
| Load on the rigid carriage/body instead, 23:18 | -0.11750 | -0.10625 | +0.01125 mm |

The first bed test started from a visibly changing baseline. The later finger and no-touch excursions had the same last-reading difference, so they did not reproduce a large bed-only effect. The nozzle test shifted more than the carriage test, which kept the local hotend/load-cell force path under suspicion. These single manual comparisons did **not** clear the bed, carriage or electronics conclusively. A proposed direct load on the moving load-cell mount was not reported as completed.

### Leakage, tightening and a further hotend replacement

I reported leakage at the nozzle and up near the heatbreak, and tightened the nozzle. Later I replaced the hotend again, but the probing problem remained. The exact replacement model and any post-replacement PID or Z-offset calibration were not supplied in this branch. See [Hardware.md](./Hardware.md#further-replacement-during-leviq-diagnosis).

Before that further replacement, a ten-contact run at X200 Y200 had a 1.99000 mm range, bracketed by much tighter runs at X208 Y223. Separate contacts that each started from Z2 then produced:

```text
-1.06125, -1.67125, -1.61500, -0.13500, -0.12125, -0.12625, -0.14375
```

The +1.48000 mm change between the third and fourth readings did not require chained start heights within one `SAMPLES=10` command. A later same-point series also changed from -0.13375 to -1.17125 without the proposed manual nozzle push having been performed. Other repeated-contact sequences gradually returned toward -0.10 mm.

After the replacement, the X208 Y223 retry log still spanned +0.50875 to -0.44875 mm and ended in `Probe samples exceed samples_tolerance`. Heating targets of 210 °C nozzle and 50 °C bed were shown, but the excerpt did not establish a completed temperature soak or the exact live sampling configuration.

The old leaking hotend could have contributed to earlier behavior, but replacing it did not remove the observed problem. A trigger-coordinate difference is not, on its own, a measurement of physical hotend play or proof that the nozzle moved through the bed by that amount.

## Z-motion-associated indicator flicker and homing failure

**Status: speed-associated visual symptom observed; mechanical or electrical cause unresolved.**

After removing the cover, I clarified that the LeviQ electronics were still installed. This was not a test with a detached, electrically isolated printhead. During the upward Z movement associated with homing, the indicator flickered. I reported an apparent immediate first homing trigger, followed by an unreliable slow pass that could finish without a trigger. The exact final error text was not included in that report.

### Comparisons actually reported

| Test | Observation |
|---|---|
| Gentle manual Z-belt movement | No blink |
| A substantial manual Z jerk | Could produce a blink |
| `STEPPER_BUZZ` on X and Z | A blink mainly at the initial kick |
| `STEPPER_BUZZ` on Y | No observed blink |
| The supplied normal X moves with acceleration settings up to 10000 mm/s² | No observed blink; a separate much harsher X movement was thought to involve Bowden drag |
| `SET_STEPPER_ENABLE` alone on X, Y and Z | No observed blink |
| Controlled Z speed comparisons | Strong dependence on the requested Z speed, as below |

| Requested Z speed | Reported indicator behavior |
|---|---|
| 0.5, 1 and 2 mm/s | No flicker |
| 3 and 4 mm/s | No flicker |
| 5 mm/s | Strong flicker during movement |
| 6 mm/s | Blink at start/stop, not during steady movement |
| 7 mm/s | Blink mainly when stopping after downward movement |
| 8 mm/s | Blink at start/stop |

This is consistent with a speed-dependent excitation, including a possible resonance, but it is **not a verified mechanical-resonance diagnosis**. Speed-dependent electrical interference was not excluded. LED observations were not accompanied by captured PB15, PB14 or supply waveforms, or force/vibration measurements.

The absence of a blink from enable alone weakened an enable-only explanation. The `STEPPER_BUZZ` blink did not independently establish EMI. No completed motor-disconnected, mechanically decoupled motor, motor-current A/B, separate-supply or signal-filter comparison was returned. Those remained proposals, not verified wiring changes or fixes.

The dashboard acceleration value was not a measurement of actual Z acceleration. Published Z limits and the distinction from runtime test commands are recorded in [Configuration](./config/config.md#later-leviq-diagnostics-versus-this-snapshot).

## Retract-speed control did not establish a complete fix

**Status: 2 mm/s used in later diagnostic logs; prevention of all trigger shifts not verified.**

In the first cold comparison at 21:52–21:54, the 2 mm/s-lift series began at -0.30750 mm before seven much tighter readings. The 5 mm/s group then contained -0.07375, -0.21000, -0.06500, -0.07125 and -0.07500 mm. This looked suggestive, but the supplied comparison also changed the external return-to-Z2 feedrate from F120 to F300. It did not isolate the automatic retract alone.

The cleaner follow-up kept the external Z2 moves at F120 and compared a probe followed by a 5 mm/s retract with the **next** reading, whose own retract used 2 mm/s. Four returned pairs were:

| Reading before the 5 mm/s retract | Next reading | Change |
|---:|---:|---:|
| -0.07000 | -0.06625 | +0.00375 mm |
| -0.07250 | -0.07250 | 0.00000 mm |
| -0.07500 | -0.07500 | 0.00000 mm |
| -0.07750 | -0.08000 | -0.00250 mm |

Those four exposures did not reproduce a large next-reading shift. The same log contained -0.09375 → -0.54750 → -0.06750 mm in the slow-lift sequence before those pairs. A 2 mm/s lift was therefore not sufficient to prevent every large deviation.

The distinction is important: an indicator can react **during movement** without that movement demonstrably causing a persistent error in the **next contact measurement**. The record supports avoiding a problematic test speed during diagnosis, but not declaring the entire intermittent fault solved by changing `lift_speed`.

## Cold nozzle condition and the final warm series

I then clarified that the recent probing had been cold and that some filament might remain on the nozzle. This condition had not been controlled. It cannot be retrospectively excluded, but neither was it established as the cause of the large deviations.

The next returned series was explicitly reported at **220 °C nozzle / 50 °C bed**, at X200 Y200. All ten contacts started from Z2 and used `SAMPLES=1`, `PROBE_SPEED=0.5`, `SAMPLE_RETRACT_DIST=1`, `LIFT_SPEED=2`, with external Z moves at F120.

| Across all ten contacts, 22:20–22:22 | Value |
|---|---:|
| Minimum trigger Z | -1.39500 mm |
| Maximum trigger Z | -1.38625 mm |
| Range | **0.00875 mm** |
| Mean | -1.390875 mm |
| Median | -1.390625 mm |
| Population standard deviation | 0.00296 mm |

This verifies excellent **single-point repeatability in that run**, including its first contact. It does not verify absolute Z correctness, a correct Z offset, repeatability after visiting other points, successful homing or a reliable mesh/print-start cycle.

There was no confirmed clean/unloaded-nozzle comparison, paired heat/cool cycle with a proven unchanged Z reference, or final calibration record. Heating to 220 °C is not evidence that the nozzle was completely free of filament. The change from earlier cold coordinates near -0.07 to the warm values near -1.39 must not be recorded as a measured 1.32 mm thermal offset or attributed to soft filament without those controls.

### Last supported outcome and remaining limits

- The original LeviQ signal roles remain established, but intermittent trigger shifts and the later homing failure are **unresolved / reopened**.
- A further hotend replacement did not remove the problem. Bowden removal, reset-timing changes and the manual load tests narrowed possibilities without identifying a unique component.
- The speed-associated LED symptom is documented separately from the intermittent coordinate shifts. Neither EMI nor mechanical resonance was confirmed as their common cause.
- Slow diagnostic probing produced one very tight warm series, not a validated final configuration. No completed final homing, multi-point mesh or print was returned after it.
- Later homing-speed suggestions, a permanent 2 mm/s lift configuration, electrical filters, motor-current changes and additional temperature comparisons were not established as applied final fixes. The published cfg files remain unchanged.

For the sequence of changing hypotheses, see [Journey.md](./Journey.md#leviq-follow-up-from-this-troubleshooting-branch). For the actual published values and verified command overrides, see [Configuration](./config/config.md#later-leviq-diagnostics-versus-this-snapshot).

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

### Remaining limitations

This was a verified earlier clearance problem, not a diagnosis of every subsequent false trigger. Later failures also occurred during a cover-off diagnostic phase with the LeviQ electronics still fitted. The final remounting state and exact cause of that recurrence were not documented.

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

The uploaded early `klippy.log` captured this specific Y fault:

```text
TMC 'stepper_y' reports error: DRV_STATUS: c0190060 s2vsb=1(ShortToSupply_B!) ola=1(OpenLoad_A!) cs_actual=25 stealth=1 stst=1
```

The error alone did not identify a unique wiring mistake. After one change, Y no longer shut down but still did not move. The later physical retrace, followed by the user's report that both X and Y passed `STEPPER_BUZZ`, established the successful correction.

### Cause

The original harness connectors contain both motor wires and non-motor signals.

While transferring the mostly black wires to new connector housings, the original connector orientation was interpreted incorrectly and unrelated wires were mixed into motor connectors.

The X harness was particularly confusing because it carries:

- X motor
- Bowden extruder motor
- X endstop
- Filament runout sensor

The X-gantry chassis bond was traced separately; it was not another signal pin in the X connector.

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

<details>
<summary><strong>Pause or cancel could crash after skipped XY steps</strong></summary>

### Symptoms

During aggressive motion testing, if X or Y skipped steps, Klipper's logical coordinates no longer matched the physical toolhead position. A later pause or cancel could then command a parking move toward the far X/Y corner and mechanically crash near the travel limits.

### Cause

The immediate problem was not that `PAUSE` itself caused the skipped steps. The printer had already lost position, and an absolute XY park based on stale coordinates became unsafe.

### Fix and verification

A custom pause/cancel strategy was applied so that:

- normal pause parks near X0/Y0;
- cancel avoids a risky XY parking move;
- a dedicated crash-abort path lifts Z and re-homes X/Y rather than attempting to resume from a lost position.

The next user report said the behavior was much better.

### Remaining limitations

The final live macro text was not returned, so the repository snapshot does not reproduce this behavior. A print that has genuinely skipped XY steps should still be treated as position-lost; re-homing is for recovery after abort, not proof that the interrupted print can safely resume.

</details>

</details>

---

<details>
<summary><h1>Temperature sensors</h1></summary>

## Hotend thermistor failures in the upgrade branch

**Status: reported hardware faults; final replacement and calibration unresolved.**

### Symptoms and attempted repair

On 2026-08-07 I reported a thermistor problem and said that repairing it had proved too fiddly. On 2026-08-14, during the replacement-hotend discussion, I reported an ordered unit arriving with a "short" reading of **0.09 Ω**.

The latter report does not specify whether the measurement was across the thermistor, heater or another part, whether it was disconnected, or the meter range/lead contribution. It is preserved as a reported reading and fault description, not a confirmed diagnosis of a shorted thermistor or controller input.

### Replacement and verification

Between those reports, on 2026-08-09, I reported a new OEM hotend plus silicone sock and said that it seemed to print okay. That establishes a replacement and an initial print observation. It does not establish a permanent thermal repair: extrusion problems followed that day, and the later delivered-unit fault had no documented resolution.

### Remaining limitations

No exact final replacement model, sensor curve, new PID/Z-offset record or concluding print test was supplied. The order relative to the separate [LeviQ hotend replacement](#leakage-tightening-and-a-further-hotend-replacement) is not established. Candidate cartridge-sensor hotends remained comparison material; see [Hardware.md](./Hardware.md#upgrade-candidates-not-installed-hardware).


## Generic 3950 profile caused apparent underheating

**Status: `Generic 3950` rejected for the current installed hotend; EPCOS-style curve restored.**

### Symptoms

After a later hotend replacement I changed the live hotend thermistor profile from:

```ini
sensor_type: EPCOS 100K B57560G104F
```

to:

```ini
sensor_type: Generic 3950
```

At a displayed **210 °C** the filament was very difficult to extrude. The extruder eventually ground the filament, consistent with the hotend behaving substantially colder than the displayed value.

### Interpretation

The result is strong machine-specific evidence that `Generic 3950` was the wrong Klipper curve for the installed sensor in that setup.

It does not prove that the physical sensor is literally an EPCOS B57560G104F. Both names are software resistance-temperature curves; no external temperature probe or resistance-versus-temperature measurement was supplied to identify the thermistor itself.

### Fix and verification

I reverted the live configuration to:

```ini
sensor_type: EPCOS 100K B57560G104F
```

A stationary extrusion test at the same displayed **210 °C** was then reported as much better.

### Remaining limitations

The later PID, flow, maximum-volumetric-flow and Pressure Advance calibrations still needed to be repeated after the rollback. The published `steppers.cfg` already contains the EPCOS-style curve, so no cfg-file edit was required for this documentation update.

## Historical bed-heater section-name error

The earlier heater setup returned `Section 'heated_bed' is not a valid config section`. The subsequent report confirmed both heaters PID-calibrated, and the supplied `bed.cfg` uses `[heater_bed]`. This was a historical configuration-loading error, separate from the thermistor header mismatch below; it is not present in the published file.

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
<summary><h1>Extrusion and flow</h1></summary>

## Intermittent extruder skipping and filament grinding

**Status: unresolved; no confirmed mechanical or configuration cause.**

### Symptoms

On 2026-08-09 I described the original extruder as very dirty, possibly with worn gears. It could print normally and then stop feeding, skip and grind the filament until the print had to be cancelled. The earlier same-day report that the new OEM hotend seemed to print okay was therefore limited to initial operation.

### Attempted changes and outcome

I reported changing the nozzle and trying both higher and lower filament tension without eliminating the intermittent fault. Those actions did not isolate the cause. Gear wear, motor settings/microsteps, filament-path resistance and hotend restrictions were discussed, but no returned inspection or controlled test established one as the cause.

### Remaining limitations

No replacement extruder, motor-current change, cleaning result or permanent fix was confirmed. Product motor dimensions and shaft requirements were comparison material rather than a verified identification of the installed motor. The published extruder ratio, rotation distance and Pressure Advance remain unchanged.

## Temperature loss during the max-flow trial

**Status: thermal shutdown reported; sustained maximum flow not established.**

In the 2026-08-02 Orca trial, output still looked acceptable at approximately **22 mm³/s**, but Klipper shut down because the hotend could not maintain temperature. I reported that no silicone sock was fitted and suspected that this contributed. The exact shutdown message, target temperature and a temperature trace were not supplied with that result.

This was not a verified continuous-flow rating of 22 mm³/s or proof that the missing sock was the only cause. A sock was later reported with the OEM replacement, but no equivalent max-flow retest established the improvement. The associated PLA temperature/flow and inconclusive retraction observations are kept in [Journey.md](./Journey.md#tuning-and-hardware-upgrade-follow-up).

</details>

---

<details>
<summary><h1>Firmware identification and SD-card update ambiguity</h1></summary>

### Symptoms and observations

The physical board was an SKR 3 EZ with STM32H723, despite the early H743 assumption. Firmware was rebuilt for H723, 128 KiB bootloader offset, a 25 MHz crystal and USB on PA11/PA12.

The file repeatedly remained named `firmware.bin` after the attempted SD update. Most attempts used the locally built firmware; a SWIO H723 image was also tried. Status-light observations varied and did not establish a reliable flash result.

The 16 GB card was shown as MBR with two partitions: a small FAT `RECOVERY` partition followed by the larger FAT32 volume holding the firmware. The user could write and rename files, but could not delete `RECOVERY` through the attempted Windows workflow. A full format of a volume was attempted; a successful conversion to a single-partition card was not demonstrated.

### Verification

Windows subsequently enumerated a USB serial device with `VID_1D50&PID_614E`. The Pi then showed `usb-Klipper_stm32h723xx_...` under `/dev/serial/by-id/`, and the later uploaded `klippy.log` recorded:

```text
Loaded MCU 'mcu' 139 commands (v0.13.0-699-gc707dd19 / gcc: (15:14.2.rel1-1) 14.2.1 20241119 binutils: (2.44-3+23+b2) 2.44)
```

The log's MCU configuration identified `MCU=stm32h723xx`. This is stronger evidence of running Klipper than the SD filename or an assumed LED meaning.

### Remaining limitations

USB operation was verified, but the reason for the missing `.cur` rename was not isolated. The extra partition was a possible explanation, not a proven one. The available record does not identify the exact successful flash attempt or show a bootloader repair. SWD/ST-Link and DFU were discussed but were not established as performed work.

</details>

---

<details>
<summary><h1>Raspberry Pi host</h1></summary>

## Input Shaper dependency installation

**Status: setup recovered sufficiently for calibration; exact installed package versions not captured.**

On 2026-08-03 the attempted dependency installation returned:

```text
Error: Package 'libatlas-base-dev' has no installation candidate
```

The follow-up instructions omitted that unavailable package, retained `libopenblas-dev`, and offered an additional Python-environment installation only if imports still failed. I replied that it now returned OK; X and Y shaper-calibration logs followed. The record does not identify which fallback, if any, was needed or capture a complete package/version inventory. This documents the successful recovery, not a universal installation command or an assertion that every proposed step was executed.

## Klippy exited before creating its log

### Symptoms

Moonraker reported that it could not connect to Klipper. `klipper.service` repeatedly exited with status 1 and restarted, while the expected `klippy.log` file did not exist.

The system journal, rather than a Klipper log, exposed the failure:

```text
File "/home/admin/klipper/klippy/reactor.py", line 7, in <module>
    import greenlet
ModuleNotFoundError: No module named 'greenlet'
```

### Cause

The interpreter used by the service was `/home/admin/klippy-env/bin/python`; its environment could not import `greenlet`. This failure occurred while importing Klippy, before the printer configuration or MCU connection could be evaluated.

### Recovery and verification

The repair targeted the Python dependencies in `klippy-env`, using Klipper's `scripts/klippy-requirements.txt`; a direct `greenlet` installation was also offered as a fallback. The user confirmed that the host connection worked after the dependency-repair instructions, without specifying which installation branch was needed. No corresponding firmware or wiring change was required for this startup error.

Separately, several Update Manager entries had shown `INVALID`, but became readable version/update entries after a refresh. The screenshots do not establish a damaged Git repository or a repair of one.

## Ping worked but SSH and Moonraker did not

### Symptoms

- The previously used IP address still replied to ping; the responding device was not confirmed
- SSH returned `Connection refused`
- Mainsail and Moonraker were unreachable
- Rebooting did not restore the services

### Cause

The cause was not confirmed.

During wiring tests, the Raspberry Pi was powered from the printer and lost power every time the printer was switched off. Repeated hard power cuts were a plausible concern, but no filesystem check or boot log established corruption as the cause of this incident.

The MAC address attached to the old responding IP differed from the address later read directly on `wlan0`. The earlier replies therefore do not conclusively prove that the Pi, rather than another LAN device, was responding. The cause of the address difference was not established either.

### Fix

The host installation was re-created using Raspberry Pi OS Lite and subsequently worked again. This confirms recovery after reinstallation, not a diagnosis of the original failure. Later no-profile and intermittent-network incidents are recorded separately below.

The Pi should be shut down cleanly before power is removed:

```bash
sudo poweroff
```

### Prevention

During development, avoid switching off the printer while the Pi is writing to its SD card.

A separate or controlled 5 V supply is preferable when repeated power cycling is expected.



## Wi-Fi connection profile not present in NetworkManager

**Status: recovery verified for the reported connection and autoconnect retest; original cause unresolved.**

### Symptoms and observations

After a reported orderly shutdown, remote access was unavailable again. Local login worked, and `nmcli device status` showed `wlan0` as `disconnected`, while `nmcli connection show` listed only loopback. WLAN was neither soft- nor hard-blocked by `rfkill`.

NetworkManager and `wpa_supplicant` were running. The user reported no `/etc/network/interfaces` file. Setting the interface up or requesting `managed yes` did not establish a connection.

### Recovery

A Wi-Fi connection was created/activated from the local terminal with `nmcli`. It returned `Device "wlan0" successfully activated`, and the user subsequently confirmed both remote access and autoconnect working.

The evidence is that no Wi-Fi profile was listed at the failed check. It does not establish whether a profile had been deleted, had never been persisted, or had failed to load.

### Correction to the initial explanation

The earlier claim that `[ifupdown] managed=false` by itself explained this failure was unsupported. The [NetworkManager reference](https://networkmanager.dev/docs/api/latest/NetworkManager.conf.html), checked during this documentation update, says this setting applies to interfaces listed in `/etc/network/interfaces`. It is not a global switch disabling every Wi-Fi device.

## Recurring network latency and loss of access

**Status: unresolved / reopened in this troubleshooting branch.**

This was distinct from the successful profile recreation. The Pi could run normally for extended periods, then lose remote access during operation or after a later boot. Some failures recovered without any user action, but high latency and further outages remained.

### Representative measurements

The private SSIDs, host addresses and MAC addresses are omitted here; endpoint roles are retained.

| Test | Packets sent / received | Loss | Average RTT | Maximum RTT |
|---|---:|---:|---:|---:|
| Pi to LAN gateway, Wi-Fi power saving on | 100 / 100 | 0% | 609.818 ms | 2986.998 ms |
| Pi to LAN gateway, after power saving was switched off | 100 / 98 | 2% | 534.843 ms | 4850.784 ms |
| Later Pi-to-gateway test at a shorter ping interval | 290 / 263 | 9.31% | 82.221 ms | 452.758 ms |
| Ethernet-connected Windows PC to `1.1.1.1` | 45 / 41 | 4 lost; Windows displayed 8% | 405 ms | 3025 ms |

These were different runs, not a controlled benchmark. In particular, the lower average in a later test did not establish a fix while loss persisted.

Other reported observations:

- RSSI snapshots were around -50 to -55 dBm; the Wi-Fi link reported association and authentication during some checks.
- A `top` capture showed 98.3% CPU idle and 0% I/O wait. A nearby `free -h` result showed about 201 MiB available and no swap use.
- Loopback and the Pi's own-address pings were reported at about 0.1–0.2 ms. Those tests did not traverse the Wi-Fi path.
- `vcgencmd get_throttled` returned `throttled=0x0`.
- Changing from the IoT SSID to the main SSID retained the IP in the observed transition and did not resolve latency. Power saving was later explicitly confirmed off.
- Windows pings to the Pi alternated between timeouts, `Destination host unreachable` from the local PC, and replies delayed by several seconds.
- The Ethernet-connected PC also had slow/lost replies from the local gateway. Its traceroute showed first-hop samples of 645, 418 and 1702 ms.
- A later local login screen displayed an IP address, while access was again unavailable. The user reported `STALE` neighbour entries and described occasional ping responses taking roughly 60 seconds; no complete trace was supplied for that last timing estimate.

### Interpretation and remaining uncertainty

The PC-to-gateway and PC-to-Internet results showed that the incident was not adequately explained by a Klipper-only configuration fault. A shared LAN/mesh path or gateway problem became a leading hypothesis. However, the PC's exact Ethernet path and whether it still traversed wireless mesh backhaul were never confirmed. Neither a specific Deco node nor a specific Wi-Fi/driver fault was proven.

The good RSSI, light CPU load and successful local pings were snapshots, not proof that every part of the host and network was healthy. The available excerpts also do not establish what `wpa_supplicant` or NetworkManager did at the precise moment of the later outages. Different BSSIDs seen in separate captures are not, on their own, proof of roaming causing a disconnect.

Two technical clarifications were checked against upstream documentation during this update, rather than measured on the printer:

- `STALE` is not the same as a failed neighbour lookup; the neighbour entry is still valid but needs renewed reachability confirmation. See the [iproute2 neighbour-state manual](https://manpages.debian.org/trixie/iproute2/ip-neighbour.8.en.html).
- `throttled=0x0` was not a power-supply clearance test. Raspberry Pi's [power-supply documentation](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#power-supply-warnings) excludes the Zero range from the low-voltage detection circuitry described there. The readout cannot rule out this host's supply/cabling problems.

### Workarounds and verification boundary

Access returned after manual reconnection and sometimes spontaneously. An autoconnect retest succeeded, but disabling Wi-Fi power saving and changing SSID did not establish stable operation. None of these events was shown to eliminate the later failures permanently.

A mobile-hotspot comparison, changes to Deco roaming/backhaul settings, a different host network adapter and a final independent-power comparison were proposed, but no completed result establishes any of them as the fix in this branch. The failure journals requested for the final outages were not returned as a complete diagnostic capture.

No successful print through a documented outage was established here. The host-to-SKR link in this build is USB, separate from browser/SSH traffic over Wi-Fi. Loss of the web interface must not be recorded as a proven MCU-disconnect event without the corresponding Klipper evidence.

See [Journey.md](./Journey.md#host-and-network-follow-up-from-this-chat-branch) for the order of temporary recoveries and later recurrences.

</details>
