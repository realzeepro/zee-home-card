# Zee Home Card

A custom Home Assistant Lovelace dashboard card forked from [Casa Luna](https://github.com/thekhan1122/casa-luna) by The Khan, customized for **Zee's** home energy setup.

Features a single-file, shadow-DOM energy dashboard with animated flow lines, weather-reactive sky backgrounds, multi-language support, and a visual editor — all in one card.

---

## Installation

### HACS (recommended)

1. Add this repository as a custom repository in HACS:
   - **URL:** `https://github.com/realzeepro/zee-home-card`
   - **Category:** Lovelace
2. Install **Zee Home Card** from HACS.
3. Register the resource in **Settings → Dashboards → Resources**:
   - URL: `/hacsfiles/zee-home-card/zee-home-card.js`
   - Resource type: **JavaScript Module**

### Manual

Copy `zee-home-card.js` into `/config/www/community/zee-home-card/` and register the resource:

```yaml
resources:
  - url: /local/community/zee-home-card/zee-home-card.js
    type: module
```

### Sky Background Images

The `sky/` folder contains 14 PNG background images hosted in the repo. Since HACS plugin-type repositories only download the single JavaScript file, the card loads sky images from **GitHub's raw CDN** by default. No manual placement needed.

The expected directory structure if you prefer local hosting:

```
/config/www/community/zee-home-card/
│
├── zee-home-card.js
└── sky/
    ├── zee-home-card-clear-day.png
    ├── zee-home-card-clear-dawn.png
    ├── zee-home-card-clear-dusk.png
    ├── zee-home-card-clear-night.png
    ├── zee-home-card-partlycloudy-day.png
    ├── zee-home-card-partlycloudy-night.png
    ├── zee-home-card-cloudy-day.png
    ├── zee-home-card-cloudy-night.png
    ├── zee-home-card-rainy-day.png
    ├── zee-home-card-rainy-night.png
    ├── zee-home-card-thunderstorm.png
    ├── zee-home-card-snowy-day.png
    ├── zee-home-card-fog-day.png
    └── zee-home-card-snowy-night.png
```

To switch to local loading, set `background_path` to `/local/community/zee-home-card/sky` in the editor and copy the `sky/` folder manually.

---

## Usage

Add the card to any Lovelace view via the **Add Card** button, then search for **Zee Home Card**. Use the visual editor or configure via YAML.

```yaml
type: custom:zee-home-card
title: 'ZEE HOME'
```

---

## Requirements

- Home Assistant 2024.x or newer (uses `hass-resources` in the panel config and modern JS features)
- A `weather_entity` — drives sky backgrounds, weather overlay, and the header weather popup

---

## Features

### Main Dashboard
- Animated energy flow lines (battery↔home, grid↔home) with live power/voltage labels
- Sun/moon arc + star field + 14 weather-reactive sky backgrounds with rain/snow/fog particles
- Battery cylinder with SOC color + tap-flip tile (stats ↔ 6 PV strings)
- 3-phase grid ↔ inverter flip tile
- EV charging banner (W, A, SOC, ETA)
- PV power/voltage tile, production/consumption history charts
- 6 configurable bottom tiles with animated room-card popups
- Recent events feed
- Header status icons: WiFi, power menu (restart/reboot/shutdown), Bluetooth, camera
- **Top-row stat tiles:** GRID (W | V), LOAD (W | V), BATTERY (W | V), CHG / DIS (energy today) — all values at 20px
- **Bottom row:** Phase/Inverter flip tile, INV LOAD donut gauge + inverter name/status, TOTAL IMP / TOTAL EXP / TOTAL PV summary tiles

### 9 Nav Views (tap left rail to open)

1. **Energy** — live power grid + inverter controls (EMS mode, export limit, backup, DOD, SoC protection sliders, grid switch, sync)
2. **Smart Plugs** — up to 6 plug toggles with power + voltage readout
3. **Battery** — Pack metrics (SoC, SOH, Index, BMS Ver, cell min/max, temps) + charge/discharge/force-charge toggles + SoC limit slider
4. **Climate** — AC card (temp steppers, mode/fan/swing chips, eco) + fridge + ambient sensors + extra slots
5. **Security** — 4 camera streams + safety sensors + doors + alarm scenes + motion alert
6. **Automation** — scenes + 4 relays + 6 extra toggles + automations toggles + Alexa + Tuya timers
7. **Lighting** — individual lights with brightness sliders + all-on/off + adaptive
8. **System** — inverter temp/mode/running hours + server CPU/memory/disk/uptime + core temps + network stats
9. **Dashboard** — returns to main view

### Other Features
- 15 languages (auto-detected from HA)
- Visual editor with 24 config sections
- Demo mode (mock data for testing)
- Calendar popup (tap clock) with event dots
- Camera fullscreen viewer
- Collapsible panels, pause-on-idle, edge dim overlay
- Shadow DOM isolated, scales to any screen width
- Auto-discovery per view for climate/security/lighting/automation entities

---

## System View

The System View has been redesigned with two sections:

### Inverter Section (4 tiles)
- Inverter Temp, Board Temp, Mode, Running Hours

### Server Section (3 rows)
- **Row 1:** CPU, Memory, Disk, Uptime
- **Row 2:** Core 1 Temp, Core 2 Temp, Package Temp
- **Row 3:** ETH0 RX/TX, WLAN0 RX/TX (network stats formatted as KB/s, MB/s, or GB/s)

Uptime is displayed in `Xd Xh` format, parsed from HA datetime strings.

---

## Configuration

All keys are configurable through the visual editor. YAML equivalents are listed for reference. Almost every caption can be renamed with a matching `label_<key>` key.

### Solar (PV)

| Key | Default | Description |
|---|---|---|
| `pv1_power` | `sensor.goodwe_pv1_power` | PV string 1 power |
| `pv2_power` … `pv6_power` | `''` | PV strings 2–6 power |
| `pv1_voltage` … `pv6_voltage` | `'sensor.goodwe_pv1_voltage'` / `''` | Per-string voltage |
| `pv_total_power` | `sensor.goodwe_pv_power` | Fallback total PV power |
| `pv_max_power` | `7500` | Max PV power for the PV capsule bar (W) |
| `_show_pv_extra` | `false` | Raises the string cap from 2 to 6 |
| `_show_pvtile` | `true` | Show/hide the PV PWR/VOLT tile |

### Grid

| Key | Default | Description |
|---|---|---|
| `grid_active_power` | `sensor.goodwe_active_power` | Grid active power |
| `grid_voltage` | `''` | Grid voltage (also used as fallback for LOAD voltage) |
| `load_voltage` | `''` | LOAD voltage (separate sensor, falls back to grid_voltage) |
| `grid_import_today` | `sensor.goodwe_today_energy_import` | Today's grid import |
| `grid_export_energy` | `''` | Today's grid export |
| `consump` | `sensor.goodwe_house_consumption` | House load |
| `invert_grid_power` | `true` | Invert sign if positive = exporting |
| `grid_phase_a/b/c` | `''` | Per-phase power (3-phase) |
| `grid_phase_a/b/c_volt` | `''` | Per-phase voltage |
| `_show_phase` | `true` | Show/hide the Grid-Phases ⇄ Inverter flip tile |

### Primary Battery

| Key | Default | Description |
|---|---|---|
| `battery_soc` | `sensor.jk_soc` | State of charge |
| `battery_power` | `sensor.jk_power` | Battery power |
| `battery_current` | `sensor.jk_current` | Battery current |
| `battery_voltage` | `sensor.jk_voltage` | Battery voltage |
| `battery_min_cell` / `battery_max_cell` | `''` | Cell voltage extremes |
| `battery_temp1` / `battery_temp2` | `''` | Cell temperature probes |
| `cell_temp_x10` | `false` | Enable if temp sensor reports 10× too low |
| `battery_mos` | `''` | BMS / MOS temperature |
| `battery_cap_unit` | `'ah'` | `ah` or `kwh` |
| `battery_full_ah` / `battery_full_wh` | `0` | Nameplate capacity (plain numbers) |
| `invert_battery_power` | `false` | Invert sign if positive = discharging |
| `_show_battstats` | `true` | Show/hide battery stats tile |

### Secondary Battery

| Key | Default | Description |
|---|---|---|
| `_show_battery2` | `false` | Enable a second pack |
| `battery2_soc` / `_power` / `_current` / `_voltage` / `_mos` | `''` | Secondary pack telemetry |
| `battery2_cap_unit` / `_full_ah` / `_full_wh` | `'ah'` / `0` / `0` | Secondary capacity |

### EV / Car Charger

| Key | Default | Description |
|---|---|---|
| `_show_ev` | `false` | Enable the EV tile |
| `charger_power` / `charger_current` / `charger_soc` | `''` | Charger power / current / car SOC |
| `charger_eta` | `''` | Charge ETA |
| `charger_battery_capacity_wh` | `0` | EV battery capacity |

### Energy Today / Totals

| Key | Default | Description |
|---|---|---|
| `total_pv` | `sensor.goodwe_total_pv_generation` | Lifetime PV generation |
| `total_import` / `total_export` | `''` | Lifetime import/export |
| `today_pv` / `today_load` | `''` / `''` | Daily PV / load |
| `today_batt_chg` | `sensor.goodwe_today_battery_charge` | Daily battery charge |
| `batt_dis` | `sensor.goodwe_today_battery_discharge` | Daily battery discharge |
| `label_total_imp` / `label_total_exp` / `label_chg_dis` | English defaults | Totals row captions |

### Weather / Sun

| Key | Default | Description |
|---|---|---|
| `weather_entity` | `weather.home` | Drives sky variant + condition + overlay |
| `weather_temp_entity` / `_wind_entity` / `_dir_entity` | `''` | Header weather detail popup |
| `sun` | `sun.sun` | Sun/moon arc position |

### Thresholds

| Key | Default | Description |
|---|---|---|
| `thresh_soc_low` / `_critical` | `25` / `15` | Battery SOC colour bands (%) |
| `thresh_temp_warn` / `_critical` | `40` / `50` | Temperature colour bands (°C) |
| `thresh_cell_v_low` / `_critical` / `_high` | `3.1` / `3.0` / `3.65` | Cell voltage bands (V) |
| `thresh_load_warn` / `_critical` | `70` / `90` | Load colour bands (%) |
| `thresh_endurance_low` / `_crit` | `2` / `1` | Endurance colour bands (hours) |

### System View Entities

| Key | Default | Description |
|---|---|---|
| `sys_inv_temp` | `''` | Inverter temperature |
| `sys_board_temp` | `''` | Board temperature |
| `sys_work_mode` | `''` | Inverter work mode |
| `sys_running_hours` | `''` | Total running hours |
| `sys_cpu` | `''` | Server CPU usage |
| `sys_memory` | `''` | Server memory usage |
| `sys_disk` | `''` | Server disk usage |
| `sys_uptime` | `''` | Server uptime (datetime string → `Xd Xh`) |
| `sys_core1_temp` | `''` | Core 1 temperature |
| `sys_core2_temp` | `''` | Core 2 temperature |
| `sys_package_temp` | `''` | Package temperature |
| `sys_eth0_rx` / `sys_eth0_tx` | `''` | ETH0 receive/transmit (raw MB/s) |
| `sys_wlan0_rx` / `sys_wlan0_tx` | `''` | WLAN0 receive/transmit (raw MB/s) |
| `sys_c3_status` | `''` | C3 status |
| `sys_gas` | `''` | Gas level |
| `sys_lux` | `''` | Light level |
| `sys_wifi` | `''` | WiFi signal |
| `sys_bluetooth` | `''` | Bluetooth |

### Toggles

| Key | Default | Hides/shows |
|---|---|---|
| `_show_bars` | `true` | Both PV / PWR capsule bars |
| `_show_phase` | `true` | The Grid-Phases ⇄ Inverter flip tile |
| `_show_battstats` | `true` | The battery stats tile |
| `_show_pvtile` | `true` | The PV PWR/VOLT tile |
| `_show_pv_extra` | `false` | PV strings 3–6 |
| `_show_ev` | `false` | The EV tile |
| `_show_battery2` | `false` | The secondary battery |

### Title & General

| Key | Default | Description |
|---|---|---|
| `title` | `'ZEE HOME'` | The only editable header text |
| `inverter_name` | `''` | Free-text label |
| `pv_max_power` | `7500` | PV bar scaling |
| `inverter_max_power` | `6000` | Inverter/PWR bar + INV LOAD % scaling |
| `lower_section_offset` | `0` | Flow-diagram vertical nudge |

### Bottom Tiles (1–6)

Each tile (`_extra_tile_N_*`, N = 1–6) has:

| Key | Description |
|---|---|
| `_extra_tile_N_enabled` | Show/hide this tile |
| `_extra_tile_N_label` | Free-text name |
| `_extra_tile_N_entity` | Any entity |
| `_extra_tile_N_icon` | Static: `home batt bolt therm shield gear sun pump irrig warn plug` · Animated: `flame snow water heat fan bulb plug` |
| `_extra_tile_6_power_entity` *(tile 6 only)* | Power sensor (W) — shows wattage below the On/Off state |

### Nav-View Sections

Climate, Security, Lighting, and Automation each have:
- Named built-in slots with a paired `_name` override
- `auto_discover_<view>` toggle (default `false`)
- 6 `<view>_extra_N_entity` + `<view>_extra_N_name` free slots

Smart Plugs has 6 `plug_N_entity` + `plug_N_name` + `plug_N_power` + `plug_N_voltage` slots.

Battery View has its own `bat_*` entities (including `bat_soh`, `bat_index`, `bat_bms_version`) — each falls back to the matching main-face `battery_*` entity if left blank.

System and Energy views are populated by their own dedicated editor sections (`sys_*`, `en_*` keys).

---

## Colour Logic

| Metric | Thresholds |
|---|---|
| **Battery SOC** | ≤15% red · ≤25% orange · >25% blue |
| **Temperature** (cell/BMS) | ≥critical red · ≥warn orange · below = green |
| **Cell Voltage** | ≤critical red · ≤low **or** ≥high orange · between = green |
| **Load** | ≥90% red · ≥70% orange · below green |

All thresholds are adjustable in the **Thresholds** editor section.

---

## Decimal Formatting

If the source value has more than 2 decimal places, it's rounded to 2; otherwise shown as the sensor reports it. Every power entity is normalized to **Watts** based on its own `unit_of_measurement` before any kW conversion.

Network speed values are displayed in auto-scaled units (KB/s → MB/s → GB/s) based on magnitude. Uptime strings are parsed from HA datetime format into `Xd Xh`.

---

## Visual Editor Sections

| Section | Notes |
|---|---|
| General | Title, inverter name, capacities, PV/inverter max power |
| Toggles | 7 show/hide switches |
| Weather & Sun | Weather entity, sun entity |
| Solar | PV strings 1–6 |
| Grid | 3-phase breakdown |
| Phase / Inverter Tile | 3-phase grid + inverter flip tile |
| Battery | Primary & secondary battery |
| Inverter | State, error, temp |
| Energy Today | Daily + lifetime energy |
| EV / Car Charger | — |
| Bottom Tiles (1–6) | Per-tile entity + name + icon |
| Thresholds | All colour bands |
| Appearance | Background, colours, opacity, blur |
| Text Sizes | Per-element font sizes |
| Cameras (go2rtc) | Camera entities |
| Energy View | Energy view entities |
| Smart Plugs View | 6 plug slots |
| Battery View | Battery view entities + fallback |
| Climate View | Entity-row model |
| Security View | Entity-row model |
| Automation View | Entity-row model + 4 relays |
| Lighting View | Entity-row model |
| Recent Events | Event center |
| System View | Inverter + Server entities |
| Testing | Demo-mode helpers |

---

## Notes

- Tested on Home Assistant OS with GoodWe ET/ES + JK BMS. Designed for a 1500 × 1000 wall tablet — not a responsive/mobile layout.
- The card uses shadow DOM — theme CSS does not penetrate. All colours are hardcoded or entity-driven.
- Sky selection requires a `weather_entity`; without it the card falls back to the clear day/night variant.
- Attribution DRM from the original Casa Luna has been removed.
- Custom element names: `zee-home-card` and `zee-home-card-editor`.
- `background_path` defaults to `https://raw.githubusercontent.com/realzeepro/zee-home-card/main/sky` so sky images load via HACS without manual folder copy. Set to a local path for offline use.
- `.detail` nav modal: percentage-centered (`left:18%; width:64%; height:84%`) with fade-in animation
- Camera grid CSS: `display:grid; grid-template-columns:1fr 1fr; gap:12px` with `.pw-cam` at `aspect-ratio:16/9` and `min-height:120px`; single camera spans full width; mobile collapses to 1 column
- Visual editor uses a single-column block layout
- Heavy Load formula: `max(houseLoad - backupLoad, 0)` — reads `en_load` and `en_backup` only

---

## Troubleshooting

### Card does not appear / "Custom element doesn't exist: zee-home-card"
- Confirm the resource is registered: **Settings → Dashboards → Resources**. You should see `/hacsfiles/zee-home-card/zee-home-card.js` (HACS) or `/local/community/zee-home-card/zee-home-card.js` (manual), type **JavaScript Module**.
- Hard refresh (`Ctrl/Cmd + Shift + R`). On the mobile app, force-close and reopen.

### Sky background missing / wrong image
- Confirm all 14 PNGs exist under `/config/www/community/zee-home-card/sky/` with exact, case-sensitive names.

### Card looks correct but shows the wrong language
- It follows Home Assistant's own language automatically. To force one, add `language: <code>` to the card YAML. Unsupported languages fall back to English.

### Entities show `--` or don't update
- Check **Developer Tools → States**: the entity must exist and return a numeric value (not `unavailable`/`unknown`). Entity IDs are case-sensitive.

### Network stats show raw MB/s instead of auto-scaled
- Ensure `sys_eth0_rx` / `sys_wlan0_rx` etc. point to sensors reporting raw MB/s. The card auto-scales to KB/s or GB/s as needed.

---

*Zee Home Card v1.2.0 — forked from Casa Luna by The Khan*
