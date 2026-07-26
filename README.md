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

The `sky/` folder with 14 PNG background images is bundled in the repository and **downloaded automatically by HACS** — no manual placement needed. The expected structure after HACS installs:

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

## Visual Overview

- **Main Dashboard:** Grid ↔ Battery ↔ PV animated flow diagram with live power values
- **Nav Views:** Energy, Smart Plugs, Battery, Climate, Security, Lighting, Automation, System, Recent Events
- **Weather Sky:** Auto-switches between 14 backgrounds (clear/cloudy/rainy/snow/thunder + day/night/dawn/dusk) based on your HA weather entity
- **Battery Flip Tile:** Tap to toggle between live stats (front) and PV string details (back) — pure show/hide, no CSS 3D transforms
- **Energy Charts:** History graphs for daily load, PV production, battery charge/discharge, and grid import/export

---

## System View

The System View has been redesigned with two sections:

### Inverter Section (4 tiles)
- State, Error, Temp, Lifetime

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
| `grid_voltage` | `''` | Grid voltage |
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
| `sys_inverter_state` | `''` | Inverter state |
| `sys_inverter_error` | `''` | Inverter error code |
| `sys_inverter_temp` | `''` | Inverter temperature |
| `sys_inverter_lifetime` | `''` | Lifetime energy |
| `sys_server_cpu` | `''` | Server CPU usage |
| `sys_server_mem` | `''` | Server memory usage |
| `sys_server_disk` | `''` | Server disk usage |
| `sys_server_uptime` | `''` | Server uptime (datetime string → `Xd Xh`) |
| `sys_core1_temp` | `''` | Core 1 temperature |
| `sys_core2_temp` | `''` | Core 2 temperature |
| `sys_package_temp` | `''` | Package temperature |
| `sys_eth0_rx` / `sys_eth0_tx` | `''` | ETH0 receive/transmit (raw MB/s) |
| `sys_wlan0_rx` / `sys_wlan0_tx` | `''` | WLAN0 receive/transmit (raw MB/s) |

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

### Nav-View Sections

Climate, Security, Lighting, and Automation each have:
- Named built-in slots with a paired `_name` override
- `auto_discover_<view>` toggle (default `false`)
- 6 `<view>_extra_N_entity` + `<view>_extra_N_name` free slots

Smart Plugs has 6 `plug_N_entity` + `plug_N_name` + `plug_N_power` slots.

Battery View has its own `bat_*` entities — each falls back to the matching main-face `battery_*` entity if left blank.

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
| Phase / Inverter Tile | Inverter output power |
| Battery | Secondary battery |
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
- `background_path` defaults to `/local/community/zee-home-card/sky`.

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

*Zee Home Card v1.1.0 — forked from Casa Luna by The Khan*
