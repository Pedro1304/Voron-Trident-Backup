# Voron Trident 250 — Build Information Hub
*Last updated: 2026-06-14*

---

## Compute & Control
- **Mainboard:** BTT Octopus Pro (CAN UUID: `24de3c7cb71e`)
- **SBC:** BTT CB2, username `biqu`, timezone `Europe/Lisbon`
- **Software:** Mainsail, Moonraker, Klipper, Spoolman (Docker), Tailscale
- **GitHub backup:** `Pedro1304/Voron-Trident-Backup` via SSH key `~/.ssh/github_backup`, service `voron-backup.service`
- **Manual backup trigger:** `sudo systemctl start voron-backup.service`
- **Systemd note:** CB2 interactive editor always saves empty files — use `sudo tee ... << 'EOF'` for all systemd overrides

## Network & Remote Access
- **Local hostname:** `bigtreetech-cb2`
- **Mainsail (local):** `http://bigtreetech-cb2.local`
- **Remote access:** Tailscale — IP: `100.91.164.122`
- **Mainsail (remote):** `http://100.91.164.122`
- **Moonraker auth:** Tailscale subnet `100.64.0.0/10` added to trusted_clients
- **Spoolman:** Running via Docker on port 8000 — `http://192.168.1.14:8000` — 11 spools tracked, exported to `spoolman_stats.json` every 60s

## Display
- **BTT HDMI5 V1.2** — 5" IPS touchscreen, HDMI + USB touch
- KlipperScreen running

## Camera
- **Angry CAM USB V2** (chri.kai.in mod) — Waveshare OV5648 5MP USB, mounted to frame/gantry
- **Streamer:** Crowsnest (ustreamer), port 8080, /dev/video10, 1920×1080 @ 30fps
- **Stream URL (local):** `http://bigtreetech-cb2.local/webcam/?action=stream`
- **Snapshot URL (local):** `http://bigtreetech-cb2.local/webcam/?action=snapshot`
- **Stream URL (remote):** `http://100.91.164.122/webcam/?action=stream`

## Frame & Enclosure
- **Frame:** Self-sourced Voron Trident 250
- **Enclosure panels:** Acrylic
- **Feet:** Hula feet (anti-vibration silicone) — installed 2026-05-27
- **Surface:** Unstable table — long term fix needed, accepted for now
- All input shaper values calibrated with hula feet in place — recalibrate if feet are removed/replaced

## CAN Bus Network
- **Interface:** `can0`, bitrate: `1000000` (1Mbit)
- **Topology:** Octopus Pro (bridge) → EBB36 → (Cartographer spur, when added)
- **Octopus Pro UUID:** `24de3c7cb71e`
- **EBB36 UUID:** `b8138acd0e11`
- **Note:** Cannot flash EBB36 over CAN while Cartographer is connected — unplug CAN-H/L first

## Power System
- **Main PSU:** Meanwell LRS-350-24 (salvaged from Ender 3 Pro) — 24V, 350W, 14.6A
- **5V buck converter:** Powers HW-104 amp and LED strip logic — GND shared with Octopus
- **Chamber heater:** PTC 24V 150W — powered directly from 24V rail
- **WS7040 CPAP driver:** Mellow driver board — 24V+/GND from main rail, GND common with Octopus (<1A draw, OK on LRS-350 for now)
- **Bed heater:** Silicone 110/220V 500W — mains powered via SSR

## Motion
- **Kinematics:** CoreXY
- **Build volume:** 250×250×220mm

### X/Y Motors
- **Model:** LDO-42STH48-2804AH(S40R) — NEMA17, 40mm body, 2.8A rated, 180°C rated, long shaft
- **Driver:** TMC5160T Pro at 2.5A run / 0.5A hold, 32 microsteps, stealthchop off, interpolation on
- **Homing:** Sensorless (StallGuard4), SGT=4, 25mm/s, 2.5A — tuned for 55°C chamber, homing_retract_dist=0
- **Homing macro:** Triple G28 — after first home moves Y+100mm, Z-10mm, then homes twice more for repeatability

