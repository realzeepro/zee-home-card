# Zee Home Card — Project Knowledge

## Overview
Single-file Home Assistant Lovelace custom card (`zee-home-card.js`, ~5700 lines). Fork of Casa Luna by The Khan. Shadow DOM, viewBox 1500×1000. Weather-reactive sky backgrounds, animated flow lines, navigation modal with 9 views.

## Key Architecture
- **IIFE** wrapping a `LitElement`-like class (`ZeeHomeCard`) + editor class
- **`SL`** object (line ~449) — slot layout constants `[x, y, w, h]` for every positioned element
- **All styles in one CSS template string** in `render()` (around line 1200)
- **9 nav views**: security, climate, energy, plugs, battery, automation, lighting, system, dashboard
- **Config defaults** at line ~640, editor at line ~5200
- **A_Y flow-line constant** (line ~2053) auto-calculated from `SL.r_stats[1]`
- `.detail` nav modal: percentage-centered (`left:18%; width:64%; height:84%`) with fade-in animation
- Camera grid CSS: `display:grid; grid-template-columns:1fr 1fr; gap:12px` with `.pw-cam` at `min-height:130px`
- Visual editor: single-column block layout
- Heavy Load formula: `max(houseLoad - backupLoad, 0)` — reads `en_load` and `en_backup` only

## Recent Changes (commits on main)

### Top-Row Stat Tiles — redesigned as Power + Voltage
- **LOAD** (id=`load`): shows `consump` power (W) + `grid_voltage` (V) — blue glow
- **GRID** (id=`grid`, was `gimp`): shows `grid_active_power` (W) + `grid_voltage` (V) — orange glow. Label `c.label_grid || 'GRID'`
- **BATTERY** (id=`batt`, was `gexp`): shows `battery_power` (W) + `battery_voltage` (V) — green glow. Label `c.label_battery || 'BATTERY'`
- **CHG/DIS** (id=`chgdis`, unchanged): shows `today_batt_chg` / `batt_dis` (kWh)
- Each tile now displays two values side by side: power and voltage separated by `|`
- Added `_fmtVoltage(id)` helper for consistent voltage formatting
- Tap handler updated: `grid` → `grid_active_power`, `batt` → `battery_power`
- New config keys: `label_grid`, `label_battery` (with editor entries)
- Voltage fallback entities: `grid_voltage` (LOAD/GRID), `battery_voltage` (BATTERY)

### EMS Box Removed — INV LOAD shifted left
- **EMS MODE + OPERATION box** (conditional block when `_show_phase=false`) deleted entirely
- `irShift` variable removed; donut and labels position via `dcSvgLeft` / `nameLeft`
- When phase tile hidden, `inv_right` expands left and the donut/gauge/labels shift into the freed space
- Donut SVG left = 130px from box edge when expanded (vs 37px when not)
- Name/status start at `dcSvgLeft + 130` when expanded

### Summary Tiles — spread wider
- `SL.invt.xs` changed from `[687, 823, 959]` to `[620, 770, 920]`

### Inverter Card CSS Refactor — vertical centre alignment
- Donut SVG: `top:33px` (was 18) — vertically centred in 136px inv_right box
- Name: `top:12px` (was 6) — `line-height:1` for consistent baseline
- Temps: `top:58px` (was 28) — centre-aligned with donut centre line (67.5px)
- Error: `top:63px` (was 46), `left:78px` (was 76) — `line-height:1`, aligned with donut centre
- All text spans now use `line-height:1` to eliminate cross-element baseline drift

### INV LOAD Reposition — far left vertical stack
- Donut SVG reduced from 115px to 70px, moved to top-left of inv_right box
- Inverter name, Radiator Temp, and error status stacked vertically below the donut (was: horizontal row to the right)
- **Current layout**: name above donut, temps (Temp / Rad Temp) left of donut, error right of donut
- Text container: `flex-direction:column;align-items:center` at same x as donut, `overflow:hidden` with `text-overflow:ellipsis` to prevent bleed under TOTAL IMP
- Expanded (phase hidden): `dcSvgLeft=92`; not expanded: `dcSvgLeft=64` (auto-calculated)
- `overflow:hidden` added to inv_right box to contain all content
- invt xs further pushed to `[650, 800, 950]` for margin from status text
- Temperature label: `Temp:` (inverter temp) and `Rad Temp:` (radiator temp via `inv_rad_temp`) — stacked vertically on the left

