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
| LeviQ not reset | Probe values drift or trigger before touching the bed | Pulse PB14 LOW then HIGH before every probe attempt |
| Printhead cover touching load cell | Random probe triggers | Increase clearance around the LeviQ load-cell screws |
| Z motors fighting each other | Loud Z motion, barely moves in one direction | Reverse one coil pair on one Z motor |
| Mixed motor and signal wires | TMC shutdown or motor does not move | Re-map the complete harness and preserve coil pairs |
| Extruder UART on wrong pin | `Unable to read tmc uart 'extruder' register IFCNT` | Use E0 UART pin PC6 |
| Bed thermistor on wrong header | Invalid or missing bed temperature | Use TB / PA1 instead of TH1 |
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

- The Raspberry Pi still replied to ping
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
