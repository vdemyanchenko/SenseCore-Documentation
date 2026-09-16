# Fab Listing Draft

Use this as a starting point for the Fab product page.

## Product Name

SenseCore: DualSense Support for Unreal Engine

## Short Description

Blueprint-friendly DualSense and DualSense Edge support for Unreal Engine, including raw HID input, adaptive triggers, rumble, LEDs, touch, motion, battery status, audio controls, USB advanced haptics, and diagnostics for Windows and macOS.

## Long Description

SenseCore gives Unreal Engine projects direct access to DualSense-specific controller features on Windows and macOS. It is built for teams that want tactile feedback, controller status, supplemental buttons, and player-routed output without writing raw HID, platform audio endpoint, or controller assignment code.

Use familiar Blueprint nodes to drive compatible rumble, adaptive trigger effects, lightbar color, player LEDs, mute LED, microphone state, audio routing, touchpad data, motion sensor values, battery state, and DualSense Edge inputs. For richer tactile design, route MetaSound or Audio Mixer output into the USB advanced haptics endpoint.

SenseCore also includes diagnostic helpers and status data so developers can inspect connected controllers, routing, endpoint state, queue health, and support reports during development.

## Feature Highlights

- Direct DualSense and DualSense Edge support for Windows and macOS
- Blueprint-first gameplay API
- USB and Bluetooth HID input support
- Adaptive trigger presets and custom trigger payloads
- Compatible rumble with player-based routing
- Lightbar, player LEDs, mute LED, mic mute, volume, and audio routing controls
- Touchpad, motion sensor, battery, headset, and connection status reads
- DualSense Edge rear button and function button support
- USB PCM advanced haptics endpoint for Audio Mixer and MetaSound workflows
- Multi-device status and preferred device selection
- Press-to-join and auto-join workflows for local multiplayer controller assignment
- Diagnostic report and endpoint runtime status for support workflows

## Technical Details

| Detail | Value |
|---|---|
| Plugin type | Runtime code plugin |
| Module | `DualSenseSupport` |
| Supported platforms | Windows, macOS |
| Primary workflow | Blueprint and Unreal Audio Mixer endpoint |
| Content required | No gameplay content required |
| Network replicated | No, local controller hardware feature |

## Compatibility Notes

- Advanced PCM haptics are intended for USB DualSense connections.
- Bluetooth support depends on OS and adapter behavior for output reports.
- macOS may also publish standard axes through the native gamepad layer; SenseCore defaults are configured to reduce duplicate raw analog input.
- This is not a console SDK integration and does not provide PlayStation platform support.

## Suggested Gallery Assets

| Asset | Purpose |
|---|---|
| Main image | Controller plus Unreal Blueprint node composition |
| Screenshot 1 | Blueprint nodes for vibration, trigger, and lightbar |
| Screenshot 2 | MetaSound or Audio Mixer advanced haptics routing |
| Screenshot 3 | Runtime status panel or diagnostic view |
| Screenshot 4 | Compatibility matrix |
| Video | 30-60 second demo of triggers, LEDs, haptics, and status panel |

## Trademark Notice

DualSense, DualSense Edge, PlayStation, and related names are trademarks or registered trademarks of Sony Interactive Entertainment Inc. SenseCore is an independent Unreal Engine plugin and is not affiliated with, endorsed by, or sponsored by Sony Interactive Entertainment Inc.
