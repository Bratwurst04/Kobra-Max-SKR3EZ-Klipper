# Repository working rules

This is the owner's documentation of one original Anycubic Kobra Max converted to a BIGTREETECH SKR 3 EZ / STM32H723. It is also a reference for similar conversions. Write public prose, configuration comments and descriptions in English.

Read [README.md](README.md), [dated status](docs/STATUS.md), and the [configuration limitations](docs/config/config.md#published-snapshot-and-host-state) before proposing changes. The split cfg files are a historical reference, with deliberate local-identity placeholders and missing host-side dependencies. They are not the owner's complete current backup or a ready-to-flash installation.

- Label evidence: owner report, dated read-only observation, historical test, static/offline check, proposal, or attended physical result. A service reporting ready or a successful template render is not a successful print.
- Prefer the dated status for present scope; keep the build and troubleshooting history dated. Do not turn an old unresolved report into a newly observed fault, or erase it as permanently solved without evidence.
- Preserve board/MCU types, pins, signal polarities and historical numerical settings unless a specific independently reviewed change is authorized. Never infer a safe component temperature from `max_temp`, a seller claim, or a similar hotend.
- Do not copy raw machine snapshots, logs, slicer projects, credentials, host names, local addresses, MCU serial IDs or personal paths into this tree. Use explicit `REPLACE_WITH_YOUR_...` fields with replacement instructions. Local `reviews/` and private material are excluded from publication.
- Review the exact changed file list and diff before staging. Add named files only. Do not use `git add .` or force-add private/ignored material. Ignore rules do not inspect content or remove old history.
- For cfg proposals, preserve exact before bytes and hashes; document the after hash, diff, reason, limits and rollback. Require separate review. A later live transfer needs an actual-target backup and drift check. Repository access alone authorizes no printer/Pi connection, movement, heating, print, restart or update.
- Validate documentation links and substantive cfg invariants appropriate to the diff. For macro behavior, exercise meaningful offline boundary/error cases and state the emulator/source-version limits. Keep physical testing explicitly separate.

Do not publish releases or rewrite history as a documentation cleanup step. Preserve the source record in Git; any credential incident or justified history rewrite needs its own concrete, reviewed response.
