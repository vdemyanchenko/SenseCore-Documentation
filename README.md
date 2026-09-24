# SenseCore: DualSense Support for Unreal Engine

SenseCore brings DualSense controller features to Unreal Engine 5.4–5.8 on Windows and macOS. Use Blueprint to control adaptive triggers, rumble, lighting and audio settings, read touch, motion and controller status, and route Audio Mixer or MetaSound audio to USB haptics.

## Features

- Standard gamepad input and additional DualSense buttons.
- Adaptive trigger effects, compatible rumble, lightbar, player LEDs and microphone LED.
- Touchpad contacts, raw motion sensors, battery and USB headset status.
- Controller microphone mute, volume and audio routing controls.
- USB PCM haptics through an Audio Mixer endpoint, including MetaSound sources.
- Local-player routing, controller assignment helpers and diagnostics.

## Compatibility

Testing on Windows 11 and macOS 26 with UE 5.4–5.8 covered the API, USB haptics including MetaSound, and connection/reconnection scenarios with the available controllers.

One DualSense is the validated macOS configuration. Windows testing also covered local multiplayer with DualSense and Xbox controllers. Multiple DualSense controllers and DualSense Edge have implementation support but have not been tested on hardware. macOS local multiplayer may work in some configurations, but device identification, assignment and input routing are not guaranteed in this release.

PCM haptics and headset connection status require USB. For the feature matrix and test scope, see [Compatibility](Docs/COMPATIBILITY.md).

## Get Started

Follow [Quick Start](Docs/QUICK_START.md) to install the plugin and play your first effect. On macOS, use Unreal's native standard gamepad input with SenseCore raw gamepad input disabled and supplemental buttons enabled; see [Platform Notes](Docs/PLATFORM_NOTES.md) for the required setup and button-mapping differences.

## Documentation

| Guide | Contents |
|---|---|
| [Quick Start](Docs/QUICK_START.md) | Installation and a first Blueprint effect |
| [Compatibility](Docs/COMPATIBILITY.md) | Features, platforms, models and validated configurations |
| [Platform Notes](Docs/PLATFORM_NOTES.md) | Windows input ownership and macOS setup |
| [Configuration](Docs/CONFIGURATION.md) | Settings, defaults and persistence |
| [Blueprint API](Docs/BLUEPRINT_API.md) | Nodes, parameters and return values |
| [Local Multiplayer](Docs/INPUT_DEVICE_USER_SUBSYSTEM.md) | Join flows, assignment and reconnects |
| [Engine Differences](Docs/ENGINE_COMPATIBILITY.md) | Version-specific assignment behavior |
| [Advanced Haptics](Docs/ADVANCED_HAPTICS.md) | Audio Mixer and MetaSound routing |
| [Troubleshooting](Docs/TROUBLESHOOTING.md) | Symptoms and corrective steps |
| [Diagnostics and Support](Docs/DIAGNOSTICS.md) | Reports, status panel and contacting support |
| [API Compatibility](Docs/API_STABILITY.md) | Public API and identifier lifetime |

## Support

Contact [demianchenko.dev@outlook.com](mailto:demianchenko.dev@outlook.com). Include your engine/OS versions, controller model, connection type, reproduction steps and a [diagnostic report](Docs/DIAGNOSTICS.md).

## Trademark Notice

DualSense, DualSense Edge, PlayStation, and related names are trademarks or registered trademarks of Sony Interactive Entertainment Inc. SenseCore is an independent Unreal Engine plugin and is not affiliated with, endorsed by, or sponsored by Sony Interactive Entertainment Inc.
