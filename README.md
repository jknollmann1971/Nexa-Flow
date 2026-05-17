# nexa-flow-card

**Khan Automation â€” Home Assistant Custom Energy Flow Card**
`nexa-flow-card.js` Â· Unified Edition **V1.0.0**
<img width="1708" height="2520" alt="IMG_20260513_114249" src="https://github.com/user-attachments/assets/e4c54570-5b9e-43c4-9326-b44d4105a5d1" />
<img width="1108" height="1688" alt="IMG_20260516_011350" src="https://github.com/user-attachments/assets/6914d4ab-daa6-432a-983d-6abac15a1cac" />
<img width="1114" height="1744" alt="IMG_20260516_011300" src="https://github.com/user-attachments/assets/ce97703f-ed71-4ab9-a79d-8d7cf3bcd3f6" />

---

## Overview

`nexa-flow-card` is a fully custom Home Assistant Lovelace card that renders a live, animated energy-flow diagram for a Growatt Nexa solar inverter system. It is self-contained in a single JavaScript file â€” no dependencies, no HACS card required beyond loading the resource.

The card combines an SVG energy-flow canvas (sun arc, animated flow paths, inverter, battery, grid, home, EV nodes) with an HTML stat panel showing real-time battery telemetry. Everything updates live via the standard `hass` setter.

---

## Features

- Animated S-curve energy flow paths (solar â†’ inverter â†’ battery / grid / home / EV)
- Live sun position tracking along an arc based on `sun.sun` elevation
- Battery SOC colour coding with dynamic charge/discharge indicators
- Dual battery support (primary + secondary, stacked layout)
- EV / car charger node with live state, SOC, and ETA
- Extra PV strings (PV3 / PV4) with combined toggle
- System limits panel (battery Ah/Wh, inverter max, PV max)
- Battery stat tiles: Cell Temp, BMS Temp, Min/Max Cell Voltage, Batt Discharge, Total PV Gen
- Endurance tile: charge/discharge time remaining or ETA timestamp
- Full visual editor with section toggles â€” no YAML required for setup
- Per-row custom label and entity overrides for all 6 stat tiles
- Fully dark-themed, mobile-friendly

---

## Installation

### Method 1 â€” HACS (Recommended)

1. In HACS, go to **Frontend â†’ â‹® â†’ Custom repositories**
2. Paste: `https://github.com/thekhan1122/nexa-flow-card` (without `.git`)
3. Category: **Lovelace**
4. Install the card â€“ the resource is added automatically.
5. Click the **+ Explore & Download Repositories** button.
6. Search for **nexa-flow-card**.
7. Click **Download** the resource is added automatically.
8. **Hard refresh** your browser (`Ctrl + Shift + R` / `Cmd + Shift + R`).
9. Open the visual editor to configure entities.

> HACS handles resource registration automatically. No manual resource entry needed.

---

### Method 2 — Manual

1. Copy `nexa-flow-card.js` and the three icon files (`ev-charger-icon.png`, `grid-icon.png`, and `home-icon.png`) from the `dist` directory to your HA config folder:
   ```
   /config/www/nexa-flow-card.js
   /config/www/ev-charger-icon.png
   /config/www/grid-icon.png
   /config/www/home-icon.png
   ```

2. Register as a Lovelace resource:
   *(Settings → Dashboards → Resources → Add)*
   ```yaml
   url: /local/nexa-flow-card.js
   type: module
   ```

3. Add to a dashboard view:
   ```yaml
   type: custom:nexa-flow-card
   ```

4. Open the visual editor to configure entities.

---

## Configuration Reference

All keys are configured through the visual editor. The YAML equivalents are listed below for reference or manual setup.

### Core / Solar

| Key | Default | Description |
|---|---|---|
| `inverter_name` | `''` | Label shown in the inverter node |
| `nexa_work_mode` | `sensor.growatt_0hvrb0zr23jt00yy_work_mode` | Work Mode Sensor |
| `nexa_island_mode` | `switch.growatt_0hvrb0zr23jt00yy_island_mode_enabled` | Island Mode Switch |
| `pv1_power` | `sensor.Growatt Nexa_pv1_power` | PV string 1 power (W) |
| `pv2_power` | `sensor.Growatt Nexa_pv2_power` | PV string 2 power (W) |
| `pv3_power` | `''` | PV string 3 â€” optional, enable via Extra PV toggle |
| `pv4_power` | `''` | PV string 4 â€” optional, enable via Extra PV toggle |
| `pv_total_power` | `sensor.Growatt Nexa_pv_power` | Total PV power (W) |
| `pv_max_power` | `7500` | Max PV power for bar scaling (W) |
| `today_pv` | `sensor.Growatt Nexa_today_s_pv_generation` | Today's PV generation (kWh) |
| `total_pv_gen_entity` | `sensor.Growatt Nexa_total_pv_generation` | Lifetime PV generation (kWh) |
| `inv_temp` | `sensor.Growatt Nexa_inverter_temperature_module` | Inverter temperature |
| `today_batt_chg` | `sensor.Growatt Nexa_today_battery_charge` | Today battery charge (kWh) |
| `today_load` | `sensor.Growatt Nexa_today_load` | Today load (kWh) |
| `sun` | `sun.sun` | Sun entity for arc position |

