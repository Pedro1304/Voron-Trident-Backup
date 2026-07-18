# Voron Trident 250 — Maintenance Log
*Last updated: 2026-07-18*

> **Note on print stats:** Stats auto-fetched every 60s by `autobackup.sh` via `http://localhost/server/history/list?limit=9999` and saved to `print_stats.json`. ⚠️ **Stale as of 2026-07-18** — last auto-backup commit was 2026-06-21 14:35, no autobackup activity in the repo since (27 days), which lines up with the CB2 SD card swap. Confirm `voron-backup.service` is active and pushing again post-swap; current print-hours figure below is out of date until it refreshes.

---

## Current Stats
| Metric | Value | Last checked |
|--------|-------|-------------|
| Total print time | 270.8 hours | 2026-06-21 |
| Total jobs | 793 | 2026-06-21 |
| Filament used (total) | 1798.0 meters | 2026-06-21 |
| Most used filament | ASA 74% (1180m / 490 jobs) | 2026-05-29 |

---

## Filament Usage Breakdown
*Last updated: 2026-06-20*

| Material | Meters | Jobs | % of total |
|----------|--------|------|------------|
| ASA (all brands) | 1180.9m | 490 | 74% |
| PLA | 242.2m | 71 | 16% |
| PETG | 65.6m | 136 | 4% |
| TPU (Polyflex TPU90) | 37.1m | 18 | 2% |
| Unknown/Other | 10.8m | 5 | <1% |

**Primary material: ASA** — relevant for nozzle wear rate, chamber temp requirements, and PEI sheet longevity.
**Nozzle wear note:** CHT Coated Brass is suitable for ASA but not abrasive filaments. No carbon fibre or glow filaments.

---

## Maintenance Schedule

### Every Print
- [ ] Visual inspect first layer
- [ ] Check nozzle for ooze/damage before print
- [ ] Check Klicky probe dock is clean and magnets secure
- [ ] Verify bed surface (PEI clean, no debris)

### Weekly / Every ~20 hours
- [ ] Clean PEI sheet with IPA
- [ ] Check all visible wiring for chafing or loose connectors
- [ ] Check Klicky probe wiring and dock alignment
- [ ] Inspect X/Y belts for debris or wear
- [ ] Check toolhead fans spinning freely (hotend + part cooling)

### Monthly / Every ~50 hours
- [ ] **Belt tension check** — X and Y. Target ~110Hz with Gates Carbon belt tension app or similar
- [ ] **Z leadscrew lubrication** — PTFE-based grease (e.g. Superlube) on TR8×4 leadscrews
- [ ] **Z linear rail lubrication** — light oil or PTFE on MGN rails
- [ ] **X/Y linear rail lubrication** — same as Z rails
- [ ] **Check all M3 screws** on toolhead, XOL carriage, gantry
- [ ] **Orbiter v2.0 gear inspection** — check Delrin planetary gears for wear
- [ ] Check chamber heater fan spinning freely
- [ ] Verify SHT31 sensor readings are plausible

### Every 3 Months / Every ~150 hours
- [ ] **Input shaper re-calibration** — run ShakeTune after any mechanical changes or if print quality degrades
- [ ] **Pressure advance re-calibration** — especially after nozzle or filament brand changes
- [ ] **Z offset verification** — paper test to confirm z_offset=8.15 still accurate
- [ ] **Z tilt verification** — run Z_TILT_ADJUST and check deviation
- [ ] **Bed mesh comparison** — compare new mesh to saved default, check for changes
- [ ] Deep clean hotend area — remove filament residue from nozzle, heatblock, silicone sock
- [ ] **⚠️ CHT Coated nozzle:** Do NOT cold pull — damages nickel coating. Use hot pull (purge at high temp) only.
- [ ] Check EBB36 CAN connector seating
- [ ] Inspect Sexbolt Z endstop — clean contact surface, check D2HW switch travel

### Every 6 Months / Every ~300 hours
- [ ] **Full gantry tram check** — verify gantry is square, belts equal tension
- [ ] **Re-run sensorless homing tuning** — check SGT=3 still optimal, adjust if needed
- [ ] **Hotend maintenance** — inspect heatbreak, replace nozzle if worn
- [ ] **Orbiter v2.0 full inspection** — check all planetary gears, clean drive gear
- [ ] Review and update OrcaSlicer printer profiles if needed
- [ ] Check GitHub backup service still running: `sudo systemctl status voron-backup.service`
- [ ] Verify TRSYNC patch still applied after any Klipper updates: `grep TRSYNC_TIMEOUT ~/klipper/klippy/mcu.py`