### Z Motors (×3)
- **Model:** Siboor 42STH26-0804A-200 — NEMA17, TR8×4 leadscrew 300mm, 1.0A rated
- **Driver:** TMC2209 at 0.8A, 32 microsteps, stealthchop off, interpolation off
- **rotation_distance:** 4 (TR8×4)
- **Leveling:** Z-tilt, 7 retries, tolerance 0.015mm

### Speed & Acceleration Limits
- **max_velocity:** 1100 mm/s
- **max_accel:** 19000 mm/s²
- **max_z_velocity:** 15 mm/s
- **max_z_accel:** 350 mm/s²
- **square_corner_velocity:** 5.0 mm/s
- **Bed mesh speed:** 250 mm/s
- **Z-tilt speed:** 175 mm/s

### Input Shaper
- **X:** MZV @ 71.0Hz
- **Y:** MZV @ 52.4Hz
- ShakeTune installed, calibration kept manual (SAVE_CONFIG restarts Klipper)
- **Last calibrated:** 2026-05-29 (with hula feet installed — values will differ from pre-feet calibration)

## Toolhead — XOL
### Extruder
- **Current:** Orbiter v2.5 — **upgraded 2026-06-14** from v2.0 (installed alongside Turbiter/CPAP)
- ⚠️ **Recalibrate after v2.5 swap:** verify extruder `rotation_distance` (e-steps) and re-validate ASA flow ratio (was 0.938 on v2.0); confirm motor model/run_current if the kit changed the stepper
- **Driver:** TMC2209 on EBB36, 0.85A run / 0.1A hold
- **rotation_distance:** 4.637, 200 full steps, 16 microsteps
- **Thermal issue:** Motor reached ~105–110°C in 60°C chamber at 0.85A — **Turbiter 3010 motor cooling installed 2026-06-14** (EBBCan:PA1). Verify motor temp under load; wire NTC B3950 to PA2 to quantify.

### Hotend
- **Phaetus Rapido v2**
- **Nozzle:** Bondtech CHT Coated Brass 0.4mm (Nickel coated, high flow, V6 compatible) — installed 2026-01-13
- **⚠️ CHT warning:** Do NOT cold pull — damages nickel coating. Hot purge only (extrude at high temp).
- **Thermistor:** EPCOS 100K B57560G104F (NTC), pin `EBBCan:PA3` (TH0)
- **Max temp:** 290°C
- **Pressure advance:** 0.015, smooth time 0.03

### Firmware Retraction
- **Retract length:** 0.5mm
- **Retract speed:** 40 mm/s
- **Unretract extra length:** 0mm
- **Unretract speed:** 40 mm/s

### Toolhead Board
- **BTT EBB36 v1.2** (CAN UUID: `b8138acd0e11`) on `can0`
- **ADXL345:** SPI2, CS `EBBCan:PB12`
- **Hotend heater:** `EBBCan:PB13`
- **Part cooling fan (CPAP):** WS7040 on **Octopus `PB6`** (confirmed final 2026-06-14; earlier PG15/PG12 attempts abandoned). `[fan_generic Part_Cooling]`, M106/M107 remapped to it. Frame-mounted blower via Mellow driver
- **Turbiter motor cooling fan:** `EBBCan:PA1` (3010 blower) — `[heater_fan motor_cooling_fan]`, tied to extruder @ 50°C, full power. Installed 2026-06-14. Reuses the pin freed when part cooling moved to CPAP
- **Hotend fan:** `EBBCan:PA0` — `[heater_fan hotend_fan]`, extruder @ 50°C
- **Probe pin:** `EBBCan:PB9`
- **Orbiter runout sensor:** `EBBCan:PB4`
- **Orbiter unload button:** `EBBCan:PB3`
- **Motor thermistor (pending):** `EBBCan:PA2` (TH1) — NTC B3950 in Turbiter housing pocket