### Grid

| Key | Default | Description |
|---|---|---|
| `grid_active_power` | `sensor.Growatt Nexa_active_power` | Grid active power (W) |
| `grid_import_energy` | `sensor.Growatt Nexa_today_energy_import` | Today grid import (kWh) |
| `grid_export_energy` | `''` | Today grid export (kWh) â€” optional |
| `grid_power_alt` | `sensor.grid_phase_a_power` | Alternate grid power sensor |
| `invert_grid_power` | `false` | Invert sign â€” enable if positive = exporting |
| `consump` | `sensor.Growatt Nexa_house_consumption` | House consumption (W) |

### Primary Battery

| Key | Default | Description |
|---|---|---|
| `_show_battery` | `true` | Show primary battery section |
| `battery_soc` | `sensor.growatt_soc` | Battery state of charge (%) |
| `battery_power` | `sensor.growatt_power` | Battery power (W) |
| `battery_current` | `sensor.growatt_current` | Battery current (A) |
| `battery_voltage` | `sensor.growatt_voltage` | Battery voltage (V) |
| `nexa_batt_health` | `sensor.growatt_0hvrb0zr23jt00yy_battery_health` | Battery Health (%) |
| `nexa_batt_cycles` | `sensor.growatt_0hvrb0zr23jt00yy_battery_cycle_count` | Battery Cycles |
| `battery_temp1` | `sensor.growatt_temp1` | Cell temp probe 1 |
| `battery_temp2` | `sensor.growatt_temp2` | Cell temp probe 2 |
| `battery_mos` | `sensor.growatt_mos` | BMS MOS temperature |
| `battery_min_cell` | `sensor.growatt_cellmin` | Min cell voltage |
| `battery_max_cell` | `sensor.growatt_cellmax` | Max cell voltage |
| `batt_dis` | `sensor.Growatt Nexa_today_battery_discharge` | Today discharge (kWh) |
| `battery_full_ah` | `314` | Battery capacity (Ah) |
| `battery_full_wh` | `16076` | Battery capacity (Wh) |
| `Growatt Nexa_battery_soc` | `sensor.Growatt Nexa_battery_state_of_charge` | Fallback SOC |
| `Growatt Nexa_battery_curr` | `sensor.Growatt Nexa_battery_current` | Fallback current |
| `invert_battery_power` | `false` | Invert sign â€” enable if positive = discharging |

### Secondary Battery

| Key | Default | Description |
|---|---|---|
| `_show_battery2` | `false` | Enable secondary battery (chip toggle) |
| `battery2_soc` | `''` | Secondary SOC (%) |
| `battery2_power` | `''` | Secondary power (W) |
| `battery2_current` | `''` | Secondary current (A) |
| `battery2_voltage` | `''` | Secondary voltage (V) |
| `battery2_mos` | `''` | Secondary BMS temperature |

### EV / Car Charger

| Key | Default | Description |
|---|---|---|
| `_show_ev` | `false` | Enable EV section (chip toggle) |
| `charger_state` | `''` | Charger state entity (string: `charging`, `completed`, etc.) |
| `charger_power` | `''` | Charger power (W) |
| `charger_current` | `''` | Charger current (A) |
| `charger_soc` | `''` | Car battery SOC (%) |
| `charger_eta` | `''` | Charge ETA in minutes â€” optional |
| `charger_battery_capacity_wh` | `''` | EV battery capacity (Wh) |

### System Limits

| Key | Default | Description |
|---|---|---|
| `_show_limits` | `false` | Show limits section (chip toggle) |
| `inverter_max_power` | `6000` | Inverter max power for bar scaling (W) |

### Labels

