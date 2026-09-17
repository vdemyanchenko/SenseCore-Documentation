
# SenseCore: DualSense Support for Unreal Engine

SenseCore adds direct DualSense and DualSense Edge support to Unreal Engine projects on Windows and macOS. It exposes controller input, adaptive triggers, compatible rumble, lightbar and player LEDs, touchpad state, motion sensors, battery and headset state, audio controls, USB advanced haptics, and controller-assignment helpers through Blueprint-friendly APIs.

The plugin is built for teams that want DualSense-specific features without writing raw HID code, platform audio endpoint code, or local multiplayer controller-assignment glue.

<p align="center">
  <img src="Images/sc1.png" width="100%" alt="SenseCore">
</p>

## Feature Overview

| Area | Included features |
|---|---|
| Input | Standard gamepad buttons, sticks, triggers, D-pad, PS button, mic button, touchpad click, touch contacts, motion sensors, DualSense Edge rear buttons |
| Local multiplayer | Platform-user based routing, preferred controller selection, press-to-join, auto-join on connection, stable runtime reassignment after reconnect |
| Output | Compatible rumble, adaptive trigger effects, lightbar color, player LEDs, mute LED, mic mute, speaker and headset volume controls |
| Advanced haptics | USB PCM haptics endpoint for Unreal Audio Mixer and MetaSound workflows |
| Diagnostics | Support report, connected-device info, endpoint runtime status, optional UMG status panel |

## Compatibility

| Platform / connection | Input | Output | Advanced haptics | Notes |
|---|---|---|---|---|
| Windows USB | Supported | Supported | Supported | Recommended path for full feature coverage |
| Windows Bluetooth | Supported | Limited by adapter/driver HID output support | Not supported | Bluetooth behavior can vary by adapter and driver stack |
| macOS USB | Supported | Supported | Supported through the platform audio endpoint path | Recommended for validation and capture |
| macOS Bluetooth | Supported through SenseCore input; native input may be incomplete | Limited by macOS HID output support | Not supported | Enable SenseCore gamepad input if native Bluetooth input is missing standard controls |
| Linux, mobile, consoles | Not supported | Not supported | Not supported | Not included in the current plugin allow list |

Console support would require platform-holder SDK integration and is outside the scope of this plugin.

## Documentation

| Document | Start here when you need to... |
|---|---|
| [Quick Start](Docs/QUICK_START.md) | Install the plugin, connect a controller, and validate basic output |
| [Blueprint API](Docs/BLUEPRINT_API.md) | Look up Blueprint nodes, pins, ranges, and return values |
| [Input Device User Subsystem](Docs/INPUT_DEVICE_USER_SUBSYSTEM.md) | Build press-to-join, auto-join, and stable controller reassignment flows |
| [Advanced Haptics and MetaSound](Docs/ADVANCED_HAPTICS.md) | Route authored PCM haptics through Audio Mixer and MetaSound |
| [Platform Notes](Docs/PLATFORM_NOTES.md) | Understand Windows/macOS connection behavior and macOS controller settings |
| [Diagnostics](Docs/DIAGNOSTICS.md) | Generate reports and collect useful support data |
| [Troubleshooting](Docs/TROUBLESHOOTING.md) | Diagnose detection, routing, rumble, trigger, and haptics issues |
| [API Stability](Docs/API_STABILITY.md) | Understand the supported public API surface and naming compatibility |

## First Validation Path

1. Install the plugin into your project's `Plugins` directory.
2. Enable `SenseCore` in `Edit > Plugins`.
3. Connect a DualSense or DualSense Edge controller over USB.
4. Call `Is DualSense Connected` for a local `PlayerController`.
5. Test `Set DualSense Vibration`, `Set DualSense Lightbar Color`, and one adaptive trigger function.
6. For USB advanced haptics, configure the `DualSenseHaptics` endpoint submix and route a MetaSound or Audio Mixer signal to it.

USB is the recommended first test path because it covers input, output, adaptive triggers, and the advanced haptics endpoint.

## macOS Controller Setting

For DualSense projects on macOS, prefer Unreal's legacy macOS controller path:

```ini
[SystemSettings]
Slate.MacControllerPreferGCImpl=0
```

The newer native Game Controller implementation can publish the DualSense touchpad click as `Special Left` while not publishing the actual `Special Left`/Create button consistently. The legacy path gives SenseCore's raw HID layer more predictable ownership of supplemental DualSense buttons.

## Blueprint and C++ Use

The documented gameplay surface is Blueprint-first and lives in the `DualSense` Blueprint categories. The runtime module is named `DualSenseSupport`, and reflected C++ types retain their existing `DualSense` naming for compatibility with existing Blueprint assets.

For gameplay code, prefer player-targeted functions that take a local `PlayerController`. Device-path and endpoint identifiers are useful for diagnostics and explicit routing tools, but they are session identifiers and should not be saved between controller connections.

## Support

When reporting an issue, include:

| Item | Example |
|---|---|
| Unreal Engine version | `5.6.1` |
| OS version | `Windows 11 24H2` or `macOS 15.x` |
| Controller model | `DualSense` or `DualSense Edge` |
| Connection mode | `USB` or `Bluetooth` |
| Repro steps | Short numbered list |
| Expected / actual result | What should have happened and what happened instead |
| Diagnostic report | Output from `Get DualSense Diagnostic Report` |
| Logs | Relevant `DualSense` log lines |

`Get DualSense Diagnostic Report` excludes sensitive identifiers by default. Enable `Include Sensitive Identifiers` only when sharing a private support report.

## Trademark Notice

DualSense, DualSense Edge, PlayStation, and related names are trademarks or registered trademarks of Sony Interactive Entertainment Inc. SenseCore is an independent Unreal Engine plugin and is not affiliated with, endorsed by, or sponsored by Sony Interactive Entertainment Inc.
