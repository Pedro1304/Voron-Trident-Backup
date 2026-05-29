# Voron Trident 250 — Hardware & Software Profile
*Last updated: 2026-05-29*

## Compute & Control
- **Mainboard:** BTT Octopus Pro (CAN UUID: `24de3c7cb71e`)
- **SBC:** BTT CB2, username `biqu`, timezone `Europe/Lisbon`
- **Software:** Mainsail, Moonraker, Klipper, Spoolman (Docker), Tailscale
- **GitHub backup:** `Pedro1304/Voron-Trident-Backup` via SSH key `~/.ssh/github_backup`, service `voron-backup.service`
- **Systemd note:** CB2 interactive editor always saves empty files — use `sudo tee ... << 'EOF'` for all systemd overrides

## Display
- **BTT HDMI5 V1.2** — 5" IPS touchscreen, HDMI + USB touch
- KlipperScreen running, HelixScreen installed but inactive

## Camera
- **Angry CAM USB V2** (chri.kai.in mod) — Waveshare OV5648 5MP USB, mounted to frame/gantry

## Motion
- **Kinematics:** CoreXY
- **Build volume:** 250×250×220mm

### X/Y Motors
- **Model:** LDO-42STH48-2804AH(S40R) — NEMA17, 40mm body, 2.8A rated, 180°C rated, long shaft
- **Driver:** TMC5160T Pro at 2.5A run / 0.5A hold, 32 microsteps, stealthchop off
- **Homing:** Sensorless (StallGuard4), SGT=3, 45mm/s, homing_retract_dist=0
- **Homing macro:** Triple G28 — after first home moves Y+100mm, Z-10mm, then homes twice more for repeatability

### Z Motors (×3)
- **Model:** Siboor 42STH26-0804A-200 — NEMA17, TR8×4 leadscrew 300mm, 1.0A rated
- **Driver:** TMC2209 at 0.8A, 32 microsteps, stealthchop off
- **rotation_distance:** 4 (TR8×4)
- **Leveling:** Z-tilt (3-motor Trident)

### Input Shaper
- X = MZV @ 72.8Hz
- Y = EI @ 64.4Hz
- ShakeTune installed, calibration kept manual (SAVE_CONFIG restarts Klipper)

## Toolhead — XOL
### Extruder
- **Current:** Orbiter v2.0 (LDO-36STH20-1004AHG motor)
- **Pending:** Orbiter v2.5 upgrade kit on hand — install together with Turbiter/CPAP mod
- **Driver:** TMC2209 on EBB36, 0.85A run / 0.1A hold
- **rotation_distance:** 4.637, 200 full steps, 16 microsteps
- **Thermal issue:** Motor reaches ~105–110°C in 60°C chamber at 0.85A — Turbiter cooling pending

### Hotend
- **Phaetus Rapido v2**, 0.4mm nozzle
- **Thermistor:** EPCOS 100K B57560G104F (NTC), pin `EBBCan:PA3` (TH0)
- **Max temp:** 290°C
- **Pressure advance:** 0.015, smooth time 0.03

### Toolhead Board
- **BTT EBB36 v1.2** (CAN UUID: `b8138acd0e11`) on `can0`
- **ADXL345:** SPI2, CS `EBBCan:PB12`
- **Hotend heater:** `EBBCan:PB13`
- **Part cooling fan:** `EBBCan:PA1`
- **Hotend fan:** `EBBCan:PA0`
- **Probe pin:** `EBBCan:PB9`
- **Orbiter runout sensor:** `EBBCan:PB4`
- **Orbiter unload button:** `EBBCan:PB3`
- **Motor thermistor (pending):** `EBBCan:PA2` (TH1) — NTC B3950 in Turbiter housing pocket

### Pending Toolhead Mods (doing all at once)
- Orbiter v2.5 install
- Turbiter 3010 blower motor cooling
- CPAP part cooling
- EBB36 relocation to side mount (current position conflicts with Turbiter)
- NTC B3950 motor thermistor wired to `EBBCan:PA2`