### Pending Toolhead Mods (all at once)
- ✅ Orbiter v2.5 install — **done 2026-06-14**
- ✅ Turbiter 3010 blower motor cooling — **installed 2026-06-14**, EBBCan:PA1
- ✅ CPAP part cooling — **Mellow FLY-7040 / WS7040-24V** (24V brushless centrifugal blower, 6.5kPa, 45000RPM) — **blower fully installed 2026-06-14.** **Wiring:** frame-mounted; driver signal (Signal, pot removed) -> Octopus **PB6** (confirmed final; NOT a fan MOSFET); leave driver VCC unconnected; 24V+/- to main rail (⚠️ marked polarity, do NOT reverse — damages fan). Part_Cooling reassigned off EBBCan:PA1 -> Octopus PB6. ⚠️ Boot-spin fix: add `!PB6` to Octopus menuconfig 'GPIO pins to set at MCU startup', recompile + reflash. Bench-verify SPEED=0=OFF / SPEED=1=FULL.
  - **Driver pinout:** Motor-out (3φ, pre-wired to blower) | Power-in +/- (2-pin) | Control VCC/Signal/GND (pot connector — use Signal+GND only for MCU)
  - **Klipper (Mellow reference):** `max_power: 0.9`, `cycle_time: 0.002`, `hardware_pwm: false`, `off_below: 0.2`, `kick_start_time: 0.3`, `shutdown_speed: 0`
  - **⚠️ Auto-startup spin:** blower runs full speed on MCU boot until Klipper connects. Fix: add `!PG15` to Octopus firmware menuconfig 'GPIO pins to set at micro-controller startup', recompile + reflash.
  - **⚠️ Verify polarity on bench:** SPEED=0 must be OFF, SPEED=1 must be FULL. If reversed, set `pin: !PG15`. (Mellow docs are AI-translated and internally inconsistent on polarity.)
  - **Pin choice history:** PG12 rejected (Stop4/M4DIAG = stepper_z1 slot, DIAG contention). PC5 then tried — FAILED: pin sits stuck-high, Klipper cannot pull it low, blower ran constant-on regardless of SPEED or polarity (PC5 is committed to an onboard function, not a free GPIO). **Final: PG15 (Stop7 / M7DIAG, empty slot)** — clean endstop GPIO, same pin class as Bondtech's proven PG12. Other free options: PG11 (M3), PG14 (M6).
- ~~EBB36 side-mount relocation~~ — **NOT NEEDED** (2026-06-14): EBB36 mounts to the back of the Turbiter; no relocation required
- NTC B3950 motor thermistor wired to `EBBCan:PA2`

## Probe & Z Endstop
- **Probe:** Klicky, pin `^EBBCan:PB9`, Y offset +23mm, z_offset=8.15 (saved in SAVE_CONFIG)
- **Z endstop:** Sexbolt with Omron D2HW switch (~3.4mm travel, not D2F-5)
- **Auto-z plugin (protoloft):** Removed — caused persistent conflicts with Klicky + mesh. Using standard Klipper probe z_offset.
- **zero_reference_position:** 125,125 (bed center — critical, was previously wrong at 165,259)
- **safe_z_home:** 165,259

## Bed & Thermal
- **Bed plate:** 6061 aluminium 8mm, 254×254mm, magnetic PEI spring steel sheet
- **Bed heater:** Silicone 110/220V 500W (254×254mm), mains via SSR
- **Bed thermistor:** ATC Semitec 104GT-2, max 130°C
- **Mesh:** 5×5, min 5,24 / max 245,244, KAMP adaptive meshing enabled
- **Chamber sensor:** SHT31 (SHT3X compatible), I2C, scl `PB8`, sda `PB9`
- **Chamber heater:** PTC 24V 150W (insulated, with built-in fan), heater pin `PA0`, fan `PE5`, PID controlled, max 70°C (software limit)
- **Bed fans:** `PD15` — 100% when bed target active, scales with bed temp otherwise
- **Intake fans:** `PA8` — auto-managed, min 25%, ramps 40→100% between 50–65°C controller temp
- **Exhaust fans:** `PD12`, `PD13` — same logic as intake
- **Heater fan:** `PE5` — runs while chamber heater active, 45s cooldown after

