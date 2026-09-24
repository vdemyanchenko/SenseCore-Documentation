# API Compatibility

## Gameplay API

Use player-targeted functions on `UDualSenseBlueprintLibrary` for controller input state and effects. Use `UInputDeviceUserSubsystem` for local-player join and assignment flows. Check [Blueprint API](BLUEPRINT_API.md) for result semantics and [Platform Notes](PLATFORM_NOTES.md) for Mac routing limits.

These APIs operate on local hardware. A networked game must invoke output on the appropriate client.

## Names and Existing Assets

The plugin is named `SenseCore`; its runtime module remains `DualSenseSupport`. Reflected types retain their `DualSense` names and `/Script/DualSenseSupport` paths to preserve existing Blueprint references.

`Set DualSense Primary Vibration` is deprecated. Use `Set DualSense Vibration` with a local PlayerController.

## Identifier Lifetime

Device paths, input-device IDs, platform-user IDs and endpoint IDs serve different purposes. Do not substitute a LocalPlayer array index for a device or platform-user ID.

Obtain current device paths and endpoint IDs from enumeration. They are not portable saved-game identifiers, and their validity is not guaranteed across reconnects, transport changes or machines. Runtime user-assignment memory lasts only for the module/process.

## Lower-Level Interfaces

The support subsystem and HID/audio classes include implementation helpers. Prefer the documented Blueprint library, user subsystem and audio endpoint settings for gameplay integrations. The C++ runtime module dependency is `DualSenseSupport`.

The support subsystem also exposes Blueprint primary-device helpers with names similar to the player-targeted library. Prefer the versions with `TargetPlayer` for gameplay. `On DualSense Connection Changed` reports the primary device's connection/transport changes on a 0.5-second poll; it is not an event for every physical device. `Stop All DualSense Effects` stops rumble and adaptive triggers on all managed controllers; stop PCM audio sources separately.