### Layout — Right-side control panel
- **SL.r_mode**: `[1275, 25, 208, 130]` (height increased to fix INV STATE clipping)
- **SL.r_stats (battFlip)**: `[1275, 162, 208, 237]` (shifted +26px to follow MODE)
- **SL.r_cyl**: `[1113, 123, 152, 288]`
- **SL.r_pvtile**: `[1113, 410, 369, 50]` (shifted down to avoid overlap)
- **SL.r_ev**: `[1113, 462, 369, 50]`
- **SL.r_cons**: `[1113, 513, 369, 123]`
- **SL.r_prod**: `[1113, 638, 369, 123]`
- **SL.r_events**: `[1113, 763, 369, 141]`

### MODE Card (r_mode)
- `overflow:hidden` on the box
- INV STATE row: `position:absolute;top:74px;bottom:8px;overflow-y:auto` — scrollable area
- Value span: `line-height:1.35; overflow-wrap:anywhere; min-width:0` — wraps to multiple lines
- Flex container: `align-items:flex-start` — label stays top-anchored when value wraps
- Label span: `flex-shrink:0` — prevents label from collapsing

### battFlip stats rows
- Each stat row: `align-items:flex-start` (was `baseline`)
- Value span: `line-height:1.3; overflow-wrap:anywhere; min-width:0` (was `white-space:nowrap; text-overflow:ellipsis`)
- Label span: `flex-shrink:0`

### pvPwrTile cells
- Each PV string cell: `overflow:hidden; text-overflow:ellipsis; white-space:nowrap`

### Popup Views
- **Battery popup** (`_viewBattery`, line ~3100): Added `bat_soh` (SOH), `bat_index` (Index), `bat_bms_version` (BMS Ver) tiles to Pack grid
- **System popup** (`_viewSystem`, line ~3230): Replaced "Grid kWh" tile with "Running hrs" using `sys_running_hours` entity
- **Smart Plugs popup** (`_wPlugTile`, line ~2483): Added voltage display (`#ffb45a` orange) after power watts via `plug_N_voltage` entity
- **Security popup** (`_viewSecurity`, line ~2962): Added `sec_cam3` and `sec_cam4`; camera grid is `display:grid; grid-template-columns:repeat(2,1fr)` (2 per row)

### Sky Backgrounds
- Default `background_path`: `https://raw.githubusercontent.com/realzeepro/zee-home-card/main/sky` (GitHub raw CDN)
- HACS plugin type does NOT download the `sky/` folder — only the single JS file
- 14 PNG images (1.9–2.6 MB each) in `sky/` directory
- Fallback path for local: `/local/community/zee-home-card/sky`

### New Config Keys Added
- `bat_soh`, `bat_index`, `bat_bms_version` — battery popup
- `sys_running_hours` — system popup (replaces `sys_grid_meter` in inverter view; `sys_grid_meter` still kept in defaults)
- `plug_1_voltage` through `plug_6_voltage` — smart plugs
- `sec_cam3`, `sec_cam4` — security popup

## Editor Sections (line ~5200)
Each nav view has its own section in the visual editor. Entity pickers use `picker(key, label)` helper. Sections: General, Toggles, Weather, Solar, Grid, Phase/Inverter, Battery, Inverter, Energy Today, EV, Bottom Tiles, Thresholds, Appearance, Text Sizes, Cameras, Energy View, Smart Plugs View, Battery View, Climate View, Security View, Automation View, Lighting View, Recent Events, System View, Testing.

## Full Feature Catalog

