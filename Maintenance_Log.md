# Voron Trident 250 — Maintenance Log
*Last updated: 2026-06-14*

> **Note on print stats:** Stats auto-fetched every 60s by `autobackup.sh` via `http://localhost/server/history/list?limit=9999` and saved to `print_stats.json`.

---

## Current Stats
| Metric | Value | Last checked |
|--------|-------|-------------|
| Total print time | 258.6 hours | 2026-06-13 |
| Total jobs | 755 | 2026-06-13 |
| Filament used (total) | 1709.6 meters | 2026-06-13 |
| Most used filament | ASA 74% (1180m / 490 jobs) | 2026-05-29 |

---

## Filament Usage Breakdown
*Last updated: 2026-06-14*

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
| Move backup repo off live config dir | Recommended (post-corruption) | Decouple git from `~/printer_data/config` — separate clone + copy/commit/push, so a backup-side corruption can't break Klipper's running config. |
| CB2 storage — migrate root off SD | HIGH (root cause of 2026-06-14 corruption) | dmesg 2026-06-14: root is on SD `mmcblk0p2` (SD32G); card **failed UHS tuning on boot, recovered on retry** (`All phases bad! / error -5`). Healthy 32GB eMMC `mmcblk1` (PJ3032, HS200) sits unused. Fix: swap to high-endurance SD (quick) OR migrate root to eMMC (best, 24/7 service). Also drop autobackup 60s -> 5-15min to shrink corruption window. |
| BoxTurtle recommission | After toolhead mods | Paused |
| Nozzle replacement (CHT Brass) | ~2026-07-13 | Installed 2026-01-13 — monitor condition at 6 months |
| PEI sheet check | ~2026-11-15 | Replaced ~2026-05-15 — monitor for delamination/wear at 6 months |
| Belt tension check | 2026-06-29 | ✅ Done 2026-05-29 — next in ~1 month |
| Leadscrew lubrication | 2026-08-29 or 275.8h | ⏳ Approaching — at 258.6h, ~17 print-hrs out |
| Rail lubrication | 2026-08-29 or 275.8h | ⏳ Approaching — at 258.6h, ~17 print-hrs out |

