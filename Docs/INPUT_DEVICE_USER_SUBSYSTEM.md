# Input Device User Subsystem

`UInputDeviceUserSubsystem` adds local multiplayer controller assignment tools on top of Unreal's platform user and input device mapping system.

Windows local multiplayer with DualSense and Xbox controllers has been tested. Multiple DualSense controllers have not been hardware-tested. On macOS, device identification and independent input/output routing are not guaranteed; see [Platform Notes](PLATFORM_NOTES.md#local-multiplayer-limits). The assignment guidance below primarily describes Windows behavior.

The subsystem observes gamepad button events before PlayerController routing, allowing a join request before that device has a local player. Optional runtime assignment memory can restore a remembered user after reconnecting.

## Main Use Cases

| Use case | What the subsystem provides |
|---|---|
| Press-to-join | Detects input from connected gamepads that do not yet have a local player |
| Auto-join on connection | Creates local players when gamepads connect |
| Reconnect assignment | Keeps runtime device-to-user assignments instead of accepting every new automatic UE assignment |
| Debugging | Lists input devices, platform users, local-player assignment, device names, and hardware identifiers |

## Events

| Event | When it fires |
|---|---|
| `On Unassigned Device Input` | With press-to-join enabled, a recognized gamepad without a local player sends a non-repeat key press |
| `On Device Join Requested` | The key press is one of the configured join keys |

Both events provide:

| Value | Meaning |
|---|---|
| `InputDeviceId` | The gamepad that generated input |
| `PlatformUserId` | The user currently mapped to that input device |
| `Key` or `JoinKey` | The pressed gamepad key |

## Assignment Modes

| Mode | Behavior |
|---|---|
| `UE Automatic` | Uses the engine policy on UE 5.6+ and the configured legacy mapper/primary-user path on Windows UE 5.4-5.5 |
| `Keep Runtime User Assignment` | Restores DualSense assignments; on UE 5.6+ also restores recognized third-party gamepads |

Use `Keep Runtime User Assignment` after your game has established the desired player/controller mapping. This helps prevent reconnect order from moving controllers to the wrong users.

The mode is configured in Project Settings > Plugins > SenseCore DualSense > Input Devices. `Set User Assignment Mode` updates the same setting and saves `UserAssignmentMode=UEAutomatic` or `UserAssignmentMode=KeepRuntimeAssignment` in the project/user config. It persists across PIE sessions and restarts. `Get User Assignment Mode` reads that setting directly. All active subsystem instances apply changes from either Project Settings or Blueprint; newly initialized instances apply the saved mode.

Only the policy is saved to config. DualSense assignments live in module memory, survive PIE restarts and reconnects outside PIE, and are discarded when the module/process ends. Switching modes clears remembered assignments; entering `Keep Runtime User Assignment` snapshots eligible connected devices (plugin-owned DualSense on UE 5.4–5.5; also recognized third-party gamepads on UE 5.6+). Setting the same mode does not clear assignments. On UE 5.6+ recognized third-party gamepads are restored too; on UE 5.4-5.5 only plugin-owned DualSense devices are restored. This does not preserve LocalPlayer objects between PIE sessions. Restoration requires the same device identity.

See [Configuration](CONFIGURATION.md) for save locations and the distinction between persistent settings and session join controls.

## Join Controls

| Function | Purpose |
|---|---|
| `Set Auto Join On Connection Enabled` | Enables or disables automatic local-player creation when a known gamepad connects |
| `Is Auto Join On Connection Enabled` | Reads the auto-join setting |
| `Set Press To Join Enabled` | Enables or disables press-to-join detection |
| `Set Join Keys` | Sets which gamepad keys request a join |

Auto-join defaults to off; press-to-join defaults to on. The default join key is `Gamepad Face Button Bottom`. These controls are session-only. Press-to-join raises an event; it does not create a player automatically. Enabling auto-join also attempts to add already connected eligible devices.

## Device Info

`Get Input Device User Info` returns an array of `FInputDeviceUserInfo`.

| Field | Meaning |
|---|---|
| `InputDeviceId` | Unreal input device id |
| `PlatformUserId` | Mapped Unreal platform user |
| `bConnected` | Whether the device is currently connected |
| `bPairedToUser` | Whether the device has a valid non-unpaired user |
| `bAssignedToLocalPlayer` | Whether a local player owns this device |
| `DeviceName` | Registered input device name |
| `HardwareIdentifier` | Registered hardware identifier |

`Get Input Device Mapping Debug String` returns a readable mapping dump for development builds. In Shipping builds it returns a short unavailable message.

## Recommended Press-to-Join Flow

1. On game instance startup, get `Input Device User Subsystem`.
2. Call `Set Press To Join Enabled(true)`.
3. Call `Set Join Keys` with your accepted join buttons.
4. Bind to `On Device Join Requested`.
5. When the event fires, create or confirm a local player for the provided `PlatformUserId`/`InputDeviceId` according to your game's UI flow.
6. When gameplay should receive input, call `Focus Game Viewport For Player` on this subsystem, passing the PlayerController returned by player creation. Check its return value and `Error`. If the viewport is not ready, retry after initialization (for example, on the next tick).
7. After player ownership is correct, set assignment mode to `Keep Runtime User Assignment` if reconnects should return to the same participant.

### Explicit gameplay focus

`Focus Game Viewport For Player` resolves the Slate user from the local player's Platform User ID and focuses that player's viewport. It does not use the LocalPlayers array index, change assignments, install Mapping Contexts, or change input mode. It rejects controllers belonging to another game instance. Call it when leaving a join screen for gameplay; leave focus on your UI while navigating menus.

A platform-user ID can differ from a local-player array index, including after PIE restarts. Adding a Mapping Context alone does not establish Slate focus.

## Recommended Auto-Join Flow

1. Set assignment mode to `Keep Runtime User Assignment` if your game already knows the desired mappings.
2. Call `Set Auto Join On Connection Enabled(true)`.
3. The subsystem will attempt to create local players for connected gamepads that do not already have local players.

This is useful for couch co-op games where connecting a controller should immediately add a player.

## Reconnect Behavior

With Unreal's default automatic mapping, a controller reconnect may receive a new platform user id. When multiple controllers reconnect together, player ownership can become scrambled.

`Keep Runtime User Assignment` restores the remembered DualSense user before the connection notification. On UE 5.6+ remembered mappings are enforced while this mode is enabled. To intentionally change ownership, disable the mode or clear remembered assignments first. `Clear Runtime User Assignments` clears remembered users globally without changing live mappings; connected DualSense assignments are remembered again on subsequent input polling. Switching to `UE Automatic` disables restoration without undoing live mappings.
