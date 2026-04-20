# IKEA Dirigera Hub Integration for Home Assistant

[![HACS](https://img.shields.io/badge/HACS-Custom-orange.svg)](https://github.com/hacs/integration)
[![Version](https://img.shields.io/github/v/release/nrbrt/dirigera_platform)](https://github.com/nrbrt/dirigera_platform/releases)


A Home Assistant integration for the IKEA Dirigera hub, built on the [dirigera](https://github.com/Leggin/dirigera) Python library (v1.2.6). Originally forked from [sanjoyg/dirigera_platform](https://github.com/sanjoyg/dirigera_platform) and actively maintained with new features, device support, and bug fixes.

Contributions are welcome — feel free to open [issues](https://github.com/nrbrt/dirigera_platform/issues) or submit pull requests. Device data dumps are especially helpful for adding support for new devices ([how to dump](docs/dump-data.md)).

## Highlights

- **Dynamic Device Discovery** — devices added, removed, renamed, or moved to a different room in the IKEA Home app are automatically reflected in Home Assistant without a restart
- **Split-Device Merging** — newer IKEA devices (GRILLPLATS, TOFSMYGGA, TIMMERFLOTTE, MYGGSPRAY) expose as multiple API devices linked by `relationId`; the integration automatically merges them into single HA entities
- **Real-Time Updates** — full WebSocket event support for instant state changes across all device types
- **Reliable Startup** — automatic retry on connection failure (`ConfigEntryNotReady`) instead of requiring manual reloads

## Supported Devices

| Category | Devices | Notes |
|----------|---------|-------|
| **Lights** | TRÅDFRI, FLOALT, and other IKEA lights | RGBWW with dynamic color mode switching (HS ↔ color temp) |
| **Outlets** | INSPELNING, GRILLPLATS, TOFSMYGGA | Energy monitoring (voltage, current, power, energy); split-device pairs auto-merged |
| **Motion Sensors** | VALLHORN, MYGGSPRAY (E2494) | Handles both `motionSensor` and `occupancySensor` device types |
| **Light Sensors** | MYGGSPRAY (E2494) | Illuminance in lux (Matter raw value conversion) |
| **Open/Close Sensors** | PARASOLL, MYGGBETT | |
| **Environment Sensors** | VINDSTYRKA (PM2.5, VOC), ALPSTUGA (CO2), TIMMERFLOTTE | Split-device pairs auto-merged; `state_class: measurement` for Long Term Statistics |
| **Blinds** | FYRTUR, KADRILJ | |
| **Remotes** | STYRBAR, RODRET, SOMRIG, BILRESA | `remotePressEvent` + `shortcutController` support; automation triggers for all buttons |
| **Air Purifiers** | STARKVIND | Native unit of measurement fix |
| **Water Sensors** | BADRING | |
| **Scenes** | All Dirigera scenes | Exposed as entities with Activate button |

## Requirements

- Home Assistant 2026.1 or newer (v0.2.6 and earlier work with HA 2024.12+)
- IKEA Dirigera hub on your local network
- HACS installed

## Installation

### Via HACS (recommended)

[![Open your Home Assistant instance and open a repository inside the Home Assistant Community Store.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=nrbrt&repository=dirigera_platform&category=integration)

Or manually:
1. In Home Assistant, go to **HACS** → **Integrations** → **⋮** (top right) → **Custom repositories**
2. Add `https://github.com/nrbrt/dirigera_platform` as an **Integration**
3. Search for "Dirigera" and install

## Setup

1. Go to **Settings** → **Devices & Services** → **Add Integration** → search for **Dirigera**
2. Enter the IP address of your Dirigera hub (find it in your router's DHCP client list)
3. When prompted, press the **action button** on the Dirigera hub, then click Submit
4. Your devices will appear automatically

To test with mock devices, enter `mock` as the IP address.

## Bug Reports & Feature Requests

When opening an issue, please include a device data dump — it helps enormously:

1. Go to **Developer Tools** → **Services**
2. Call `dirigera_platform.dump_data` (no parameters needed)
3. Copy the output from the Home Assistant log

See [detailed instructions](docs/dump-data.md).

## Changelog

See [Releases](https://github.com/nrbrt/dirigera_platform/releases) for a full changelog.

### Recent

- **v0.2.13** (2026-04-20) — Fix: split-device entity naming — secondary entities (e.g. MYGGSPRAY illuminance) now inherit the user-configured name from the primary entity
- **v0.2.11/12** (2026-04-17) — Fix: application-level WebSocket keepalive to prevent Dirigera hub "disconnected due to inactivity" (issue #12)
- **v0.2.10** (2026-04-16) — Fix: honor 10s sleep between listener reconnects; add on_open/on_close diagnostics for WebSocket disconnect debugging
- **v0.2.9** (2026-04-15) — Feature: light device triggers — turned_on/turned_off automations (upstream PR #197)
- **v0.2.8** (2026-04-15) — Fix: `ikea_bulb_device_set` registry_entry registration (upstream PR #196)
- **v0.2.7** (2026-04-13) — Fix: replace deprecated `hass.loop` with `asyncio.get_event_loop()` (HA 2026.1+ compatibility)
- **v0.2.6** (2026-04-04) — BILRESA dual button support; consistent device naming for split-devices
- **v0.2.5** (2026-04-04) — Split-device plug support (GRILLPLATS, TOFSMYGGA)
- **v0.2.0** (2026-03-27) — Split-device merging framework; TIMMERFLOTTE support