### As Needed / Condition Based
- [ ] **Nozzle replacement** — when under-extrusion or stringing cannot be tuned out, or after abrasive filament
- [ ] **Klicky probe re-dock calibration** — if probe fails to attach reliably
- [ ] **Re-run Z_ENDSTOP_CALIBRATE** — if first layer height shifts unexpectedly
- [ ] **Re-run PROBE_CALIBRATE** — if z_offset drifts or nozzle is replaced
- [ ] **Belt replacement** — if tension cannot be maintained or visible wear/cracking
- [ ] **Moonraker DB backup** — periodically copy `~/printer_data/data/moonraker.db` somewhere safe (not in git repo — binary file). History can be cleared unexpectedly.
- [ ] **Nozzle cold pull** — not yet practiced; consider adding for hotend maintenance routine (NOT for CHT coated nozzle — damages nickel coating; use hot pull / purge only)
- [ ] **Orbiter v2.5 upgrade** — when doing Turbiter/CPAP mod (parts on hand)

---

## Task History
| Date | Print Hours | Task | Notes |
|------|-------------|------|-------|
| 2026-07-18 | ~270.8h* | Belt tension check | X and Y belts tensioned — was ~19 days overdue (last done 2026-05-29). Monthly cadence, next due ~2026-08-18. *Print hours stale, see 2026-07-18 SD-card entry below. |
| 2026-07-18 | ~270.8h* | CB2 root SD card replaced — SanDisk High Endurance 32GB | Swapped the failing root SD (`mmcblk0`, repeatedly failed UHS SDR104 tuning on boot) for a SanDisk High Endurance 32GB card. This was the confirmed root cause of the 2026-06-14 git index/refs corruption and the 2026-06-27 zero-filled docs incident. eMMC migration still off the table (Pedro's decision) — SD swap is the full fix. Autobackup interval stays at 300s (5 min) as a continued mitigation. **Unblocks the SD-card half of the DEPLOY_CONFIG gate** — decoupled backup (moving the backup repo out of the live `~/printer_data/config` dir) is still outstanding as the second prerequisite; DEPLOY_CONFIG remains un-deployed until that's done. *Print hours shown are the last known value (2026-06-21) — autobackup/stats have been silent for 27 days, likely spanning the swap; confirm service is running and stats have resumed. |
| 2026-07-18 | ~270.8h* | PAUSE/RESUME/CANCEL_PRINT macros reworked (poop-location park, wipe-before-resume, cancel-drop) | `printer.cfg`: PAUSE now parks at the poop bucket XY (`_AFC_POOP_VARS.purge_loc_xy`, was X10/Y10 corner) with a 5mm Z-hop (was 10mm). RESUME now runs `AFC_BRUSH` (wipe) before restoring position/resuming, since it happens before `_RESUME_BASE`'s `RESTORE_GCODE_STATE`. CANCEL_PRINT now moves to the poop bucket XY then lifts Z 15mm after cancelling, instead of leaving the toolhead/bed in place. `AFC/macros/Brush.cfg`: post-poop settle wait raised 5s → 15s (`G4 P15000`), plus a retract (`G10`) added before the wait to cut ooze. |
| 2026-07-18 | ~270.8h* | AFC-Klipper-Add-On forked to `Pedro1304/AFC-Klipper-Add-On`, local patch applied for Orbiter Smart Sensor toolhead-load timing | Root cause of intermittent "stopped short of commanded distance to toolhead, backing up and retrying load" faults: the Orbiter v2.5 Smart Sensor has a hardware reporting delay after its physical trigger, and AFC's toolhead-load approach in `extras/AFC.py` (`load_then_home`, ~line 1300) uses a real Klipper homing move against the raw MCU-level endstop — not software `debounce_delay` (which doesn't apply to homing moves) — so a slow-reporting sensor makes AFC think the load fell short even when filament is physically there. **Fix:** added `self.reactor.pause(self.reactor.monotonic() + 3.0)` right after the initial bowden-length approach move, before AFC reacts to/retries on a missed trigger, giving the sensor time to settle. Since `extras/AFC.py` lives in `~/AFC-Klipper-Add-On`, a separate repo under Moonraker's `update_manager` (auto-tracks `AFCProject/AFC-Klipper-Add-On` main), a direct edit would be wiped on the next AFC update. **Durable fix:** forked the repo to `github.com/Pedro1304/AFC-Klipper-Add-On`, patch committed there (`d89d81d`), `~/AFC-Klipper-Add-On`'s git remotes renamed (`origin` → fork, old `origin` → `upstream`), and `moonraker.conf`'s `[update_manager afc-software]` `origin:` repointed to the fork so future Mainsail updates pull from it, patch intact. To pick up real upstream fixes later: `git fetch upstream && git merge upstream/main` on the printer, reapplying/rebasing the patch if it conflicts. Also filed as an upstream issue/PR candidate (delay-tolerant load check) — if merged, the fork/patch can eventually be retired. Verified: `ast.parse` syntax-clean, `systemctl restart klipper` clean, `/printer/info` returned `state: ready`. |
| 2026-06-28 | 270.8h | BoxTurtle — hub→toolhead load (T0) fails; `pin_tool_start`/PB4 not triggering | Progressed past hub stage: lanes 1 & 2 load to hub, T0 (lane1) attempts full load to toolhead. Console: *"Distance stopped short of commanded distance to toolhead, backing up and retrying load"* ×2 → *"lane1 filament failed to trigger pre extruder gear toolhead sensor"* → PAUSE. Filament path map showed tip between HUB and TOOL (`==>--`), not reaching TOOL sensor. **Confirmed from live config (uploaded snapshot):** Orbiter `[filament_switch_sensor O2_sensor]` on PB4 is **commented out** → no PB4 double-claim; PB4 owned solely by AFC `pin_tool_start`. So failure is NOT a pin conflict. Live `afc_bowden_length: 1331.16` (repo lagged at 1664.61 — backup stale). No `[AFC_buffer]` configured. **Decisive next step (sensor-vs-drive bisection):** hand-feed filament to the Orbiter/toolhead sensor and watch the `extruder` tool_start sensor in Mainsail (`enable_sensors_in_gui: True`). Flips TRUE by hand → sensor good, chase DRIVE/DISTANCE (bowden length recalibration / lane grip / espooler assist). Doesn't flip → sensor electrical: PB4 polarity (try `!`/`^` on `pin_tool_start`), wiring, or v2.5 SmartSensor lever not actuated by filament. Open Q: did a toolhead load ever complete, and was `afc_bowden_length` just changed/recalibrated? | AFC-Lite enumerated + flashed; `[include AFC/*.cfg]` enabled in printer.cfg (was disabled). Lanes 1 & 2 load to hub OK. **Lane 3 fails to complete load-to-hub.** Diagnosed the "Cannot load lane3 spool while printer is actively moving or homing" banner to source: `AFC_lane.py prep_callback` calls `AFC_error(..., pause=False)` → **non-fatal warning only**. Guard = `is_moving()` = `idle_timeout.state == "Printing"`, which trips when the lane's own feed move is still running as the prep switch bounces. Same benign banner appears on lanes 1 & 2 (they still load because pause=False), so the message is NOT the cause — lane 3 simply isn't reaching/triggering shared hub switch (HUB=PC4). Lane 3 config block is identical to 1/2 → physical/electrical on lane 3 path. Next: watch lane3 prep(TRG3/PB1)+load(EXT3/PE10) in GUI for chatter; `LANE_MOVE LANE=lane3 DISTANCE=50` vs lane1 to split sensor-vs-drive (M3 stepper / MOT3 espooler); confirm hub triggers from lane3 by hand. Prime suspect: not-fully-seated JST on lane3 prep switch or espooler. |
| 2026-06-27 | 270.8h | BoxTurtle bringup — BTIO found shorted, bypassed | LDO/Armored Turtle **BTIO hub measured 1.0–1.5Ω steady across 24v↔Gnd** (bare board, all ports disconnected) = dead short on the power/ground plane. Suspected reverse-polarity protection part failed shorted after repeated PSU-tripping plug-ins. Cross-plug 24v↔24v / Gnd↔Gnd beeps are NORMAL (parallel daisy-chain bus) — only same-plug 24v↔Gnd matters. BTIO has no signal-only path (every inter-board connector carries 24V; common ground except USB), so it cannot be used "buffer-only." **BTIO pulled from circuit (RMA/replace candidate; inspect for solder bridge before condemning).** Bypassed per standard BoxTurtle wiring: AFC-Lite powered direct via **24V/GND on CAN-port power pins** (AFC-Lite has no USB-PD), **USB-C for data**, TurtleNeck Pro + lane sensors straight to AFC-Lite. PSU held on power-up (AFC-Lite not shorted). Pending: confirm USB enumeration (`ls /dev/serial/by-id/`), then flash. |
| 2026-06-27 | 270.8h | Doc files zero-filled by SD corruption — recovered from git | `Maintenance_Log.md` (12638B) and `Trident_Build_Info.md` (16620B) both found **100% null bytes** at repo HEAD. Corruption entered at autobackup commit `8b6b4ec` (2026-06-20 14:11:20) — that cycle captured the files already zeroed on the CB2's SD. Last-good blobs: Maintenance_Log `1af0024` (06-20 10:29), Build_Info `efcfa6b` (06-20 08:56). Recovered both via `git show <blob> > file`; fsck/content verified clean. Config + JSON files unaffected (only the two largest, least-rewritten docs took the hit — classic failing-SD signature). **Root cause unchanged: root-on-SD card still failing UHS tuning. This WILL recur until the SanDisk High Endurance card is installed.** ⚠️ Pedro must confirm the live copies on the printer are good after next autobackup pull. |
| 2026-06-15 | 260.7h | Added M600 manual colour-change macro | `[gcode_macro M600]` in printer.cfg wraps PAUSE + CHIME_PAUSE + Mainsail Resume prompt. Enables OrcaSlicer layer-slider colour changes (single extruder, manual swap) without the missing-M600 error. Unload via Orbiter button, RESUME after purge. |
| 2026-06-14 | 258.6h | Git index/ref corruption — recovered | CB2 `~/printer_data/config` git broke: `bad signature 0x00000000` (zero-filled `.git/index`) + zero-filled loose `refs/heads/main` shadowing an older valid packed ref. Cause: unclean write/power-loss during a 60s autobackup cycle. Object DB intact (fsck clean, 9981 objs). Recovered without data loss: rebuilt index (`rm .git/index; git reset`), cleared broken loose+packed `main`, recreated `main`@`15ca7c7` via `update-ref`, mixed-reset, restored good docs via checkout. No config lost — all live files were byte-identical to repo except the two stale docs. fsck CLEAN post-recovery; backups flowing again. ⚠️ Follow-ups: check eMMC/SD health (`dmesg | grep -i mmc`); move backup repo OUT of the live config dir so this can't recur. |
| 2026-06-14 | 258.6h | Turbiter 3010 motor cooling — firmware/config | Blower + Turbiter physically installed. Configured `[heater_fan motor_cooling_fan]` on **EBBCan:PA1** (pin freed when part cooling moved to CPAP), tied to extruder @ 50°C, full power. No EBB36 reflash needed (PA1 already a firmware GPIO). Cleaned dead commented PA1 block from `_ebb36.cfg`. Interim heater_fan control until NTC B3950 motor thermistor wired to PA2 -> then convert to [temperature_fan]. |
| 2026-06-14 | 258.6h | WS7040 CPAP blower fully installed | Part cooling blower install complete. Pin confirmed **Octopus PB6** (final; earlier PG15/PG12 abandoned). Docs reconciled. No boot-spin on PB6 (verified) — no menuconfig fix needed; polarity correct (SPEED=0=OFF / 1=FULL). CPAP fully done. |
| 2026-06-14 | 258.6h | Orbiter v2.0 -> v2.5 upgrade | Extruder upgraded to Orbiter v2.5 (installed with Turbiter/CPAP). ⚠️ Recalibrate: extruder rotation_distance / e-steps + re-validate ASA flow ratio (0.938 on v2.0); confirm motor/run_current if kit changed stepper. |
| 2026-06-14 | 258.6h | EBB36 side-mount relocation cancelled | No longer needed — EBB36 mounts to the back of the Turbiter. Removed from toolhead roadmap. |
| 2026-06-13 | 258.6h | WS7040 signal pin debug | PC5 failed — stuck-high, no PWM (blower constant-on at any SPEED/polarity; connection otherwise sound). Moved signal to **PG15 (Stop7)**, empty M7 slot. Config `pin: PG15`. Boot-spin fix (`!PG15` in menuconfig) + polarity bench-test to follow once modulation confirmed. |
| 2026-06-13 | 258.6h | WS7040 CPAP blower wiring (in progress) | Blower installed + connected to Mellow driver board. Driver wiring: 24V+ to main rail, GND common with Octopus, signal (SV, pot removed) to Octopus **PG12** (5V positive logic pin — NOT a fan MOSFET). Part_Cooling fan to be reassigned EBBCan:PA1 -> PG12 once wiring complete. Frame-mounted (remote CPAP); Turbiter toolhead duct still pending. |
| ~2025 | — | Print history cleared | Reason unknown — only 1 job remained in Moonraker DB. 726 jobs/225.8h recovered from full list endpoint. Consider periodic Moonraker DB backup. |
| ~2025 | — | SGT raised 2→3 | Sensorless homing early trigger on X |
| 2026-05-31 | 232.7h | Sensorless homing retuned | Final values: SGT=4, 25mm/s, full 2.5A run current. SGT=4 required for reliable homing at 55°C chamber — lower SGT caused early triggers when hot. Added [homing_override] with Y-first single-pass, SET_KINEMATIC_POSITION backoff, SET_TMC_FIELD to apply SGT at homing time. Verify SGT=4 does not miss endstop when cold. |
| ~2025 | — | Auto-z plugin removed | Persistent conflicts; switched to standard z_offset |
| ~2025 | — | zero_reference_position corrected | Was 165,259 (Sexbolt), changed to 125,125 (bed center) |
| ~2025 | — | TRSYNC_TIMEOUT patched 0.025→0.075 | CAN bus timing fix |
| ~2025 | — | Input shaper calibrated | X=MZV 72.8Hz, Y=EI 64.4Hz (pre-hula feet) |
| 2026-05-29 | 225.8h | Input shaper re-calibrated | X=MZV 71.0Hz, Y=MZV 52.4Hz — with hula feet installed. Y changed from EI to MZV, freq dropped due to feet dampening frame resonance (expected). |
| 2026-05-29 | — | OrcaSlicer backup set up | Daily auto-backup to Pedro1304/OrcaSlicer-Backup |
| 2026-05-29 | 225.8h | Belt tension check | X and Y belts tensioned |
| 2026-05-29 | 225.8h | Leadscrew & rail lubrication | TR8×4 leadscrews + all MGN rails. Next due whichever comes first: ~2026-08-29 OR ~275.8h |
| 2026-01-13 | — | Nozzle installed | Bondtech CHT Coated Brass 0.4mm (Nickel coated, high flow) |
| 2026-05-15 | — | PEI sheet replaced | New magnetic PEI spring steel sheet installed |
| 2026-05-29 | 225.8h | Bed mesh re-calibrated | After belt tension + lubrication today |
| 2026-05-29 | 225.8h | Z offset verified | Paper test confirmed z_offset=8.15 still accurate |
| 2026-05-29 | 225.8h | Pressure advance verified | No conflict between Klipper and OrcaSlicer PA values |
| 2026-05-29 | 225.8h | Sensorless homing verified | Reliable triggering confirmed after belt tension |
| 2026-05-29 | 225.8h | PEI sheet IPA clean | Thorough clean, ~2 weeks since install |
| 2026-05-29 | 225.8h | Hotend inspection | Silicone sock removed, checked for filament creep — all good |
| 2026-05-29 | 225.8h | z_calibration removed from disk | `~/klipper_z_calibration` deleted — already removed from config and moonraker.conf |

