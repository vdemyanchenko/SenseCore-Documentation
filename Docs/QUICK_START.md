# Quick Start

This guide gets a local DualSense controller working in an Unreal Engine project with SenseCore.

## 1. Install the Plugin

1. Copy `SenseCore-UE5-Plugin` into your project's `Plugins` directory.
2. Restart Unreal Editor.
3. Open `Edit > Plugins`.
4. Enable `SenseCore`.
5. Restart the editor when prompted.

The plugin module is named `DualSenseSupport` and loads during `PreDefault`.

## 2. Connect a Controller

Connect a DualSense or DualSense Edge controller over USB or Bluetooth.

For first validation, USB is recommended because it gives the most complete feature coverage, including advanced haptics endpoint routing.

## 3. Blueprint Setup

Use the functions in the `DualSense` Blueprint categories with a local `Player Controller` target. Player-targeted functions resolve the controller assigned to that Unreal platform user and are the preferred gameplay API.

Typical startup flow:

1. Call `Is DualSense Connected` for the local player.
2. Use `Set DualSense Vibration`, lightbar, LED, audio, or trigger functions with the same player controller.
3. Call `Stop DualSense Triggers` and stop vibration when an effect or gameplay session ends.

For local multiplayer, let Unreal assign each input device to a platform user. Use `Set Preferred DualSense Device` only when multiple supported controllers are mapped to the same user and the game needs an explicit choice.

## 4. Play Basic Rumble

Use `Set DualSense Vibration`:

| Parameter | Suggested test value |
|---|---|
| `TargetPlayer` | Your local PlayerController |
| `LeftMotor` | `0.8` |
| `RightMotor` | `0.4` |
| `Duration` | `0.5` |

Set both motor values to `0` or wait for `Duration` to expire to stop the effect.

## 5. Try Adaptive Triggers

Start with `Set DualSense Trigger Weapon`:

| Parameter | Suggested test value |
|---|---|
| `Hand` | `Right` |
| `StartPosition` | `2` |
| `EndPosition` | `7` |
| `Strength` | `0.8` |

Call `Stop DualSense Triggers` when leaving gameplay, closing menus, or changing weapon state.

## 6. Read Touch, Motion, and Battery

Use these nodes from a local player context:

- `Get DualSense Touch State`
- `Get DualSense Motion State`
- `Get DualSense Battery State`
- `Get DualSense Headset State`

For touch input, valid touch indices are `0` and `1`.

## 7. Avoiding Duplicate Input

SenseCore publishes raw HID input by default. If another input layer publishes the same physical controller as a separate virtual gamepad, disable SenseCore gamepad input events with `Set DualSense Gamepad Input Enabled(false)`. Output features can remain enabled.

Likewise, disable SenseCore engine force-feedback forwarding if another system owns vibration. Direct SenseCore effect calls are independent of that switch.

## 8. Add Advanced Haptics

After basic output works, use the Audio Mixer endpoint flow described in [Advanced Haptics and MetaSound](ADVANCED_HAPTICS.md).

## 9. Generate a Support Report

Use `Get DualSense Diagnostic Report` when troubleshooting. By default it excludes device paths and platform identifiers. Enable `Include Sensitive Identifiers` only when sharing a report privately with support.

## Connection Differences

- USB supports input, standard output effects, and advanced audio-haptics endpoints.
- Bluetooth supports input and HID output effects but is not recommended for advanced PCM haptics.
- A device path is valid only for the lifetime of the current connection. Enumerate devices again after reconnecting.
