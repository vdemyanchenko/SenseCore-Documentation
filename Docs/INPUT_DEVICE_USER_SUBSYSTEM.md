# Input Device User Subsystem

`UInputDeviceUserSubsystem` adds local multiplayer controller assignment tools on top of Unreal's platform user and input device mapping system.

It exists because the standard PlayerController input path only receives gamepad input after a local player exists. That makes common flows such as "press a gamepad button to join" difficult: the gamepad that should create the new player has no PlayerController yet. Unreal can also assign reconnected gamepads to later platform user ids, so simultaneous reconnects may attach a physical controller to the wrong user.

SenseCore solves these problems by registering a Slate input pre-processor that can see gamepad key presses before they reach any PlayerController, and by optionally preserving runtime device-to-user assignments.

## Main Use Cases

| Use case | What the subsystem provides |
|---|---|
| Press-to-join | Detects input from connected gamepads that do not yet have a local player |
| Auto-join on connection | Creates local players when gamepads connect |
| Stable reconnect behavior | Keeps runtime device-to-user assignments instead of accepting every new automatic UE assignment |
| Debugging | Lists input devices, platform users, local-player assignment, device names, and hardware identifiers |

## Events

| Event | When it fires |
|---|---|
| `On Unassigned Device Input` | A known connected gamepad without a local player sends a non-repeat key press |
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
| `UE Automatic` | Uses Unreal's default input-device to platform-user assignment behavior |
| `Keep Runtime User Assignment` | Remembers connected gamepad assignments and restores them when Unreal attempts to remap a known device |

Use `Keep Runtime User Assignment` after your game has established the desired player/controller mapping. This helps prevent reconnect order from moving controllers to the wrong users.

## Join Controls

| Function | Purpose |
|---|---|
| `Set Auto Join On Connection Enabled` | Enables or disables automatic local-player creation when a known gamepad connects |
| `Is Auto Join On Connection Enabled` | Reads the auto-join setting |
| `Set Press To Join Enabled` | Enables or disables press-to-join detection |
| `Set Join Keys` | Sets which gamepad keys request a join |

The default join key is `Gamepad Face Button Bottom`.

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
6. After player ownership is correct, set assignment mode to `Keep Runtime User Assignment`.

## Recommended Auto-Join Flow

1. Set assignment mode to `Keep Runtime User Assignment` if your game already knows the desired mappings.
2. Call `Set Auto Join On Connection Enabled(true)`.
3. The subsystem will attempt to create local players for connected gamepads that do not already have local players.

This is useful for couch co-op games where connecting a controller should immediately add a player.

## Reconnect Behavior

With Unreal's default automatic mapping, a controller reconnect may receive a new platform user id. When multiple controllers reconnect together, player ownership can become scrambled.

`Keep Runtime User Assignment` stores known connected gamepad mappings and restores them when Unreal reports a different user for a known input device. Use `Clear Runtime User Assignments` when intentionally resetting controller ownership, such as returning to the title screen or rebuilding the local multiplayer lobby.

