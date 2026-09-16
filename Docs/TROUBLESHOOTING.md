# Troubleshooting

Use this guide when a controller is not detected, output does not play, or advanced haptics are not routed as expected.

## Generate a Diagnostic Report

Use `Get DualSense Diagnostic Report` and copy the output into your private support request.

By default, the report omits sensitive identifiers. Enable `Include Sensitive Identifiers` only when support needs exact HID device paths, endpoint ids, or container ids.

## Controller Is Not Detected

| Check | What to do |
|---|---|
| Plugin enabled | Confirm `SenseCore` is enabled in `Edit > Plugins` |
| Platform | Confirm you are running on Windows or macOS |
| Connection | Try USB first, then Bluetooth |
| Device power | Turn the controller off and reconnect it |
| Unreal restart | Restart the editor after enabling the plugin |
| Logs | Search for `DualSenseSupport`, `DualSenseInput`, or `DualSense` in the output log |

## Input Appears Twice

On macOS or when another gamepad layer is active, standard axes or buttons may be published by both Unreal/native input and raw HID input.

Recommended fixes:

- Keep the default Mac raw analog behavior unless you explicitly need raw axes.
- Use `Set DualSense Gamepad Input Enabled` if another input layer should own standard gamepad buttons.
- Keep DualSense supplemental button publishing available for PS, mic, touchpad click, and Edge buttons.

## Rumble Does Not Play

| Check | What to do |
|---|---|
| Target player | Use a local `PlayerController` target |
| Controller assignment | Call `Get Connected DualSense Devices` and verify `PlatformUserId` |
| Force feedback bridge | Check `Is DualSense Engine Force Feedback Enabled` |
| Connection type | Test USB first |
| Competing software | Close tools that may own the controller |

Use `Set DualSense Vibration` with a local `PlayerController` target for gameplay logic.

## Player Joins the Wrong User After Reconnect

Unreal can assign a newly reconnected gamepad to the next available platform user. If several controllers reconnect at nearly the same time, this may put a physical controller on the wrong user and therefore the wrong local player.

Recommended fixes:

- Use `Input Device User Subsystem`.
- Set user assignment mode to `Keep Runtime User Assignment` after the initial desired mapping is established.
- Use `Get Input Device User Info` or `Get Input Device Mapping Debug String` while debugging controller routing.
- Clear runtime assignments only when intentionally rebuilding local player/controller ownership.

## Press-to-Join Does Not Fire

The standard PlayerController input path cannot receive input from a gamepad before that gamepad has a local player. SenseCore solves this by registering an input pre-processor in `Input Device User Subsystem`.

Check these items:

| Check | What to do |
|---|---|
| Press-to-join enabled | Call `Set Press To Join Enabled(true)` |
| Join keys | Call `Set Join Keys` with gamepad keys such as `Gamepad Face Button Bottom` |
| Device already assigned | Press-to-join ignores devices that already have a local player |
| Device type | Keyboard/mouse identifiers are ignored |

## Adaptive Triggers Stay Active

Call `Stop DualSense Triggers` when leaving gameplay, switching weapons, pausing, or ending PIE. SenseCore also clears output effects during game instance shutdown, but explicit gameplay cleanup gives the best player experience.

## Advanced Haptics Endpoint Is Not Ready

| Check | What to do |
|---|---|
| USB | Use a USB connection for PCM haptics |
| Endpoint list | Call `Get DualSense Advanced Haptics Audio Endpoints` |
| Runtime status | Call `Get DualSense Advanced Haptics Endpoint Status` |
| Routing settings | Prefer `InputDeviceId` or `PlatformUserId` for local multiplayer |
| Signal level | Keep `MaximumAmplitude` at or below `0.25` during testing |
| MetaSound output | Confirm the source is actually playing and routed to the endpoint/submix |

If status repeatedly returns `Recovering`, check whether the physical controller was disconnected, the audio endpoint changed, or another process took over the device.

## Support Checklist

| Item | Example |
|---|---|
| Unreal Engine version | `5.6.1` |
| OS version | `Windows 11 24H2` or `macOS 15.x` |
| Controller model | `DualSense` or `DualSense Edge` |
| Connection mode | `USB` or `Bluetooth` |
| Repro steps | Short numbered list |
| Diagnostic report | Output from `Get DualSense Diagnostic Report` |
| Logs | Relevant `DualSense` log lines |
