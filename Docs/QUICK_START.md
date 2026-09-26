# Quick Start

Start with one DualSense connected by USB. See [Compatibility](COMPATIBILITY.md) for tested configurations.

## 1. Install

1. Install the package for your engine version and platform into the project's `Plugins` directory. When using a source checkout, the folder is `SenseCore`; build it with the matching engine/toolchain.
2. Open the project and enable **SenseCore** in **Edit > Plugins**.
3. Restart the editor if prompted.

On macOS, complete [macOS setup](PLATFORM_NOTES.md#macos-setup) before testing: native Game Controller input enabled, SenseCore raw gamepad input disabled, supplemental buttons enabled.

Before packaging a Mac application, configure its [USB and Bluetooth HID entitlements](PLATFORM_NOTES.md#packaged-app-hid-permissions).

## 2. Get the Local Player

Use the local `PlayerController` that should receive the effect. For a single-player test, use `Get Player Controller` with player index `0`.

Call `Is DualSense Connected` with this controller. If it returns false, check the device list and assignment using [Troubleshooting](TROUBLESHOOTING.md). Allow time for detection after connecting a controller; retry the check if necessary.

Standard gameplay controls use Unreal gamepad keys in your Input Mapping Context. Additional keys such as `DualSense Touchpad Click` can be mapped separately. SenseCore does not install Mapping Contexts for your game.

## 3. Play Rumble

Call `Set DualSense Vibration` with:

| Pin | Value |
|---|---|
| `TargetPlayer` | Your local PlayerController |
| `LeftMotor` | `0.8` |
| `RightMotor` | `0.4` |
| `Duration` | `0.5` seconds |

The effect stops after the duration. A duration of `0` creates no stop timer; set both motor values to `0` to stop it explicitly. Active PCM haptics take priority over compatible rumble.

## 4. Try a Trigger Effect

Call `Set DualSense Trigger Weapon` with the same player:

| Pin | Value |
|---|---|
| `Hand` | `Right` |
| `StartPosition` | `2` |
| `EndPosition` | `7` |
| `Strength` | `0.8` |

Call `Stop DualSense Triggers` when the effect should end. Trigger effects do not have an automatic duration. Stop vibration and trigger effects when leaving gameplay.

## 5. Read Controller State

Use `Get DualSense Touch State`, `Get DualSense Motion State`, `Get DualSense Battery State` and `Get DualSense Headset State` with the same local player.

Check each function's return value before using its output. Touch indices are `0` and `1`; check `bActive` for contact. For headset state, also check `bStatusAvailable`: headphone and microphone connection status is unavailable over Bluetooth.

## Next Steps

- [Advanced Haptics](ADVANCED_HAPTICS.md): connect an Audio Mixer or MetaSound source to USB PCM haptics.
- [Local Multiplayer](INPUT_DEVICE_USER_SUBSYSTEM.md): join flows and controller ownership.
- [Blueprint API](BLUEPRINT_API.md): parameters and result semantics. A successful output call does not confirm the physical effect occurred.
- [Diagnostics and Support](DIAGNOSTICS.md): collect a report if a feature does not work.