## Probe & Z Endstop
- **Probe:** Klicky, pin `^EBBCan:PB9`, Y offset +23mm, z_offset=8.15 (saved in SAVE_CONFIG)
- **Z endstop:** Sexbolt with Omron D2HW switch (~3.4mm travel, not D2F-5)
- **Auto-z plugin (protoloft):** Removed — caused persistent conflicts with Klicky + mesh. Using standard Klipper probe z_offset.
- **zero_reference_position:** 125,125 (bed center — critical, was previously wrong at 165,259)
- **safe_z_home:** 165,259

## Bed & Thermal
- **Bed:** 250mm, 6061 aluminium 8mm plate 254×254mm, magnetic PEI spring steel sheet
- **Bed heater:** Silicone 110/220V 500W (254×254mm)
- **Thermistor:** ATC Semitec 104GT-2, max 130°C
- **Mesh:** 5×5, min 5,24 / max 245,244, KAMP adaptive meshing enabled
- **Chamber sensor:** SHT31 (SHT3X compatible), I2C, scl `PB8`, sda `PB9`
- **Chamber heater:** PTC 24V 150W (insulated, with built-in fan), heater pin `PA0`, fan `PE5`, PID controlled, max 70°C (software limit)
- **Bed fans:** `PD15` — auto-managed, 100% when bed target active
- **Intake fans:** `PA8`
- **Exhaust fans:** `PD12`, `PD13`
- All fans auto-managed via `UPDATE_FANS` macro based on bed/chamber/controller temps

## Lighting, Audio & UI

### Chamber LEDs
- 32× GRB NeoPixel chain, Octopus `PB10`
- Red = standby, Cyan = printing, Pulse animation (cyan↔orange) on pause, transition animation on print start

### Physical Buttons
- **LED brightness cycle button:** Octopus `PD6` — cycles 0/25/50/75/100% white brightness via `CYCLE_LED_BRIGHTNESS` macro
- **Planned:** More buttons for different tasks (e.g. power button style — 12mm metal illuminated, 12-24V self-reset)

### Audio
- **Amp:** HW-104 (PAM8403), 5V, 3W/channel
- **Signal pin:** Octopus `PB0` (PWM, `[output_pin beeper]`)
- **Wiring:** 5V from buck converter, GND shared with Octopus, signal from PB0, L IN- tied to GND
- **Capability:** Monophonic only (single PWM tone at a time) — multi-tone would require ESP32+I2S DAC (future project)
- **Macros:** M300 (tone), CHIME_START, CHIME_DONE, CHIME_CANCEL, CHIME_PAUSE, SET_VOLUME, boot chime on startup

## Multi-Material (Paused)
- **Unit:** BoxTurtle 4-lane, top-mounted
- **Controller:** AFC-Lite board with lane control buttons (not working yet)
- **Buffer:** TurtleNeck Pro
- **Cutter:** FilamATrix (planned)
- **Software:** AFC-Klipper Add-On (`[include AFC/*.cfg]` in printer.cfg)
- **Status:** Uninstalled/paused — unreliable, focusing on toolhead mods first
- **Planned integration:** Happy Hare + Spoolman pull mode

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

## Known Issues & History
- Klipper shows "DIRTY" warning in Mainsail update manager — `klippy/mcu.py` has one intentional change: `TRSYNC_TIMEOUT` increased from `0.025` to `0.075` (line ~256). This gives the CAN bus more time to respond during homing and prevents spurious "Timer too close" errors. Safe to ignore the dirty warning. If Klipper is ever updated and the patch is lost, re-apply this change manually.
- CB2 systemd interactive editor saves empty files — always use `sudo tee`
- Spoolman has boot race condition with Docker — fixed via Moonraker systemd override (30s delay + After=docker.service)
- Sensorless homing SGT was 2, raised to 3 after early-trigger on X
- Auto-z plugin removed after extensive troubleshooting; zero_reference_position was corrupting mesh offsets
- HelixScreen install attempted but service not found; currently on KlipperScreen
- BoxTurtle uninstalled — unreliable operation, AFC lane control buttons not working
- HW-104 amp is monophonic only; multi-tone audio requires different hardware (ESP32+I2S, future)
