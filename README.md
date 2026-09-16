# SenseCore: DualSense Support for Unreal Engine

SenseCore adds direct DualSense and DualSense Edge support to Unreal Engine projects on Windows and macOS. It exposes controller input, adaptive triggers, compatible rumble, lightbar and player LEDs, touchpad state, motion sensors, battery status, audio controls, USB advanced haptics, and runtime diagnostics through Blueprint-friendly APIs.

This plugin is designed for gameplay teams that want DualSense-specific features without writing raw HID or platform audio endpoint code.

## Main Features

| Area | Features |
|---|---|
| Input | Standard gamepad buttons, sticks, triggers, D-pad, PS button, mic button, touchpad click, DualSense Edge rear buttons |
| Player routing | Local player and platform-user based output routing, preferred controller selection, multi-device status, press-to-join and auto-join support |
| Output | Compatible rumble, adaptive trigger effects, lightbar color, player LEDs, mute LED, mic mute, volume and routing controls |
| Sensors | Touchpad contacts, motion sensor values, battery status, headset status |
| Advanced haptics | USB PCM haptics endpoint for Unreal Audio Mixer and MetaSound workflows |
| Diagnostics | Support report, connected-device info, endpoint runtime status, optional UMG status panel |

## Supported Platforms

| Platform | Status | Notes |
|---|---|---|
| Windows | Supported | USB and Bluetooth HID support; USB advanced haptics endpoint support |
| macOS | Supported | USB and Bluetooth HID support; native gamepad axis publishing may overlap with raw input |
| Consoles | Not supported | This plugin is not a Sony SDK integration |

## Documentation

Start here:

- [Quick Start](Docs/QUICK_START.md)
- [Blueprint API](Docs/BLUEPRINT_API.md)
- [Input Device User Subsystem](Docs/INPUT_DEVICE_USER_SUBSYSTEM.md)
- [Advanced Haptics and MetaSound](Docs/ADVANCED_HAPTICS.md)
- [Platform Notes](Docs/PLATFORM_NOTES.md)
- [Diagnostics](Docs/DIAGNOSTICS.md)
- [Troubleshooting](Docs/TROUBLESHOOTING.md)
- [API Stability](Docs/API_STABILITY.md)
- [Fab Listing Draft](Docs/FAB_LISTING_DRAFT.md)

## Important Notes

- Advanced haptics are intended for USB connections. Bluetooth controllers can still use regular HID input and supported output features.
- Use the Audio Mixer endpoint workflow for USB PCM haptics and MetaSound output.

## Trademark Notice

DualSense, DualSense Edge, PlayStation, and related names are trademarks or registered trademarks of Sony Interactive Entertainment Inc. SenseCore is an independent Unreal Engine plugin and is not affiliated with, endorsed by, or sponsored by Sony Interactive Entertainment Inc.
