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

## Recent Changes (commits on main)

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
