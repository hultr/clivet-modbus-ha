# Clivet Modbus for Home Assistant

Modbus sensor, template, and dashboard configuration for Clivet Sphera EVO 2.0 heat pumps (and other Midea-based units with similar register maps).

This repository gives you:

- A split Modbus config (`clivet_modbus/sensors/*.yaml`) covering control, ODU, IDU, and technician registers.
- Template entities (`clivet_modbus/templates/*.yaml`) for bitmask decoding, combined values, and derived metrics.
- A ready Lovelace panel (`clivet_modbus/panel.yaml`) with image overlays for key runtime states.
- A detailed register reference (`clivet_modbus/registers.md`).

## What This Is (and What It Is Not)

This is configuration content for Home Assistant YAML mode. It is not a custom integration, not a HACS package, and not an add-on.

You "install" it by copying files into your HA config directory and adding `!include` entries to your main `configuration.yaml`.

## Repository Structure

```text
clivet_modbus/
	modbus.yaml                 # Modbus TCP hub and sensor includes
	sensors/                    # Raw register sensors grouped by topic
	templates/                  # Decoded bitmasks, switches, derived sensors
	panel.yaml                  # Lovelace picture-elements panel
	panel_pictures/             # PNG assets used by panel.yaml
	registers.md                # Register documentation and notes
from-configurations.yml       # Legacy single-file export (reference only)
```

Notes:

- `from-configurations.yml` is legacy/reference material and is not used by the split config.
- Technician registers are in `clivet_modbus/sensors/_08_tech.yaml` and can be disabled by renaming/removing that file.

## Prerequisites

- Home Assistant with YAML configuration enabled.
- Modbus TCP reachable heat pump.
- Heat pump IP address.
- Access to `secrets.yaml` and `configuration.yaml`.

## Install

1. Copy this repository content into your Home Assistant config directory (typically `/config`).
2. Add your heat pump host to `secrets.yaml`:

```yaml
clivet_host: 192.168.20.113
```

3. In `configuration.yaml`, add:

```yaml
modbus: !include clivet_modbus/modbus.yaml
template: !include_dir_merge_list clivet_modbus/templates/
```

4. Ensure `clivet_modbus/modbus.yaml` hub name remains `clivet_hp` (template switches write to that hub).
5. Restart Home Assistant.

Optional:

- To disable password-protected technician registers (200-272), remove or rename `clivet_modbus/sensors/_08_tech.yaml`.

## Lovelace Panel Setup

`clivet_modbus/panel.yaml` expects image files under Home Assistant's `www/heat_pump_viz/` path (served as `/local/heat_pump_viz/...`).

1. Create directory:

```bash
mkdir -p /config/www/heat_pump_viz
```

2. Copy PNG assets from this repo:

```bash
cp clivet_modbus/panel_pictures/* /config/www/heat_pump_viz/
```

3. Add the panel view to your dashboard (Raw configuration editor), or include `clivet_modbus/panel.yaml` content directly in your Lovelace YAML.

## How to Use It

After restart, check these first:

- `sensor.hp_compressor_operating_frequency`
- `sensor.hp_hydronic_supply_temperature`
- `sensor.hp_hydronic_return_temperature`
- `binary_sensor.hp_compressor_running`
- `binary_sensor.hp_fan_running`

Control registers are exposed as regular sensors and template switches:

- Register 0 switches in `templates/06_switches_reg0.yaml`
- Register 5 switches in `templates/07_switches_reg5.yaml`

Use those switches for safe bit-level control instead of writing raw bitmasks manually.

Derived values are in `templates/08_combined_sensors.yaml`, such as:

- `sensor.hp_heat_delivered`
- `sensor.hp_odu_power_consumption`
- `sensor.hp_calculated_cop`

## Important Safety Notes

- Registers `200-272` are installer/technician parameters. Read with care; writing wrong values can cause malfunction.
- Register scales for some values (for example flow/capacity) are inferred from documentation and noted as guesswork in comments.
- Validate key measurements against your unit display before basing automations on them.

## Known Compatibility Notes

- This config is built around a Clivet Sphera EVO 2.0 map.
- Many Midea-derived units should be close, but register behavior can differ by firmware/model.
- If values look wrong, start by comparing `clivet_modbus/registers.md` against your own service manual.

## Troubleshooting

- `unavailable` entities:
	- Check heat pump IP in `secrets.yaml`.
	- Check TCP/502 reachability from HA host.
	- Confirm slave/unit ID is `1` for your device.
- Panel overlays not visible:
	- Confirm images exist under `/config/www/heat_pump_viz/`.
	- Confirm paths are reachable as `/local/heat_pump_viz/...`.
- Missing template entities:
	- Confirm `template: !include_dir_merge_list clivet_modbus/templates/` is present.

## Consistency Fixes Included

This repository has been cleaned up for two concrete inconsistencies:

- `clivet_modbus/modbus.yaml` now references the actual technician file name (`_08_tech.yaml`).
- `clivet_modbus/panel.yaml` now points to the existing entity `sensor.hp_curve1_t1s_output`.

