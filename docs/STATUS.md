# Dated machine and workflow status

Updated **2026-10-29**. This page records the owner's machine and the evidence available on that date. It is not a claim about the printer's live state whenever this page is read. Raw machine snapshots and slicer/test files are kept outside this public repository.

## Intended everyday flow

**Power on the printer → open OrcaSlicer (Moonraker/Mainsail when needed) → Print.** This is the target workflow. On 28 October the owner reported no known current fault after the summer break. The previously working reference belongs to the home PC; the exact historical G-code was not recovered. Printing from the laptop has **not yet been tried**. No attended printer test or print was performed during this documentation pass.

The next step is a separately agreed attended readiness session, followed by the staged homing/start/print checks appropriate to its results. Treat the source configuration and the reviewed export below as preparation for that session. Do not replace a working machine with the older cfg files in this repository.

## Evidence and limits

| Record | What it establishes | What it does not establish |
|---|---|---|
| Published cfg reference | Historical split configuration, wiring-related values and known missing prerequisites | A complete backup or the configuration read on 28 October |
| S01, read-only snapshot on 28 October, 17:33:16–17:33:18 UTC | 15 exact cfg/conf files, 13 resolved include references and separately captured loaded settings | An atomic capture, present runtime state, restore success or physical function |
| P04, reviewed laptop cube export | Static start/end/object-definition checks for the exact file identified below | A printed cube, correct physical clearance or print quality |
| Local purge-guard proposal | An independently reviewed offline check of the selected nominal XYZ purge envelope | An installed change, collision simulation, heater shutdown or resolved retraction behavior |

P04 SHA-256: `2f61271991311096d37eb77c96b54aaa1843adee811a6d3490701fe3857c8d6c`. Its native object polygon is defined before `PRINT_START BED=55 EXTRUDER=210`, and its slicer pressure-advance override was removed. Those are file observations, not temperature recommendations. Relative extrusion and the normal heater/fan-off ending were checked; an error path need not reach that ending.

**OrcaSlicer's “Emit limits to G-code” flag is ignored for Klipper**, as the [official Motion Ability documentation](https://github.com/OrcaSlicer/OrcaSlicer/wiki/printer_motion_ability#emit-limits-to-g-code) states. Metadata value `1` was not a defect and did not require changing it to `0`. The process's actual acceleration commands and separate slicer limits are distinct. The earlier local review requirement to change this flag was withdrawn.

On 29 October, five relevant Klipper source files on disk, covering firmware retraction, macros, movement state, command dispatch and file error handling, matched the [pinned official revision](https://github.com/Klipper3d/klipper/tree/7046bd00ef5c30dec6febc724f8d22967433c45c). This verifies those disk files, not the whole installation, loaded process or package bytes, material state or physical function. The start and recovery contract for retraction remains unresolved.

## How S01 differs from the public reference

| Area | Dated S01 observation | Interpretation |
|---|---|---|
| Start chain | Active KAMP includes; filament check, wipe, mesh, Smart Park and line purge present | More complete than the public reference; successful execution still untested in this pass |
| Extrusion | Configured nozzle 0.400 mm and observed loaded PA 0.21; owner confirmed a 0.4 mm nozzle | The public PA 0.44 is historical. Neither value is a universal recommendation |
| Motion | Limits/current/microstep settings differ from the older files | Do not infer quality or safe performance from a configured maximum |
| Runout | Sensor configured/enabled and reporting filament at capture time | End-to-end runout, pause and resume were not tested |
| Calibration | Saved machine calibration exists in S01 | It is machine-specific; this public reference is not its replacement |

The [configuration guide](config/config.md) describes the older files in this tree. Its historical statements that a final live wipe, runout setup or saved calibration was absent refer to that supplied public snapshot, not to an absence of those definitions in S01.

## Open readiness questions

- The configured wipe waits for 230 °C independently of P04's 210 °C request. Exact replacement-hotend identity and its temperature rating are unverified. The owner describes an original-style replacement that is not all-metal. A candidate product listing is insufficient to turn this into a confirmed bill of materials or safe temperature ceiling. S01's EPCOS profile and `max_temp: 280` are configuration observations, not component ratings.
- Physical LeviQ behavior, brush/contact height, travel clearance and the complete start sequence need attended verification. Historical intermittent probe, extrusion and network reports remain in [Issues](Issues.md); their old “unresolved” labels do not establish a new fault today.
- A local coordinate-guard candidate rejects the documented purge target X425 beyond configured X415 before its own commands are returned. It leaves the reviewed cube's nominal XYZ/E sequence unchanged. It was not transferred or activated. Retraction/state handling remains a separate open review; no isolated G10/G11 correction is implied.
- The inspected historical backup archives lack all three required KAMP targets. Read-only inspection on 29 October also bound the installed [Klipper-Backup revision](https://github.com/Staubgeborener/Klipper-Backup/tree/77d83ac3169839bdfadc81f9adeddf21d637a536), actual job configuration and configured destination. The selected copy path skips the three KAMP cfg files and `mainsail.cfg` behind known links; their four logical include paths are absent from the local backup commit. All four targets were stable with recorded file hashes and no further include headers in those files. That closes only this static include chain, not a complete restore set.
- Successful remote delivery and a full restore remain unverified. A fresh remote read advertised no revision; existing job records showed grouped HTTP access/target and policy errors without identifying a specific credential or policy cause. Service completion is not proof of delivery. A local repair candidate is being prepared; it has not been deployed or tested live.

## Reading the older documentation

[Wiring](Wiring.md) preserves the machine's reported pin/connector mapping; verify another machine's revision and harness before following it. [Hardware](Hardware.md), [Journey](Journey.md) and [Issues](Issues.md) preserve build-stage observations and earlier open questions. [The audit index](../AUDIT_REPORT.md) points to the immutable September audit and explains its scope. None of those historical success reports is a fresh end-to-end acceptance test.