## Print Materials & Typical Settings
| Material | Hotend | Bed | Chamber | PA | Notes |
|----------|--------|-----|---------|-----|-------|
| ASA (Polymaker/eSun) | 260°C | 100°C | 55°C | per filament from slicer | Main material (74% of all prints). Flow ratio 0.938, max vol speed 23mm³/s |
| PETG (Sunlu) | 250°C | 80°C | Off / <40°C | 0.045 | Flow ratio 0.96, max vol speed 25mm³/s. Door cracked if chamber >40°C |
| PLA (Sunlu/Polymaker) | 210°C | 55°C | Off | per filament from slicer | — |
| TPU (Polyflex TPU90) | 220°C | 35°C | Off | 0.1 | Flow ratio 0.99. Polymaker brand |

## Slicer
- **OrcaSlicer** (Windows)
- **Printer profile name:** Voron Trident 250 0.4 nozzle
- **Pressure advance:** Set per filament in OrcaSlicer profiles (not globally in Klipper) — PETG=0.045, TPU=0.1, ASA/PLA=per profile
- **Config backup:** `Pedro1304/OrcaSlicer-Backup` (private GitHub repo)
- **Backed up folders:** `user`, `system`, `printers`, `plugins`
- **Backup script:** `C:\Users\pmade\Documents\orca-backup.ps1`
- **Schedule:** Daily at 12:00 via Windows Task Scheduler ("OrcaSlicer Backup" task)
- **Manual trigger:** `Start-ScheduledTask -TaskName "OrcaSlicer Backup"` (run in PowerShell)

## Lighting, Audio & UI

### Chamber LEDs
- 32× GRB NeoPixel chain, Octopus `PB10`
- Red = standby, Cyan = printing, Cyan↔Orange pulse on pause

### Physical Buttons
- **LED brightness cycle:** Octopus `PD6` — cycles 0/25/50/75/100% white via `CYCLE_LED_BRIGHTNESS`
- **Planned:** More 12mm metal illuminated buttons (12-24V, self-reset) for other tasks

### Audio
- **Amp:** HW-104 (PAM8403), 5V, 3W/channel
- **Signal pin:** Octopus `PB0` (PWM, `[output_pin beeper]`)
- **Wiring:** 5V from buck converter, GND shared with Octopus, signal PB0, L IN- tied to GND
- **Capability:** Monophonic only (single PWM tone) — multi-tone needs ESP32+I2S DAC (future)
- **Macros:** M300, CHIME_START, CHIME_DONE, CHIME_CANCEL, CHIME_PAUSE, SET_VOLUME, boot chime

## Multi-Material (Paused)
- **Unit:** BoxTurtle 4-lane, top-mounted
- **Controller:** AFC-Lite board with lane control buttons (not working yet)
- **Buffer:** TurtleNeck Pro
- **Cutter:** FilamATrix (planned)
- **Software:** AFC-Klipper Add-On (`[include AFC/*.cfg]` in printer.cfg)
- **Status:** Uninstalled/paused — unreliable, resuming after toolhead mods complete
- **Planned integration:** Happy Hare + Spoolman pull mode

## Planned Mods Roadmap
1. **Toolhead overhaul** — ✅ Orbiter v2.5 + Turbiter + CPAP done (2026-06-14); remaining: motor thermistor (PA2)
2. **Cartographer probe** — replace Klicky with Cartographer CAN (eddy current, touch mode)
3. **BoxTurtle recommission** — AFC lane control fix, FilamATrix cutter, Happy Hare integration
4. **Audio upgrade** — ESP32 + I2S DAC for polyphonic audio (future)
5. **More physical buttons** — 12mm illuminated buttons for additional printer functions