| Key | Default | Description |
|---|---|---|
| `_labels_custom_entities` | `false` | Enable Labels section (chip toggle) |
| `label_cell_temp_minmax` | `CELL TEMP MIN/MAX` | Tile label â€” cell temp |
| `label_bms_temp` | `BMS TEMP` | Tile label â€” BMS temp |
| `label_min_cell` | `Min Cell` | Tile label â€” min cell voltage |
| `label_max_cell` | `Max Cell` | Tile label â€” max cell voltage |
| `label_batt_dis` | `Batt Dis.` | Tile label â€” battery discharge |
| `label_total_pv_gen` | `TOTAL PV GEN.` | Tile label â€” total PV generation |
| `label_entity_cell_temp` | `''` | Override entity for cell temp tile |
| `label_entity_bms_temp` | `''` | Override entity for BMS temp tile |
| `label_entity_min_cell` | `''` | Override entity for min cell tile |
| `label_entity_max_cell` | `''` | Override entity for max cell tile |
| `label_entity_batt_dis` | `''` | Override entity for batt dis tile |

> **Entity override rule:** An entity picker in the Labels section activates only when its corresponding label text has been changed from the default. Once active, the matching picker in the Battery section is locked with an "Overridden by Labels" veil to prevent duplication.

---

## Visual Editor Sections

| Section | Toggle | Description |
|---|---|---|
| General | â€” | Inverter name |
| Labels | `+ Enable` chip | Rename stat tiles; per-row entity overrides |
| Solar | â€” | PV1, PV2 entities |
| Extra PV Strings | `+ Enable` chip | PV3, PV4 |
| Solar Extras | â€” | Totals, temperatures, today stats |
| Grid | â€” | Grid power, import/export, consumption |
| Primary Battery | `+ Enable` chip | Full BMS telemetry |
| Secondary Battery | `+ Enable` chip | Second pack |
| System Limits | `+ Enable` chip | Capacity and power limits |
| EV / Car Charger | `+ Enable` chip | Charger state, SOC, ETA |

---

## Colour Logic

| Metric | Thresholds |
|---|---|
| **SOC** | â‰¤25% red Â· â‰¤50% orange Â· â‰¤75% blue Â· >75% green |
| **Cell Temp** | â‰¤15Â°C blue Â· â‰¤35Â°C green Â· â‰¤45Â°C orange Â· >45Â°C red |
| **Cell Voltage** | <3.0V red Â· <3.1V orange Â· <3.4V yellow Â· â‰¤3.65V green Â· >3.65V red |
| **Inverter / Env Temp** | â‰¤25Â°C green Â· â‰¤45Â°C orange Â· >45Â°C red |

---

## Changelog

### v1.0.0
- Initial release of `nexa-flow-card`
- Fully custom Home Assistant Lovelace card for Growatt Nexa solar inverter systems
- Animated energy-flow diagram (solar, inverter, battery, grid, home, EV)
- Built-in visual editor (no YAML required)
- Supports dual battery telemetry, extra PV strings, system limits, and customizable stat tiles
- Includes dynamic data-reading for Nexa specific sensors (Work Mode, Island Mode, Battery Health, Battery Cycles)

---

## File Structure

```
nexa-flow-card.js
â”‚
â”œâ”€â”€ class NexaFlowCardEditor   (visual editor â€” HTMLElement, shadow DOM)
â”‚   â”œâ”€â”€ _render()           builds editor sections
â”‚   â”œâ”€â”€ makeSection()       collapsible section with optional chip toggle
â”‚   â”œâ”€â”€ picker()            ha-selector entity picker
â”‚   â”œâ”€â”€ textField()         ha-textfield input
â”‚   â”œâ”€â”€ numberField()       ha-textfield numeric input
â”‚   â”œâ”€â”€ switchRow()         pill toggle (used for invert flags)
â”‚   â”œâ”€â”€ labelRow()          text field + conditionally-enabled entity picker
â”‚   â””â”€â”€ pickerMaybeDisabled() picker with override veil overlay
â”‚
â””â”€â”€ class NexaFlowCard         (main card â€” HTMLElement, shadow DOM)
    â”œâ”€â”€ setConfig()         merges config with stub defaults, triggers static build
    â”œâ”€â”€ _buildStaticSVG()   renders full SVG + HTML stat panel (called once per config)
    â”œâ”€â”€ _updateDynamic()    updates all live values, colours, animations (called on every hass update)
    â”œâ”€â”€ _val()              safe numeric entity reader
    â”œâ”€â”€ _strVal()           safe string entity reader
    â”œâ”€â”€ _socColor()         SOC â†’ hex colour
    â”œâ”€â”€ _cellTempColor()    temperature â†’ hex colour
    â”œâ”€â”€ _cellVoltColor()    cell voltage â†’ hex colour
    â”œâ”€â”€ _tempColor()        general temperature â†’ hex colour
    â”œâ”€â”€ _remCapColor()      remaining capacity â†’ hex colour
    â””â”€â”€ _fmtTill()          hours â†’ "Till HH:MM" or "in Xh Ym" string
```

