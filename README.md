# k-flow-card

**Khan Automation — Home Assistant Custom Energy Flow Card**
`k-flow-card.js` · Unified Edition **v7.4.0**

---

## Overview

`k-flow-card` is a fully custom Home Assistant Lovelace card that renders a live, animated energy-flow diagram for a GoodWe solar inverter system with JK BMS battery storage. It is self-contained in a single JavaScript file — no dependencies, no HACS card required beyond loading the resource.

The card combines an SVG energy-flow canvas (sun arc, animated flow paths, inverter, battery, grid, home, EV nodes) with an HTML stat panel showing real-time battery telemetry. Everything updates live via the standard `hass` setter.

---

## Features

- Animated S-curve energy flow paths (solar → inverter → battery / grid / home / EV)
- Live sun position tracking along an arc based on `sun.sun` elevation
- Battery SOC colour coding with dynamic charge/discharge indicators
- Dual battery support (primary + secondary, stacked layout)
- EV / car charger node with live state, SOC, and ETA
- Extra PV strings (PV3 / PV4) with combined toggle
- System limits panel (battery Ah/Wh, inverter max, PV max)
- Battery stat tiles: Cell Temp, BMS Temp, Min/Max Cell Voltage, Batt Discharge, Total PV Gen
- Endurance tile: charge/discharge time remaining or ETA timestamp
- Full visual editor with section toggles — no YAML required for setup
- Per-row custom label and entity overrides for all 6 stat tiles
- Fully dark-themed, mobile-friendly

---

## Installation

1. Copy `k-flow-card.js` to your HA config folder:
   ```
   /config/www/k-flow-card.js
   ```

2. Add as a Lovelace resource:
   ```yaml
   url: /local/k-flow-card.js
   type: module
   ```
   *(Settings → Dashboards → Resources → Add)*

3. Add to a dashboard view:
   ```yaml
   type: custom:k-flow-card
   ```

4. Open the visual editor to configure entities.

---

## Configuration Reference

All keys are configured through the visual editor. The YAML equivalents are listed below for reference or manual setup.

### Core / Solar

| Key | Default | Description |
|---|---|---|
| `inverter_name` | `''` | Label shown in the inverter node |
| `pv1_power` | `sensor.goodwe_pv1_power` | PV string 1 power (W) |
| `pv2_power` | `sensor.goodwe_pv2_power` | PV string 2 power (W) |
| `pv3_power` | `''` | PV string 3 — optional, enable via Extra PV toggle |
| `pv4_power` | `''` | PV string 4 — optional, enable via Extra PV toggle |
| `pv_total_power` | `sensor.goodwe_pv_power` | Total PV power (W) |
| `pv_max_power` | `7500` | Max PV power for bar scaling (W) |
| `today_pv` | `sensor.goodwe_today_s_pv_generation` | Today's PV generation (kWh) |
| `total_pv_gen_entity` | `sensor.goodwe_total_pv_generation` | Lifetime PV generation (kWh) |
| `inv_temp` | `sensor.goodwe_inverter_temperature_module` | Inverter temperature |
| `today_batt_chg` | `sensor.goodwe_today_battery_charge` | Today battery charge (kWh) |
| `today_load` | `sensor.goodwe_today_load` | Today load (kWh) |
| `sun` | `sun.sun` | Sun entity for arc position |

### Grid

| Key | Default | Description |
|---|---|---|
| `grid_active_power` | `sensor.goodwe_active_power` | Grid active power (W) |
| `grid_import_energy` | `sensor.goodwe_today_energy_import` | Today grid import (kWh) |
| `grid_export_energy` | `''` | Today grid export (kWh) — optional |
| `grid_power_alt` | `sensor.grid_phase_a_power` | Alternate grid power sensor |
| `invert_grid_power` | `false` | Invert sign — enable if positive = exporting |
| `consump` | `sensor.goodwe_house_consumption` | House consumption (W) |

### Primary Battery

