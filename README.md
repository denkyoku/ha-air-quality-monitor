# 🌬️ Multi-Room Awair Air Quality Monitor

A robust, multi-room Home Assistant automation that monitors air quality scores across the **Bedroom**, **Living Room**, and **Office** using Awair monitors. Features stateful hysteresis to prevent alert spam and ambient shelf lighting cues that illuminate red during poor air quality and automatically turn off upon recovery.

---

## ✨ Features

- **Multi-Room Monitoring**: Independently tracks Awair air quality scores across three distinct rooms:
  - **Bedroom**: `sensor.bedroom_awair_score`
  - **Living Room**: `sensor.awair_awair_score`
  - **Office**: `sensor.office_awair_score`
- **Strict Stateful Hysteresis**:
  - **Degraded Alert Threshold (< 50)**: Triggers an immediate notification and flags the room's alert state as active.
  - **Recovery Threshold (> 70)**: Only alerts when air quality recovers back above 70 **after** having dropped below 50. Normal baseline fluctuations between 65 and 75 will never trigger spurious notifications.
- **Ambient Lighting Cues**:
  - **Living Room Shelf** (`light.shelf`): Automatically turns solid **RED** (`rgb_color: [255, 0, 0]`, 100% brightness) when living room air quality drops below 50, and turns off when recovered above 70.
  - **Office Shelf** (`light.stalagmite`): Automatically turns solid **RED** (`rgb_color: [255, 0, 0]`, 100% brightness) when office air quality drops below 50, and turns off when recovered above 70.
- **Persistent State Tracking**: Employs dedicated Home Assistant `input_boolean` helpers that survive restarts and reboots.
- **Queued Execution**: Configured with `mode: queued` (max: 10) to reliably handle simultaneous or closely-spaced sensor score events across rooms without dropped actions.

---

## 🛠️ Hardware & Entity Mapping

| Room | Awair Sensor Entity | Alert State Helper | Shelf Light Entity | Degraded Action (<50) | Recovery Action (>70) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Bedroom** | `sensor.bedroom_awair_score` | `input_boolean.bedroom_air_quality_alert` | *None* | Turn helper ON + Send notification | If helper ON: Turn helper OFF + Send notification |
| **Living Room** | `sensor.awair_awair_score` | `input_boolean.living_room_air_quality_alert` | `light.shelf` (*Living Room Shelf*) | Turn helper ON + Turn shelf RED + Send notification | If helper ON: Turn helper OFF + Turn shelf OFF + Send notification |
| **Office** | `sensor.office_awair_score` | `input_boolean.office_air_quality_alert` | `light.stalagmite` (*Office Shelf*) | Turn helper ON + Turn shelf RED + Send notification | If helper ON: Turn helper OFF + Turn shelf OFF + Send notification |

---

## ⚙️ Helper Setup

To support stateful hysteresis, create the following 3 `input_boolean` helpers in Home Assistant under **Settings > Devices & Services > Helpers > Create Helper > Toggle**:

1. **Name**: `Bedroom Air Quality Alert` (`input_boolean.bedroom_air_quality_alert`)
2. **Name**: `Living Room Air Quality Alert` (`input_boolean.living_room_air_quality_alert`)
3. **Name**: `Office Air Quality Alert` (`input_boolean.office_air_quality_alert`)

*(Icon recommendation: `mdi:air-filter`)*

---

## 🚀 Installation & Usage

1. In Home Assistant, navigate to **Settings > Automations & Scenes > Create Automation > Edit in YAML** (or edit your `automations.yaml`).
2. Paste the contents of [`automation.yaml`](automation.yaml).
3. Ensure your Awair sensor entity IDs and shelf light entity IDs match your setup.
4. Save and reload automations.

---

## 📜 Version History

### `v1.0.0`
- Initial multi-room production release.
- Added support for Bedroom, Living Room, and Office Awair monitors.
- Implemented stateful hysteresis logic via `input_boolean` helpers.
- Added visual red shelf lighting alerts for Living Room and Office with automatic turn-off upon recovery.