## Installed Plugins & Software
| Plugin | Purpose | Update Manager |
|--------|---------|---------------|
| KAMP (Klipper Adaptive Meshing & Purging) | Adaptive bed mesh | `Klipper-Adaptive-Meshing-Purging` |
| ShakeTune (Klippain) | Input shaper analysis & graphs | `Klippain-ShakeTune` |
| AFC-Klipper-Add-On | BoxTurtle multi-material | `afc-software` |
| KlipperScreen | Touchscreen UI | `KlipperScreen` |
| Crowsnest | Webcam streaming | `crowsnest` |
| Sonar | WiFi keepalive daemon (enabled) | `sonar` |
| print_area_bed_mesh | Bed mesh by print area | `print_area_bed_mesh` |
| Mainsail | Web UI | `mainsail` |
| Spoolman | Filament spool tracking | Docker container |


## Key Config Files
| File | Purpose |
|------|---------|
| `printer.cfg` | Main config, macros, LEDs, fans, bed mesh, z_tilt |
| `_BTT Octopus Pro.cfg` | Mainboard pins, steppers, drivers, chamber thermal |
| `_ebb36.cfg` | Toolhead board, extruder, probe, fans |
| `_klicky-probe.cfg` | Klicky includes |
| `klicky-variables.cfg` | Klicky dock position, speeds |
| `klicky-macros.cfg` | Attach/dock macros |
| `klicky-z-tilt-adjust.cfg` | Z tilt with Klicky |
| `klicky-bed-mesh-calibrate.cfg` | Mesh with Klicky |
| `Orbiter2_SmartSensor.cfg` | Filament runout/autoload macros |
| `KAMP_Settings.cfg` | Adaptive mesh settings |
| `AFC/*.cfg` | BoxTurtle / multi-material configs (paused) |

## Useful Commands
```bash
# Manual config backup to GitHub
sudo systemctl start voron-backup.service

# Check CAN bus devices
~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0

# Re-apply TRSYNC patch after Klipper update
sed -i 's/TRSYNC_TIMEOUT = 0.025/TRSYNC_TIMEOUT = 0.075/' ~/klipper/klippy/mcu.py
sudo systemctl restart klipper

# Check Spoolman status
sudo docker ps | grep spoolman

# Tailscale status
tailscale status

# Get print stats summary
curl -s "http://localhost/server/history/list?limit=9999" | python3 -c "
import json,sys
data=json.load(sys.stdin); jobs=data['result']['jobs']
print(f'Jobs: {len(jobs)}, Hours: {sum(j[\"print_duration\"] for j in jobs)/3600:.1f}, Filament: {sum(j[\"filament_used\"] for j in jobs)/1000:.1f}m')
"

# Run input shaper calibration (both axes)
# Home and level first, then:
AXES_SHAPER_CALIBRATION
# Results saved to ~/printer_data/config/ShakeTune_results/
```

## Maintenance
See **Maintenance_Log.md** in this repo for full schedule, task history, filament breakdown, and upcoming/overdue tasks.

## Known Issues & History
- **Klipper DIRTY:** `klippy/mcu.py` intentionally modified — `TRSYNC_TIMEOUT` changed from `0.025` to `0.075` (line ~256) to fix CAN bus timing. If lost after Klipper update, re-apply with the command in Useful Commands above.
- **CB2 systemd editor:** Always saves empty files — use `sudo tee` instead
- **Spoolman boot race:** Fixed via Moonraker systemd override (30s delay + After=docker.service)
- **Sensorless homing:** SGT was 2, raised to 3 after early X trigger
- **Auto-z plugin:** Removed after extensive issues; zero_reference_position at Sexbolt location was corrupting mesh offsets
- **BoxTurtle:** Uninstalled — unreliable, AFC lane control buttons not working
- **HW-104 amp:** Monophonic only — ESP32+I2S needed for multi-tone (future project)
- **ADXL345 SPI interference during ShakeTune:** At high frequencies (>125Hz), `spi_transfer_response` errors cause Klipper shutdown mid-sweep. **Fixed** by adding `MAX_FREQ=125` to calibration — wrapped in `AXES_SHAPER_CALIBRATION` macro override so it applies automatically. Resonant frequencies (71Hz/52Hz) are well below this cap so no data is lost. Monitor if errors return at lower frequencies.