| Key | Default | Description |
|---|---|---|
| `_show_battery` | `true` | Show primary battery section |
| `battery_soc` | `sensor.jk_soc` | Battery state of charge (%) |
| `battery_power` | `sensor.jk_power` | Battery power (W) |
| `battery_current` | `sensor.jk_current` | Battery current (A) |
| `battery_voltage` | `sensor.jk_voltage` | Battery voltage (V) |
| `battery_temp1` | `sensor.jk_temp1` | Cell temp probe 1 |
| `battery_temp2` | `sensor.jk_temp2` | Cell temp probe 2 |
| `battery_mos` | `sensor.jk_mos` | BMS MOS temperature |
| `battery_min_cell` | `sensor.jk_cellmin` | Min cell voltage |
| `battery_max_cell` | `sensor.jk_cellmax` | Max cell voltage |
| `batt_dis` | `sensor.goodwe_today_battery_discharge` | Today discharge (kWh) |
| `battery_full_ah` | `314` | Battery capacity (Ah) |
| `battery_full_wh` | `16076` | Battery capacity (Wh) |
| `goodwe_battery_soc` | `sensor.goodwe_battery_state_of_charge` | Fallback SOC |
| `goodwe_battery_curr` | `sensor.goodwe_battery_current` | Fallback current |
| `invert_battery_power` | `false` | Invert sign — enable if positive = discharging |

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
| `charger_eta` | `''` | Charge ETA in minutes — optional |
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
| `label_cell_temp_minmax` | `CELL TEMP MIN/MAX` | Tile label — cell temp |
| `label_bms_temp` | `BMS TEMP` | Tile label — BMS temp |
| `label_min_cell` | `Min Cell` | Tile label — min cell voltage |
| `label_max_cell` | `Max Cell` | Tile label — max cell voltage |
| `label_batt_dis` | `Batt Dis.` | Tile label — battery discharge |
| `label_total_pv_gen` | `TOTAL PV GEN.` | Tile label — total PV generation |
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
| General | — | Inverter name |
| Labels | `+ Enable` chip | Rename stat tiles; per-row entity overrides |
| Solar | — | PV1, PV2 entities |
| Extra PV Strings | `+ Enable` chip | PV3, PV4 |
| Solar Extras | — | Totals, temperatures, today stats |
| Grid | — | Grid power, import/export, consumption |
| Primary Battery | `+ Enable` chip | Full BMS telemetry |
| Secondary Battery | `+ Enable` chip | Second pack |
| System Limits | `+ Enable` chip | Capacity and power limits |
| EV / Car Charger | `+ Enable` chip | Charger state, SOC, ETA |

---

## Colour Logic

| Metric | Thresholds |
|---|---|
| **SOC** | ≤25% red · ≤50% orange · ≤75% blue · >75% green |
| **Cell Temp** | ≤15°C blue · ≤35°C green · ≤45°C orange · >45°C red |
| **Cell Voltage** | <3.0V red · <3.1V orange · <3.4V yellow · ≤3.65V green · >3.65V red |
| **Inverter / Env Temp** | ≤25°C green · ≤45°C orange · >45°C red |

---

## Changelog

### v7.4.0
- **Labels section — header chip toggle** (same style as Secondary Battery, Extra PV, EV, Limits sections). Body is hidden when disabled.
- **Per-row auto-enable logic:** each label row's entity picker activates independently — only when that row's text has been changed from its default value. No global unlock needed.
- **Per-row Battery/Solar locking:** corresponding pickers in Battery and Solar Extras sections lock individually when their label row is active, not all-at-once.
- `_updateDynamic` refactored: dead `_readEntity` helper removed; replaced with clean `_rowActive`, `_readNum`, `_readStr` helpers.
- `_set` now triggers a re-render on any of the 6 label text key changes (live editor feedback).

### v7.3.0
- **Labels editor** reduced to exactly 6 rows matching the 2×3 stat tile grid.
- `label_endurance` text field removed from editor (endurance tile exists on card but label is fixed).
- `label_endu_eta` / `label_entity_endu_eta` row removed from editor.
- **Battery voltage** (`battery_voltage`, `battery2_voltage`) is never locked — always freely editable regardless of Labels state.
- **Cell Temp override:** when a custom entity is selected, tile shows a single value only (no `temp1 / temp2` pair).
- **Endurance tile** layout: full card width, vertically compact row, all content bottom-aligned (`align-items:flex-end`).
- Dead stub config keys purged: `label_endu_eta`, `label_entity_endu_eta`, `label_entity_cell_temp2`, `label_entity_endurance`.

### v7.2.1
- **Endurance tile** redesigned from narrow column to full-width single row (horizontally full, vertically compact).
- **Cell Temp Min/Max** entity override: added second entity picker (`label_entity_cell_temp2`) to allow independent min/max overrides. *(Superseded in v7.3.0)*

