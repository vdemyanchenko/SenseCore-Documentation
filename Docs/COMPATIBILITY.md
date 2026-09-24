# Compatibility

SenseCore supports Unreal Engine 5.4–5.8 on Windows and macOS. Use a plugin build matching your engine version and platform.

## Validated Configurations

The following results are reported by the developer for the available hardware. OS versions identify the tested systems, not minimum OS requirements.

| Platform | Engine versions |
|---|---|
| Windows 11 | 5.4, 5.5, 5.6, 5.7, 5.8 |
| macOS 26 | 5.4, 5.5, 5.6, 5.7, 5.8 |

Testing covered all public API functions, USB PCM haptics including MetaSound, and connection/reconnection scenarios in different orders with the available DualSense and Xbox controllers. These results do not establish coverage of every controller model, Bluetooth adapter, OS version or Mac architecture.

| Controller configuration | Status |
|---|---|
| One DualSense | Tested on Windows and macOS; see macOS button-mapping notes |
| DualSense with Xbox controllers | Windows local multiplayer tested |
| Multiple DualSense controllers | Implemented on Windows; not hardware-tested |
| DualSense Edge | Model recognition and additional button handling implemented; not hardware-tested |
| Multiple gamepads on macOS | Known identification and routing limitations; not a validated configuration for this release |

macOS local multiplayer may work in some configurations. Correct device identification, player assignment and independent input/output routing are not guaranteed. See [Platform Notes](PLATFORM_NOTES.md).

## Features by Connection

This table describes the implemented feature paths. The validation scope above applies; a supported feature is not a claim that every hardware combination has been tested.

| Feature | Windows USB | Windows Bluetooth | macOS USB | macOS Bluetooth |
|---|---|---|---|---|
| Standard buttons, sticks, triggers and D-pad | SenseCore input | SenseCore input | Native UE input | Native UE input |
| Additional DualSense buttons | Supported | Supported | Supported; mapping note below | Supported; mapping note below |
| Compatible rumble | Supported | Supported | Supported | Supported |
| Adaptive triggers | Supported | Supported | Supported | Supported |
| Lightbar, player LEDs and microphone LED | Supported | Supported | Supported | Supported |
| Touch contacts and raw motion data | Supported | Supported | Supported | Supported |
| Battery state | Supported | Supported | Supported | Supported |
| Controller audio control commands | Supported | Supported | Supported | Supported |
| Headphone/microphone connection status | Available | Unavailable | Available | Unavailable |
| PCM haptics, including MetaSound | Supported | Not supported | Supported | Not supported |

Audio control commands set controller volume, mute and routing fields. They do not create an OS audio device, capture microphone audio or stream game audio over Bluetooth. Headset state is a separate read API; check `bStatusAvailable` before using its fields.

On macOS, keep SenseCore raw standard gamepad input disabled and supplemental buttons enabled. With the recommended native input mode, touchpad click also produces `Gamepad Special Left`, while Create is not published separately. See [macOS setup](PLATFORM_NOTES.md#macos-setup).

Motion values are raw sensor readings, not a calculated orientation. Battery percentage is an estimate derived from the controller report.

## Scope

Linux, mobile and consoles are not supported. SenseCore does not integrate a PlayStation console SDK.

Controller effects operate on local hardware and are not network-replicated. In a networked game, invoke effects on the client that owns the local controller.
