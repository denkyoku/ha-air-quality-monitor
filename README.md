# 🌬️ Multi-Room Awair Air Quality Monitor

A robust, multi-room Home Assistant automation that monitors air quality scores across the **Bedroom**, **Living Room**, and **Office** using Awair monitors. Features stateful hysteresis to prevent alert spam, ambient shelf lighting cues, and automatic fan circulation when air quality drops into severe territory (< 40).

---

## ✨ Features

- **Multi-Room Monitoring**: Independently tracks Awair air quality scores across three distinct rooms:
  - **Bedroom**: `sensor.bedroom_awair_score`
  - **Living Room**: `sensor.awair_awair_score`
  - **Office**: `sensor.office_awair_score`
- **Multi-Stage Thresholds & Stateful Hysteresis**:
  - **Degraded Alert Threshold (< 50)**: Triggers an immediate notification and flags the room's alert state as active.
  - **Severe Degradation Threshold (< 40)**: Automatically turns on the room's ceiling or circulation fan to assist air circulation and air purification.
  - **Recovery Threshold (> 70)**: Only alerts and resets devices when air quality recovers back above 70 **after** having dropped below 50. Normal baseline fluctuations between 65 and 75 will never trigger spurious notifications.
- **Ambient Lighting Cues**:
  - **Living Room Shelf** (`light.shelf`): Automatically turns solid **RED** (`rgb_color: [255, 0, 0]`, 100% brightness) when living room air quality drops below 50, and turns off when recovered above 70.
  - **Office Shelf** (`light.stalagmite`): Automatically turns solid **RED** (`rgb_color: [255, 0, 0]`, 100% brightness) when office air quality drops below 50, and turns off when recovered above 70.
- **Smart Fan Circulation & Recovery Shutoff**:
  - **Bedroom Fan** (`fan.bedroom_fan_and_light`): Turns on when Bedroom air quality drops below 40. Turns back off upon recovery above 70.
  - **Dining Room Fan** (`fan.dining_room_fan`): Serves the open living/dining space; turns on when Living Room air quality drops below 40. Turns back off upon recovery above 70.
  - **Office Fan** (`fan.office_office_ceiling_fan_with_lights`): Turns on when Office air quality drops below 40. Turns back off upon recovery above 70.
  - **Smart State Guard**: Dedicated fan-boost helpers ensure fans are only turned off upon recovery if they were activated by the air quality drop, preserving manual fan usage if air quality never hit the <40 threshold.
- **Persistent State Tracking**: Employs dedicated Home Assistant `input_boolean` helpers that survive restarts and reboots.
- **Queued Execution**: Configured with `mode: queued` (max: 10) to reliably handle simultaneous or closely-spaced sensor score events across rooms without dropped actions.

---

## 🛠️ Hardware & Entity Mapping

| Room | Awair Sensor Entity | Alert Helper (<50) | Fan Boost Helper (<40) | Shelf Light Entity | Assigned Fan Entity |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Bedroom** | `sensor.bedroom_awair_score` | `input_boolean.bedroom_air_quality_alert` | `input_boolean.bedroom_air_quality_fan_boost` | *None* | `fan.bedroom_fan_and_light` |
| **Living Room** | `sensor.awair_awair_score` | `input_boolean.living_room_air_quality_alert` | `input_boolean.living_room_air_quality_fan_boost` | `light.shelf` (*Living Room Shelf*) | `fan.dining_room_fan` (*Dining Room Fan*) |
| **Office** | `sensor.office_awair_score` | `input_boolean.office_air_quality_alert` | `input_boolean.office_air_quality_fan_boost` | `light.stalagmite` (*Office Shelf*) | `fan.office_office_ceiling_fan_with_lights` (*Office Fan*) |

---

## ⚙️ Helper Setup

To support stateful hysteresis and fan-boost tracking, create the following 6 `input_boolean` helpers in Home Assistant under **Settings > Devices & Services > Helpers > Create Helper > Toggle**:

### Air Quality Alert Helpers
1. **Name**: `Bedroom Air Quality Alert` (`input_boolean.bedroom_air_quality_alert`)
2. **Name**: `Living Room Air Quality Alert` (`input_boolean.living_room_air_quality_alert`)
3. **Name**: `Office Air Quality Alert` (`input_boolean.office_air_quality_alert`)

### Fan Boost Helpers
4. **Name**: `Bedroom Air Quality Fan Boost` (`input_boolean.bedroom_air_quality_fan_boost`)
5. **Name**: `Living Room Air Quality Fan Boost` (`input_boolean.living_room_air_quality_fan_boost`)
6. **Name**: `Office Air Quality Fan Boost` (`input_boolean.office_air_quality_fan_boost`)

*(Icon recommendations: `mdi:air-filter` for alert helpers, `mdi:fan-alert` for fan boost helpers)*

---

## 🚀 Installation & Usage

1. In Home Assistant, navigate to **Settings > Automations & Scenes > Create Automation > Edit in YAML** (or edit your `automations.yaml`).
2. Paste the contents of [`automation.yaml`](automation.yaml).
3. Ensure your Awair sensor entity IDs, shelf light entity IDs, and fan entity IDs match your setup.
4. Save and reload automations.

---

## 📜 Version History

### `v1.1.0`
- Added severe degradation threshold (`< 40`) triggering fan activation:
  - Bedroom: turns on `fan.bedroom_fan_and_light`.
  - Living Room: turns on `fan.dining_room_fan`.
  - Office: turns on `fan.office_office_ceiling_fan_with_lights`.
- Added automatic fan shutoff upon air quality recovery (`> 70`).
- Introduced companion `input_boolean.*_air_quality_fan_boost` helpers to prevent turning off fans that were not activated by the air quality drop.

### `v1.0.0`
- Initial multi-room production release.
- Added support for Bedroom, Living Room, and Office Awair monitors.
- Implemented stateful hysteresis logic via `input_boolean` helpers.
- Added visual red shelf lighting alerts for Living Room and Office with automatic turn-off upon recovery.
