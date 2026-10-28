# Historical audit index

The detailed September 2026 technical/documentation audit is preserved in Git. This English index replaces the Swedish report in the current tree; it does not rerun or silently revise that audit.

- [Original complete report at immutable commit 4eb582825d5d3e43b432dc67af1b247d015467d4](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/4eb582825d5d3e43b432dc67af1b247d015467d4/AUDIT_REPORT.md)
- [Current dated evidence and workflow status](docs/STATUS.md)
- [Published configuration prerequisites](docs/config/config.md#published-snapshot-and-host-state)

## Scope and outcome

The audit used source baseline `0a5059781347635a45b5196a254ad758e8c844a7`. Its documentation corrections were merged as `4eb582825d5d3e43b432dc67af1b247d015467d4` on 17 September 2026. Neither commit date establishes a physical printer test on that day.

The report recorded 13 findings: 0 critical, 1 high, 4 medium, 3 low and 5 informational. Its phase-two disposition was **8 fixed, 1 partially fixed and 4 accepted as-is/informational**. Those figures describe that historical review and its documentation changes; they are not a count of faults currently observed on the printer.

The main documentation corrections clarified included-file PID/SAVE_CONFIG prerequisites, the incomplete public snapshot, temperature settings versus component ratings, historical extrusion calibration, fan behavior and duplicate or broken references. Board/MCU identity and active pin assignments were not shown to conflict. Numerical configuration settings were preserved.

## Limits that remain relevant

- The public cfg reference lacks required machine/host prerequisites and generated calibration. Historical successful use of the actual printer does not make this tree a complete restore package.
- A configured temperature ceiling permits corresponding requests; it is not proof of the installed replacement hotend's physical rating.
- Conditional KAMP purge geometry and unknown final live versions needed separate assessment. October's local static work is described in dated status, not retroactively counted as September physical evidence.
- The historical audit reported source/parser/template/link checks. This index does not claim to rerun those checks or perform a new full wiring audit.
- Original photos, conversations and machine logs are not all independently available in the public repository. The original report's immutable source links retain their historical context.

The owner returned to the project on 28 October reporting no known current fault. S01 and P04 are newer, separately identified evidence; attended laptop printing and physical acceptance remain pending. Use the dated status for those boundaries and the original report for detailed finding IDs and source references.