---

## Notes

- Tested on Home Assistant OS (HAOS) with Growatt Nexa inverter integration and JK BMS Bluetooth integration.
- The card uses shadow DOM â€” custom CSS from themes does not penetrate the card. All colours are hardcoded or driven by entity values.
- Config keys prefixed with `_` (e.g. `_show_battery`) are editor-only boolean toggles â€” they control visibility but are stored in the card config YAML.
- When installed via HACS, the resource is registered automatically. When installed manually, register as `type: module`.

---

## Troubleshooting

### Card does not appear / shows "Custom element doesn't exist"

- Confirm the resource is registered: **Settings â†’ Dashboards â†’ Resources**. You should see `/hacsfiles/nexa-flow-card/nexa-flow-card.js` (HACS) or `/local/nexa-flow-card.js` (manual) with type `JavaScript Module`.
- Hard refresh the browser: `Ctrl + Shift + R` (Windows/Linux) or `Cmd + Shift + R` (Mac).
- If using the mobile app, clear app cache or force-close and reopen.
- If installed manually, confirm the file is at `/config/www/nexa-flow-card.js` â€” not inside a subfolder.

---

### Visual editor is blank or fails to load

- Open browser DevTools (`F12`) â†’ Console tab. Look for any red errors referencing `nexa-flow-card`.
- Ensure no other version of `nexa-flow-card.js` is registered as a duplicate resource. Go to Resources and remove any stale entries.
- Try clearing the HA frontend cache: **Developer Tools â†’ Template** â†’ reload page.

---

### Entities show `--` or do not update

- Open **Developer Tools â†’ States** and confirm the entity ID exists and has a valid numeric state (not `unavailable` or `unknown`).
- Entity IDs are case-sensitive. Check for typos in the editor.
- If using Growatt Nexa integration, ensure the inverter is online and the integration is polling correctly.
- The card skips `unavailable` and `unknown` states by design â€” the tile will show `--` until the entity returns a valid value.

---

### Flow animations not showing

- The card requires `sun.sun` to be present for the sun arc. If it is missing, the sun node will not animate but the rest of the card will function normally.
- Flow path animations are driven by power values. A path will only animate when its corresponding power reading is above zero.
- If all flows are static, check that your inverter entities are returning live values and not stale/unavailable states.

---

### Battery section is missing

- The Primary Battery section requires `_show_battery: true`. In the visual editor, click the **+ Enable** chip next to **Primary Battery**.
- Secondary Battery, EV, Extra PV Strings, System Limits, and Labels sections each have their own **+ Enable** chip â€” they are hidden by default.

---

### Endurance tile shows `--` or incorrect time

- The endurance calculation requires `battery_full_ah` (capacity in Ah) and `battery_current` to be set and returning valid values.
- If the battery is neither charging nor discharging (current â‰ˆ 0), the tile will show `--` as no meaningful estimate is possible.
- Ensure `battery_full_ah` in **System Limits** matches your actual battery capacity.

---

### Labels section entity pickers are greyed out

- The **Labels** section must be enabled first via the **+ Enable** chip in the section header.
- Once enabled, each entity picker unlocks **only after you change that row's label text** away from its default. This is by design â€” it prevents accidental entity overrides.
- To unlock the Cell Temp entity picker, for example, change the label text from `CELL TEMP MIN/MAX` to anything else.

---

### After update via HACS, card looks wrong or broken

1. Hard refresh the browser (`Ctrl + Shift + R`).
2. If the issue persists, go to **Settings â†’ Dashboards â†’ Resources**, delete the nexa-flow-card entry, then re-add it (HACS will re-register it on the next HA restart).
3. Restart Home Assistant and hard refresh again.

---

### Reporting a bug

When reporting an issue, include:
- Home Assistant version
- nexa-flow-card version (visible in browser DevTools console on load)
- Browser console errors (screenshot or copy-paste)
- Relevant section of your card YAML config (remove sensitive entity names if needed)

---

*Khan Automation Â· nexa-flow-card Â· Last updated: v1.0.0*