---

## Upcoming / Overdue
*Updated each session based on last task dates and print hours*

| Task | Due | Status |
|------|-----|--------|
| Toolhead overhaul | Nearly complete | ✅ Orbiter v2.5, ✅ Turbiter motor cooling (PA1), ✅ WS7040 CPAP (PB6, confirmed). EBB36 relocation cancelled (mounts to Turbiter back). ⏳ Remaining: NTC B3950 motor thermistor (PA2), then Cartographer probe. CPAP fully done (no boot-spin on PB6). |
| Motor thermistor -> temperature_fan | After thermistor wired | NTC B3950 to EBBCan:PA2 (TH1). Then convert `[heater_fan motor_cooling_fan]` to `[temperature_fan]` for proportional Turbiter control + quantify motor temp under load. |
| Cartographer probe install | After toolhead mods | Planned |
| Guarded DEPLOY_CONFIG macro | Built, GATED on decoupled backup only (SD-card half cleared 2026-07-18) | Manual-trigger config deploy GitHub->printer. `DEPLOY_CONFIG`=preview/validate/diff, `DEPLOY_CONFIG CONFIRM=1`=apply (snapshot + restart + auto-rollback if Klipper not 'ready'). `ROLLBACK_CONFIG`=restore last snapshot. Blocks mid-print; validates non-empty+anchored files (catches zero-byte corruption); warns on SAVE_CONFIG revert. Needs gcode_shell_command ext + `~/voron-backup`. Still do NOT install until backup repo is decoupled from live config dir (row below). |
| Move backup repo off live config dir | HIGH — last DEPLOY_CONFIG prerequisite | Decouple git from `~/printer_data/config` — separate clone + copy/commit/push, so a backup-side corruption can't break Klipper's running config. |
| Move backup repo off live config dir — implementation plan ready | In progress, started 2026-07-18 | Plan: (1) fresh clone of `Voron-Trident-Backup` into `~/voron-backup-repo` (separate from `~/printer_data/config`); (2) new backup loop script rsyncs tracked config out of the live dir into that clone, then commits+pushes from there, so `~/printer_data/config` never has its own `.git`; (3) point `voron-backup.service` at the new script; (4) once confirmed working, `rm -rf ~/printer_data/config/.git` to fully decouple. Claude has no SSH/terminal access to the CB2 this session — Pedro needs to run the setup; script handed to Pedro in chat 2026-07-18. |
| ~~CB2 storage — migrate root off SD~~ | ✅ **Done 2026-07-18** | SanDisk High Endurance 32GB installed, replacing the SD32G card that failed UHS tuning and caused the 2026-06-14/06-27 corruption incidents. See Task History. |
| Confirm autobackup resumed | ⏳ Verify once decoupled backup is installed | Mystery solved — printer was powered off for several weeks (not a service failure). Back on as of 2026-07-18, local IP now `192.168.1.17`. Autobackup/stats freshness should self-resolve once it's running again; re-check after the decoupled-backup work below is in place. |
| BoxTurtle recommission | In progress | AFC live (flashed + include enabled). Lanes 1 & 2 load to hub OK. **Two open faults under diagnosis:** (a) lane 3 load-to-hub failing; (b) hub→toolhead load (T0) failing — `pin_tool_start`/PB4 not triggering. Next: hand-feed sensor-vs-drive bisection (see 2026-06-28 task entry). BTIO still pulled (bypassed). Buffer + cutter not yet active. |
| GitHub backup stale vs live (AFC) | Verify after next autobackup | Repo lags live on AFC config: backup shows `O2_sensor` active + `afc_bowden_length: 1664.61`; live has `O2_sensor` commented + `1331.16` + recalibrated `dist_hub`. If SD fails before autobackup pushes, restore = old broken AFC config. Confirm GitHub HEAD shows live values. Was tied to the now-resolved SD-card root cause (see Task History 2026-07-18) — re-verify once autobackup is confirmed running again. |
| Nozzle replacement (CHT Brass) | ~2026-07-13 | 🔴 **OVERDUE ~5 days** — installed 2026-01-13, 6-month inspection point passed. Check condition now. |
| PEI sheet check | ~2026-11-15 | Replaced ~2026-05-15 — monitor for delamination/wear at 6 months |
| Belt tension check | ~2026-08-18 | ✅ **Done 2026-07-18** — next due in ~30 days (monthly interval). |
| Leadscrew lubrication | 2026-08-29 or 275.8h | ⚠️ **Hours trigger likely already passed** — last known 270.8h was 2026-06-21; 27 days of unlogged printing since means actual hours are almost certainly over 275.8h. Confirm current hours once stats refresh, but treat as due now. |
| Rail lubrication | 2026-08-29 or 275.8h | ⚠️ **Hours trigger likely already passed** — same reasoning as leadscrew lubrication above; treat as due now. |