### Main Dashboard
- Animated energy flow lines (battery↔home, grid↔home) with live power/voltage labels moving along SVG paths
- Sun/moon arc across the sky + star field + 14 weather-reactive sky backgrounds with rain/snow/fog particle effects
- Battery cylinder visualization with SOC color gradient + tap-flip tile (stats front ↔ 6 PV strings back)
- 3-phase grid ↔ inverter flip tile (tap to toggle)
- EV charging banner (power W, current A, SOC %, ETA)
- PV power/voltage tile, production/consumption history bar charts
- 6 configurable bottom tiles with animated room-card popups (tap to expand)
- Recent events feed (scrollable)
- Header status icons: WiFi signal, power menu (restart/reboot/shutdown), Bluetooth, camera snapshot trigger

### 9 Nav Views (tap left rail to open)

1. **Energy** — Live power grid diagram + inverter controls: EMS mode selector, export limit slider, backup reserve, DOD, SoC protection, grid switch, sync toggles
2. **Smart Plugs** — Up to 6 plug toggle tiles with name, power (W), voltage (V) — orange voltage readout
3. **Battery** — Pack metrics grid: SoC, SOH, Index, BMS Ver, cell min/max voltages, temperatures, cycle count + charge/discharge/force-charge toggles + SoC limit slider
4. **Climate** — AC card: target temp steppers, mode/fan/swing chip selectors, eco toggle + fridge temp + ambient temp/humidity sensors + 6 extra entity slots
5. **Security** — 4 camera streams (2×2 grid) + safety sensors (smoke, gas, water, motion) + door/window sensors + alarm scene buttons + motion alert toggle
6. **Automation** — Scene buttons + 4 relay toggles + 6 extra entity toggles + automations group (Alexa routines, Tuya timers, etc.)
7. **Lighting** — Individual light cards with brightness sliders + all-on/all-off + adaptive lighting toggle
8. **System** — Inverter section: temp, board temp, mode, running hours + Server section: CPU/Memory/Disk/Uptime row, Core temps row, Network stats row (ETH0/WLAN0 RX/TX auto-scaled)
9. **Dashboard** — Returns to main dashboard view

### Other Capabilities
- 15 languages (auto-detected from HA `language` setting, overridable via `language` config key)
- Visual editor with 24 config sections (General through Testing)
- Demo mode (mock entities for layout testing without live hardware)
- Calendar popup (tap clock in header) with event dots
- Camera fullscreen viewer (tap camera to expand)
- Collapsible panels, pause-on-idle, edge dim overlay
- Shadow DOM isolation — no theme CSS leakage
- Auto-scales to any screen width (viewBox 1500×1000, SVG viewport)
- Auto-discovery per view for climate/security/lighting/automation entities (when `auto_discover_<view>: true`)

## CSS Classes for Popup Widgets
- `.pw` — control row (flex, toggle, slider)
- `.pw-grid` — N-column grid of metric tiles
- `.pw-mtile` — metric tile (icon + label + value stack)
- `.pw-ttile` — toggle tile (icon + label + switch)
- `.pw-cams` — camera grid container
- `.pw-cam` — individual camera cell
- `.pw-cam .camStream` — camera `<img>` element
- `.pw-tgl` — toggle switch
- `.pw-head` — section header
- `.pw-btn` — action button
- `.pw-slider` — range slider row

## Helper Methods (line ~2440)
- `_wHead(label)` — section header
- `_wGrid(cols, html)` — N-column grid wrapper
- `_wTile(icon, label, entityId, unit, isPower)` — metric tile
- `_wComputedTile(icon, label, watts, unit)` — derived-power tile
- `_wToggleTile(icon, label, entityId)` — toggle tile
- `_wPlugTile(label, switchId, powerId, voltageId)` — smart plug tile
- `_wCameras(cams)` — camera tile row
- `_wSlider(icon, label, entityId, min, max, step, unit)` — slider
- `_wToggle(icon, label, entityId)` — toggle row
- `_wButton(icon, label, entityId)` — action button
- `_wScenes(scenes)` — scene buttons

## File Structure
```
zee-home-card.js   — main card (IIFE, shadow DOM, ~5700 lines)
sky/               — 14 weather background PNGs (~30MB total)
hacs.json          — HACS metadata
README.md          — docs
AGENTS.md          — this file (project knowledge for AI)
```
