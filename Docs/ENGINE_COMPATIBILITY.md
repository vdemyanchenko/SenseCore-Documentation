# Engine Differences

SenseCore supports UE 5.4–5.8. [Compatibility](COMPATIBILITY.md) records tested platforms and controller configurations. Use a separate compiled plugin package for each engine version and platform.

## Windows User Assignment

| Engine | UE Automatic behavior | Keep Runtime User Assignment |
|---|---|---|
| 5.4–5.5 | Uses Legacy User Policy; primary user by default | Restores plugin-owned DualSense assignments, subject to the legacy policy |
| 5.6–5.8 | Uses the engine's new-device assignment policy | Restores DualSense and recognized third-party gamepad assignments |

### UE 5.4–5.5

**Legacy User Policy (UE 5.4–5.5)** is in **Project Settings > Plugins > SenseCore DualSense > Input Devices**.

- **Primary User (Single Player)** assigns plugin controllers to the primary user while retaining separate device IDs.
- **Follow Engine (Legacy Mapper)** uses the legacy mapper for separate user assignment. DualSense IDs are allocated outside XInput's reserved slots 0–3.

Reconnect controllers after changing this setting. Existing local players and live assignments are not changed immediately. Old remembered mappings are invalidated before the new policy selects a user on reconnection. Primary User takes precedence over restoring a separate remembered user.

### UE 5.6–5.8

UE Automatic follows the engine's new-device policy. For single-player shared input, configure Unreal's `MapAllDevicesToPrimaryUser` policy before launch. Do not assume a live engine-policy change updates existing assignments.

Keep Runtime User Assignment restores remembered users within the process, including across PIE restarts. It does not recreate LocalPlayer objects. See [Local Multiplayer](INPUT_DEVICE_USER_SUBSYSTEM.md).

## Device Discovery

On UE 5.4–5.7, third-party gamepad metadata may become available only after a button event. Auto-join retries on that event; names may remain unknown when a driver supplies no descriptor. On UE 5.8, metadata uses Unreal's input device registry.

Input device IDs, platform user IDs and LocalPlayer indices are different identifiers. A driver sharing one device ID between keyboard and gamepad cannot provide independent device assignment through that ID.

## macOS

The Windows assignment policies do not provide independent Mac HID controller routing. Use the native input setup and observe the [macOS local multiplayer limitations](PLATFORM_NOTES.md#local-multiplayer-limits).

Source build and regression-test instructions are in [Development Validation](DEVELOPMENT_VALIDATION.md).