### v7.2.0
- **Labels section** introduced with `switchRow` global toggle for custom entities.
- Six stat tiles made label-customisable: Cell Temp Min/Max, BMS Temp, Min Cell, Max Cell, Batt Dis, Total PV Gen.
- `pickerMaybeDisabled` helper added — renders override veil on Battery section pickers when Labels global toggle is ON.
- Battery voltage pickers exempted from locking.
- Info banner added above label rows.

### v7.1.x
- **Dual battery** support: Secondary Battery section with independent SOC, power, current, voltage, BMS temp.
- Battery current and power values moved outside the battery icon SVG: power displayed above flow bar, current below.
- Dual battery values stacked in stat tiles where applicable (e.g. `mos1 / mos2`).

### v7.0.x
- **EV / Car Charger** node added to SVG canvas with animated flow path, SOC arc, and ETA display.
- `_show_ev` chip toggle added to editor.
- Charger state machine: `charging`, `completed`, `finished`, `disconnected` with colour and icon changes.

### v6.x
- **Extra PV Strings** (PV3 + PV4) merged under one `_show_pv_extra` chip toggle (previously two separate toggles).
- **System Limits** section added with numeric fields: battery Ah, battery Wh, inverter max, PV max.
- `_show_limits` chip toggle added.
- `inverter_name` moved to General section as `ha-textfield` input.
- `charger_battery_capacity_wh` and other numeric fields migrated to explicit `ha-textfield` / `numberField` inputs.

### v5.x
- Sun arc introduced: sun SVG node tracks real elevation angle from `sun.sun`, stays pinned on the arc path.
- Animated S-curve flow paths replacing straight lines.
- Cross / T-shape layout established: sun arc at top → inverter centre → Battery left, Grid right, Home below.
- Static SVG rebuilt via `_buildStaticSVG()` on `setConfig`; dynamic updates isolated to `_updateDynamic()`.

### v4.x and earlier
- Initial card structure: basic SVG energy flow with GoodWe + JK BMS entity mapping.
- Visual editor scaffolded with `makeSection`, `picker`, `textField`, `switchRow` helpers.
- `ha-selector` entity pickers introduced.
- SOC colour logic, cell temp/voltage colour helpers defined.
- Endurance calculation introduced (hours remaining based on current draw / charge rate).

---

## File Structure

```
k-flow-card.js
│
├── class KFlowCardEditor   (visual editor — HTMLElement, shadow DOM)
│   ├── _render()           builds editor sections
│   ├── makeSection()       collapsible section with optional chip toggle
│   ├── picker()            ha-selector entity picker
│   ├── textField()         ha-textfield input
│   ├── numberField()       ha-textfield numeric input
│   ├── switchRow()         pill toggle (used for invert flags)
│   ├── labelRow()          text field + conditionally-enabled entity picker
│   └── pickerMaybeDisabled() picker with override veil overlay
│
└── class KFlowCard         (main card — HTMLElement, shadow DOM)
    ├── setConfig()         merges config with stub defaults, triggers static build
    ├── _buildStaticSVG()   renders full SVG + HTML stat panel (called once per config)
    ├── _updateDynamic()    updates all live values, colours, animations (called on every hass update)
    ├── _val()              safe numeric entity reader
    ├── _strVal()           safe string entity reader
    ├── _socColor()         SOC → hex colour
    ├── _cellTempColor()    temperature → hex colour
    ├── _cellVoltColor()    cell voltage → hex colour
    ├── _tempColor()        general temperature → hex colour
    ├── _remCapColor()      remaining capacity → hex colour
    └── _fmtTill()          hours → "Till HH:MM" or "in Xh Ym" string
```

---

## Notes

- Tested on Home Assistant OS (HAOS) with GoodWe ET/ES inverter integration and JK BMS Bluetooth integration.
- The card uses shadow DOM — custom CSS from themes does not penetrate the card. All colours are hardcoded or driven by entity values.
- No external libraries or HACS dependencies. Load as a single `type: module` resource.
- Config keys prefixed with `_` (e.g. `_show_battery`) are editor-only boolean toggles — they control visibility but are stored in the card config YAML.

---

*Khan Automation · k-flow-card · Last updated: v7.4.0*
