# Voron Trident 250 — Maintenance Log
*Last updated: 2026-05-29*

> **Note on print stats:** Moonraker API is not reachable from outside your local network.
> To get current print time/job count, open Mainsail → History and check totals manually,
> or run on CB2: `curl -s http://localhost/server/history/summary`

---

## Current Stats
| Metric | Value | Last checked |
|--------|-------|-------------|
| Total print time | *fill in* | *fill in* |
| Total jobs | *fill in* | *fill in* |
| Filament used (total) | *fill in* | *fill in* |

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
- [ ] **Orbiter v2.5 upgrade** — when doing Turbiter/CPAP mod (parts on hand)

---

## Task History
| Date | Print Hours | Task | Notes |
|------|-------------|------|-------|
| ~2025 | — | SGT raised 2→3 | Sensorless homing early trigger on X |
| ~2025 | — | Auto-z plugin removed | Persistent conflicts; switched to standard z_offset |
| ~2025 | — | zero_reference_position corrected | Was 165,259 (Sexbolt), changed to 125,125 (bed center) |
| ~2025 | — | TRSYNC_TIMEOUT patched 0.025→0.075 | CAN bus timing fix |
| ~2025 | — | Input shaper calibrated | X=MZV 72.8Hz, Y=EI 64.4Hz |
| 2026-05-29 | — | OrcaSlicer backup set up | Daily auto-backup to Pedro1304/OrcaSlicer-Backup |
| *fill in* | — | Last belt tension check | — |
| *fill in* | — | Last lubrication | — |
| *fill in* | — | Last nozzle change | — |

---

## Upcoming / Overdue
*Updated each session based on last task dates and print hours*

| Task | Due | Status |
|------|-----|--------|
| Orbiter v2.5 + Turbiter + CPAP install | When parts ready | Pending — parts on hand |
| Cartographer probe install | After toolhead mods | Planned |
| BoxTurtle recommission | After toolhead mods | Paused |
| Belt tension check | *fill in last date + 1 month* | Unknown — date needed |
| Leadscrew lubrication | *fill in last date + 1 month* | Unknown — date needed |
| Rail lubrication | *fill in last date + 1 month* | Unknown — date needed |

