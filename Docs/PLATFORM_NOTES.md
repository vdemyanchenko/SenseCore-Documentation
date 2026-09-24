# Platform Notes

See [Compatibility](COMPATIBILITY.md) for the feature matrix and validated engine/OS configurations.

## Windows

SenseCore publishes standard and additional DualSense input through HID. Raw gamepad input and supplemental buttons are enabled by default.

If another input layer exposes the same physical controller as a virtual gamepad, choose which layer publishes standard input. Disable `Enable Raw Gamepad Input` in SenseCore when the other layer should own it. SenseCore output controls remain available.

Unreal force-feedback forwarding has a separate switch. Disabling it does not disable direct `Set DualSense Vibration` calls.

### USB and Bluetooth handoff

When Windows provides a valid controller identity, SenseCore uses one logical input-device ID for that controller's USB and Bluetooth connections within the process. Working USB input takes priority. Switching transports releases held input and changes the output route. If identity is unavailable, connections remain separate.

A complete disconnect can change user assignment under the engine's policy. Use [Keep Runtime User Assignment](INPUT_DEVICE_USER_SUBSYSTEM.md) when reconnects should restore the previous user. Obtain current device paths again after reconnecting or changing transport.

## macOS Setup

Use Unreal's native gamepad input. In the project's `Config/DefaultEngine.ini`, set:

```ini
[SystemSettings]
Slate.MacControllerPreferGCImpl=1
```

In **Project Settings > Plugins > SenseCore DualSense**:

- Disable **Enable Raw Gamepad Input**.
- Enable **Enable Supplemental Button Input**.

These are the plugin's Mac defaults; existing project config can override them. Restart the editor after changing the native controller implementation.

### Button mapping

The recommended Game Controller implementation recognizes DualSense and other gamepads and publishes standard gamepad keys for Enhanced Input. In developer testing on UE 5.4–5.8:

| Physical button | Native UE event | SenseCore supplemental event |
|---|---|---|
| Touchpad click | `Gamepad Special Left` | `DualSense Touchpad Click` |
| Create | Not published separately | No separate Create event |

With supplemental input enabled, touchpad click produces both events. Avoid binding both to actions that should execute only once. Touch contact data remains available through `Get DualSense Touch State`.

### Alternative legacy mode

`Slate.MacControllerPreferGCImpl=0` correctly publishes Create as `Gamepad Special Left` and works with the supplemental touchpad-click event. However, testing did not show distinct gamepad names, and stuck input was observed for a second local player using DualSense. The first DualSense player did not show that input issue.

Use `=1` as the default setup. The legacy mode is an alternative for projects that need its button mapping and have validated their own configuration.

### Local multiplayer limits

One DualSense is the validated configuration. With multiple gamepads, device identification, player assignment and independent input/output routing are not guaranteed in this release. Some configurations may work.

SenseCore's supplemental HID path attaches to the primary native input device for user 0. Its automatic HID-to-audio association on Mac also relies on a single USB DualSense. Enumerating several devices or selecting an audio endpoint manually does not establish independent per-player routing.
