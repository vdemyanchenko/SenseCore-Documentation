# Troubleshooting

Start with one USB DualSense and a local PlayerController. Check [Compatibility](COMPATIBILITY.md) before diagnosing an unvalidated configuration.

## Controller Not Found

1. Confirm SenseCore is enabled and the package matches your engine/platform.
2. Connect by USB and allow time for detection.
3. Call `Get Connected DualSense Devices`. If the list is empty, check the connection and `DualSense` log messages.
4. If a device is listed but `Is DualSense Connected` is false, check the target local player and platform-user assignment.
5. On Mac, apply [macOS setup](PLATFORM_NOTES.md#macos-setup). Multi-controller identification is not guaranteed.

## DualSense HID Is Unavailable in a Packaged Mac App

Check the application's USB and Bluetooth sandbox entitlements. In the project's `Build/Mac/Resources/Sandbox.Server.entitlements` and `Sandbox.NoNet.entitlements`, set `com.apple.security.device.bluetooth` and `com.apple.security.device.usb` to `true` inside the existing plist dictionary. Repackage and sign the app with the updated permissions.

See [Packaged App HID Permissions](PLATFORM_NOTES.md#packaged-app-hid-permissions) for the XML entries and file locations. Changing SenseCore input switches does not grant the application device access.

## Duplicate or Missing Input

On Mac, disable SenseCore raw gamepad input and enable supplemental buttons. With `Slate.MacControllerPreferGCImpl=1`, touchpad click produces both native `Gamepad Special Left` and supplemental `DualSense Touchpad Click`. Create is not separately published in that mode. Adjust Input Actions accordingly; see [button mapping](PLATFORM_NOTES.md#button-mapping).

On Windows, check whether another program or input plugin publishes the controller as a virtual gamepad. Choose one standard-input owner. SenseCore output can remain enabled when its standard input is disabled.

Confirm the local player has the intended Enhanced Input Mapping Context and viewport focus. After joining, `Focus Game Viewport For Player` can establish gameplay focus without changing input mode or Mapping Contexts.

## Rumble or Other Output Does Not Play

- Use a local PlayerController and verify its selected device.
- Check parameter ranges and the function's return value. A true result does not confirm physical playback; several setters report accepted state rather than HID-write success.
- For Unreal force-feedback effects, enable `Enable Engine Force Feedback`. Direct `Set DualSense Vibration` calls do not depend on this switch.
- Stop PCM haptics before testing compatible rumble: active PCM takes priority, and suppressed rumble calls are not queued.
- Test with one USB controller and close software that may control the same device.

## Headset Flags Are False over Bluetooth

The current API provides headphone/microphone connection status only over USB on both platforms. Check `bStatusAvailable`; false means unavailable data, not a confirmed absence of headphones or a microphone. Audio control commands are a separate feature.

## Wrong Player or No Join Event

Check `Get Input Device User Info`, filtering `bConnected`. Device IDs, platform-user IDs and local-player indices are not interchangeable.

On Windows UE 5.4–5.5, the default Legacy User Policy assigns DualSense devices to the primary user. Choose Follow Engine and reconnect when you need separate users. For reconnect ownership, use Keep Runtime User Assignment after establishing the intended mappings. See [Local Multiplayer](INPUT_DEVICE_USER_SUBSYSTEM.md).

Press-to-join requires an enabled join listener, a configured gamepad key and a recognized gamepad without an existing local player. Auto-join may already have created that player. On older engines, third-party metadata can become available only after the first button event.

Mac multi-controller discovery and routing have known limitations. The legacy Mac input mode also showed stuck input for a second DualSense player in testing; prefer the recommended native mode.

## Adaptive Triggers Stay Active

Call `Stop DualSense Triggers` when ending the effect, leaving gameplay or pausing. Trigger effects do not have a duration parameter. The plugin also clears effects during game-instance shutdown.

## PCM Endpoint Is Not Ready or Produces No Effect

1. Use USB and confirm a capable endpoint appears in `Get DualSense Advanced Haptics Audio Endpoints`.
2. Verify endpoint selection. Automatic selection needs exactly one candidate; explicit selectors must resolve to a current device.
3. Confirm the audio source is playing, routed to the endpoint submix, and has nonzero amplitude/send level.
4. For stereo sources, enable `Route Front Channels To Actuators`. Verify `MaximumAmplitude` is not zero.
5. Read runtime status. `Ready` confirms an open renderer, not a non-silent signal. Check counters during intended playback.
6. If recovery repeats, inspect the audio error and connection; re-enumerate after a device or endpoint change.

See [Advanced Haptics](ADVANCED_HAPTICS.md) for a first-playback setup. If the issue persists, send a report using [Diagnostics and Support](DIAGNOSTICS.md).
