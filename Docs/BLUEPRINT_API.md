# Blueprint API

SenseCore exposes most gameplay features through `UDualSenseBlueprintLibrary`. Player-targeted output nodes require a local `PlayerController` so effects are routed to the DualSense assigned to that platform user.

## Device and Routing Nodes

| Node | Purpose |
|---|---|
| `Is DualSense Connected` | Returns whether the target local player has a mapped DualSense |
| `Get Connected DualSense Devices` | Returns all manager-owned DualSense-family devices |
| `Set Preferred DualSense Device` | Selects which DualSense a local player should use when several are mapped |
| `Clear Preferred DualSense Device` | Clears the player's preferred controller override |
| `Get Input Device Id As Int` | Converts `FInputDeviceId` for UI or logs |
| `Get Platform User Id As Int` | Converts `FPlatformUserId` for UI or logs |
| `Is Input Device Id Valid` | Validates an input device id |
| `Is Platform User Id Valid` | Validates a platform user id |

### Device and Routing Parameters

| Node | Parameter | Accepted values | Notes |
|---|---|---|---|
| `Is DualSense Connected` | `TargetPlayer` | Local `PlayerController` | Returns `false` for null or non-local controllers |
| `Get Connected DualSense Devices` | `ContextObject` | Object with a valid world | Returns an empty array if no game instance/subsystem is available |
| `Set Preferred DualSense Device` | `TargetPlayer` | Local `PlayerController` | Applies preference to that player's `PlatformUserId` |
| `Set Preferred DualSense Device` | `DevicePath` | Device path from `Get Connected DualSense Devices` | Valid only for the current connection session; do not save it across reconnects |
| `Clear Preferred DualSense Device` | `TargetPlayer` | Local `PlayerController` | Clears only that player's preference |
| `Get Input Device Id As Int` | `InputDeviceId` | Any `FInputDeviceId` | Returns `INDEX_NONE` for invalid ids |
| `Get Platform User Id As Int` | `PlatformUserId` | Any `FPlatformUserId` | Returns `INDEX_NONE` for invalid ids |

`FDualSenseDeviceInfo` fields:

| Field | Values | Notes |
|---|---|---|
| `DevicePath` | Session-specific string | Used by preferred-device selection and endpoint routing; changes after reconnect |
| `InputDeviceId` | Unreal input device id | May be invalid until Unreal has registered the device |
| `PlatformUserId` | Unreal platform user id | Used for player-routed output |
| `ProductId` | USB product id | Regular DualSense and DualSense Edge are recognized |
| `ConnectionType` | `None`, `USB`, `Bluetooth` | Advanced PCM haptics should be validated over USB |
| `bIsDualSenseEdge` | `true`/`false` | Indicates Edge model support |
| `bSupportsAdaptiveTriggers` | `true`/`false` | Capability flag from the device descriptor |
| `bSupportsRearButtons` | `true`/`false` | True for Edge rear button support |

## Input Query Nodes

| Node | Purpose |
|---|---|
| `Get Pressed Input Keys For Player` | Checks a supplied list of keys on a player |
| `Get Pressed DualSense Input Keys For Player` | Checks standard gamepad keys, DualSense supplemental keys, and optional extra keys |
| `Set DualSense Gamepad Input Enabled` | Enables or disables raw HID gamepad button publishing |
| `Is DualSense Gamepad Input Enabled` | Reads the raw HID gamepad button publishing state |
| `Set DualSense Engine Force Feedback Enabled` | Enables or disables Unreal legacy force-feedback forwarding |
| `Is DualSense Engine Force Feedback Enabled` | Reads the force-feedback forwarding state |

DualSense supplemental keys include PS button, mic button, touchpad click, and DualSense Edge function/rear buttons.

### Input Query Parameters

| Node | Parameter | Accepted values | Notes |
|---|---|---|---|
| `Get Pressed Input Keys For Player` | `TargetPlayer` | Any `PlayerController` | Returns an empty array for null targets |
| `Get Pressed Input Keys For Player` | `KeysToCheck` | Array of valid `FKey` values | Invalid keys are ignored |
| `Get Pressed Input Keys For Player` | `bIncludeAnalogValues` | `true`/`false` | When true, returns analog key values; nearly zero pressed digital values are reported as `1.0` |
| `Get Pressed DualSense Input Keys For Player` | `bIncludeStandardGamepadKeys` | `true`/`false` | Includes face buttons, D-pad, shoulders, triggers, sticks, Create/Options equivalents |
| `Get Pressed DualSense Input Keys For Player` | `bIncludeDualSenseSupplementalKeys` | `true`/`false` | Includes PS, mic, touchpad click, and Edge buttons |
| `Get Pressed DualSense Input Keys For Player` | `AdditionalKeys` | Array of valid `FKey` values | Added to the built-in key set |
| `Set DualSense Gamepad Input Enabled` | `bEnabled` | `true`/`false` | Controls raw HID standard button publishing; useful when another input layer owns standard gamepad input |
| `Set DualSense Engine Force Feedback Enabled` | `bEnabled` | `true`/`false` | Controls forwarding of Unreal legacy force feedback to mapped DualSense devices |

`FDualSensePressedInputKey` fields:

| Field | Values | Notes |
|---|---|---|
| `Key` | Pressed key | Original key value |
| `DisplayName` | Localized display text | From Unreal key metadata |
| `Value` | Usually `0..1` | Analog value when requested; digital pressed fallback is `1.0` |

## Player State Nodes

| Node | Purpose |
|---|---|
| `Get DualSense Connection Type` | Returns USB, Bluetooth, or None for the target player |
| `Get DualSense Motion State` | Reads raw gyro, accelerometer, timestamp, and temperature values |
| `Get DualSense Touch State` | Reads one touch point from the touchpad |
| `Get DualSense Battery State` | Reads battery percentage and charge status |
| `Get DualSense Headset State` | Reads headset/microphone status when available |

### Player State Parameters and Return Values

| Node | Parameter | Accepted values | Notes |
|---|---|---|---|
| `Get DualSense Connection Type` | `TargetPlayer` | Local `PlayerController` | Returns `None` if no mapped DualSense is connected |
| `Get DualSense Motion State` | `MotionState` | Output struct | Returns `false` if no fresh input report is available |
| `Get DualSense Touch State` | `TouchIndex` | `0` or `1` | Other values return `false` |
| `Get DualSense Touch State` | `TouchState` | Output struct | `NormalizedX` and `NormalizedY` are clamped to `0..1` |
| `Get DualSense Battery State` | `BatteryState` | Output struct | Status can be `Unknown`, `Discharging`, `Charging`, `Full`, `NotCharging`, or `Error` |
| `Get DualSense Headset State` | `HeadsetState` | Output struct | Headset status is most useful on USB; `bStatusAvailable` indicates availability |

`FDualSenseMotionState` exposes raw sensor values. SenseCore does not convert gyro or accelerometer values into physical units.

## Rumble and Output Nodes

| Node | Purpose |
|---|---|
| `Set DualSense Vibration` | Plays compatible rumble for the target player's controller |
| `Set DualSense Lightbar Color` | Sets the controller lightbar color |
| `Set DualSense Player LEDs` | Sets a preset player LED pattern |
| `Set DualSense Player LED Mask` | Sets a custom LED bitmask |
| `Set DualSense Mute LED` | Sets the mute LED mode |
| `Set DualSense Microphone Muted` | Toggles controller microphone mute state |
| `Set DualSense LED Brightness` | Sets player LED brightness |
| `Set DualSense Headphone Volume` | Sets headphone volume |
| `Set DualSense Speaker Volume` | Sets speaker volume |
| `Set DualSense Microphone Volume` | Sets microphone volume |
| `Set DualSense Audio Routing` | Selects headphone/speaker audio routing |
| `Set DualSense Vibration Attenuation` | Sets rumble and trigger attenuation |

### Rumble and Output Parameters

| Node | Parameter | Accepted values | Behavior |
|---|---|---|---|
| `Set DualSense Vibration` | `TargetPlayer` | Local `PlayerController` | Routes to the DualSense mapped to that player's platform user |
| `Set DualSense Vibration` | `LeftMotor`, `RightMotor` | Normalized `0..1` | Values are clamped and encoded to `0..255` |
| `Set DualSense Vibration` | `Duration` | Seconds; `0` means no timer | When `Duration > 0`, the plugin schedules a stop for that physical device |
| `Set DualSense Lightbar Color` | `Color` | `FLinearColor` components normally `0..1` | Components are multiplied by intensity and clamped |
| `Set DualSense Lightbar Color` | `Intensity` | Normalized `0..1` | Values outside range are clamped |
| `Set DualSense Player LEDs` | `Preset` | `Off`, `One`, `Two`, `Three`, `Four`, `All` | Uses controller player-indicator patterns |
| `Set DualSense Player LED Mask` | `LedMask` | `0..31` recommended | The current implementation accepts `uint8`; only the low 5 indicator bits are meaningful |
| `Set DualSense Mute LED` | `Mode` | `Off`, `On`, `Pulse` | Controls the mute LED indicator |
| `Set DualSense Microphone Muted` | `bMuted` | `true`/`false` | Sets or clears the controller microphone mute bit |
| `Set DualSense LED Brightness` | `Brightness` | `High`, `Medium`, `Low` | Encoded directly from the enum |
| `Set DualSense Headphone Volume` | `Volume` | Normalized `0..1` | Clamped and encoded to `0..127` |
| `Set DualSense Speaker Volume` | `Volume` | Normalized `0..1` | Clamped and encoded to `0..255` |
| `Set DualSense Microphone Volume` | `Volume` | Normalized `0..1` | Clamped and encoded to `0..64` |
| `Set DualSense Audio Routing` | `Routing` | `Headphones`, `MonoHeadphones`, `Both`, `Speaker` | Encoded into the controller audio control field |
| `Set DualSense Vibration Attenuation` | `RumbleAttenuation`, `TriggerAttenuation` | Normalized `0..1` | Each is clamped and encoded to a 3-bit value `0..7` |

## Adaptive Trigger Nodes

| Node | Purpose |
|---|---|
| `Set DualSense Trigger Feedback` | Sets basic trigger resistance from a start position |
| `Set DualSense Trigger Continuous Resistance` | Applies continuous resistance from a normalized start position |
| `Set DualSense Trigger Section Resistance` | Applies resistance between normalized start and end positions |
| `Set DualSense Trigger Weapon` | Weapon-like resistance between two trigger zones |
| `Set DualSense Trigger Bow` | Bow-like resistance with snap force |
| `Set DualSense Trigger Galloping` | Alternating gallop-style trigger effect |
| `Set DualSense Trigger Machine` | Machine-style trigger vibration pattern |
| `Set DualSense Trigger Vibration` | Trigger vibration from a start position |
| `Set DualSense Trigger Slope Feedback` | Linear resistance ramp between two positions |
| `Set DualSense Trigger Multi Position Feedback` | Zone-based resistance array |
| `Set DualSense Trigger Multi Position Vibration` | Zone-based vibration array |
| `Set DualSense Trigger Automatic Weapon` | Automatic-weapon style trigger effect |
| `Set DualSense Trigger Custom` | Sends a custom trigger payload |
| `Stop DualSense Triggers` | Clears one or both trigger effects |

For integer trigger zone parameters, valid positions are normally `0..9`. Strength and amplitude values are normalized floats unless the node's parameter name says otherwise.

### Adaptive Trigger Shared Parameters

| Parameter | Accepted values | Notes |
|---|---|---|
| `TargetPlayer` | Local `PlayerController` | Required for all player-targeted trigger nodes |
| `Hand` | `Left`, `Right`, `Both` | Applies the effect to one or both triggers |
| Normalized strength/amplitude values | `0..1` | Most trigger APIs clamp to this range before encoding |
| Zone positions | Usually `0..9` | Some APIs clamp to narrower ranges to match DualSense protocol constraints |
| `Frequency` | Usually `1..255` for active vibration | Some APIs accept `0` only when no amplitude/strength is active |

### Adaptive Trigger Parameter Details

| Node | Parameter | Accepted values | Invalid/edge behavior |
|---|---|---|---|
| `Set DualSense Trigger Feedback` | `StartPosition` | Integer `0..9` | Values outside range are clamped |
| `Set DualSense Trigger Feedback` | `Strength` | Normalized `0..1` | Encoded to `0..8`; `0` stops the selected trigger |
| `Set DualSense Trigger Continuous Resistance` | `StartPosition` | Normalized `0..1` | Encoded to byte `0..255` |
| `Set DualSense Trigger Continuous Resistance` | `Strength` | Normalized `0..1` | Encoded to byte `0..255`; `0` stops the selected trigger |
| `Set DualSense Trigger Section Resistance` | `StartPosition`, `EndPosition` | Normalized `0..1` | Encoded to bytes `0..255`; returns `false` if active strength is requested and encoded end is not greater than start |
| `Set DualSense Trigger Section Resistance` | `Strength` | Normalized `0..1` | Encoded to byte `0..255`; `0` stops the selected trigger |
| `Set DualSense Trigger Weapon` | `StartPosition` | Integer `2..7` | Values outside range are clamped |
| `Set DualSense Trigger Weapon` | `EndPosition` | Integer `StartPosition + 1..8` | Values outside range are clamped |
| `Set DualSense Trigger Weapon` | `Strength` | Normalized `0..1` | Encoded to `0..8`; `0` stops the selected trigger |
| `Set DualSense Trigger Bow` | `StartPosition`, `EndPosition` | Integers `0..8` with `StartPosition < EndPosition` | If strength/snap force encode to `0`, or start is not before end, the selected trigger is stopped |
| `Set DualSense Trigger Bow` | `Strength`, `SnapForce` | Normalized `0..1` | Encoded to `0..8` |
| `Set DualSense Trigger Galloping` | `StartPosition` | Integer `0..8` | Values outside range are clamped |
| `Set DualSense Trigger Galloping` | `EndPosition` | Integer `0..9` and greater than start | Returns `false` if clamped start is not before clamped end |
| `Set DualSense Trigger Galloping` | `FirstFoot` | Integer `0..6` | Values outside range are clamped |
| `Set DualSense Trigger Galloping` | `SecondFoot` | Integer `0..7` and greater than first foot | Returns `false` if clamped first foot is not before second foot |
| `Set DualSense Trigger Galloping` | `Frequency` | Integer `1..255` | Values outside range are clamped |
| `Set DualSense Trigger Machine` | `StartPosition` | Integer `0..8` | Values outside range are clamped |
| `Set DualSense Trigger Machine` | `EndPosition` | Integer `0..9` and greater than start | Returns `false` if clamped start is not before clamped end |
| `Set DualSense Trigger Machine` | `AmplitudeA`, `AmplitudeB` | Normalized `0..1` | Each is encoded to `0..7`; returns `false` if both encode to `0` |
| `Set DualSense Trigger Machine` | `Frequency` | Integer `1..255` | Values outside range are clamped |
| `Set DualSense Trigger Machine` | `Period` | Integer `0..255` | Values outside range are clamped |
| `Set DualSense Trigger Vibration` | `StartPosition` | Integer `0..9` | Values outside range are clamped |
| `Set DualSense Trigger Vibration` | `Amplitude` | Normalized `0..1` | Encoded to `0..8`; `0` stops the selected trigger |
| `Set DualSense Trigger Vibration` | `Frequency` | Integer `1..255` when amplitude is non-zero | Returns `false` if amplitude is active and frequency is `0` |
| `Set DualSense Trigger Slope Feedback` | `StartPosition` | Integer `0..8` | Values outside range are clamped |
| `Set DualSense Trigger Slope Feedback` | `EndPosition` | Integer `StartPosition + 1..9` | Values outside range are clamped |
| `Set DualSense Trigger Slope Feedback` | `StartStrength`, `EndStrength` | Normalized `0..1` | Encoded to `0..8`; both `0` stops the selected trigger |
| `Set DualSense Trigger Multi Position Feedback` | `ZoneStrengths` | Exactly 10 normalized floats | Returns `false` unless the array has exactly 10 entries; each value is encoded to `0..8` |
| `Set DualSense Trigger Multi Position Vibration` | `ZoneStrengths` | Exactly 10 normalized floats | Returns `false` unless the array has exactly 10 entries; each value is encoded to `0..8` |
| `Set DualSense Trigger Multi Position Vibration` | `Frequency` | Integer `1..255` when any zone is active | Returns `false` if any zone is active and frequency is `0` |
| `Set DualSense Trigger Automatic Weapon` | `StartPosition` | Normalized `0..1` | Encoded to byte `0..255` |
| `Set DualSense Trigger Automatic Weapon` | `Amplitude` | Normalized `0..1` | Encoded to byte `0..255`; `0` stops the selected trigger |
| `Set DualSense Trigger Automatic Weapon` | `Frequency` | Integer `0..255` | Values outside range are clamped; the implementation does not reject `0` |
| `Set DualSense Trigger Custom` | `EffectBytes` | Exactly 11 bytes | Returns `false` for any other length |
| `Stop DualSense Triggers` | `Hand` | `Left`, `Right`, `Both` | Clears the selected trigger effect |

I am least confident about the user-facing physical meaning of `FirstFoot`, `SecondFoot`, `Period`, and the exact tactile semantics of the vendor trigger modes beyond the ranges enforced by code. The accepted values above are based on the current implementation's clamps and validation.

## Advanced Haptics Status Nodes

| Node | Purpose |
|---|---|
| `Get DualSense Advanced Haptics Audio Endpoints` | Lists audio endpoints that can be used for USB PCM haptics routing |
| `Get DualSense Advanced Haptics Endpoint Status` | Reads runtime renderer status for an endpoint |

Audio signal generation for advanced haptics is handled through the Unreal Audio Mixer endpoint path rather than direct Blueprint playback nodes.

### Advanced Haptics Status Parameters

| Node | Parameter | Accepted values | Notes |
|---|---|---|---|
| `Get DualSense Advanced Haptics Audio Endpoints` | None | N/A | Returns endpoint id, friendly name, container id, channel count, sample rate, and capability flag |
| `Get DualSense Advanced Haptics Endpoint Status` | `EndpointId` | Endpoint id from enumeration or endpoint settings | Returns `false` if no renderer/status exists for that endpoint |
| `Get DualSense Advanced Haptics Endpoint Status` | `Status` | Output struct | Contains state, last error, underrun frames, dropped frames, and queued frames |

## Input Device User Subsystem

SenseCore also includes `UInputDeviceUserSubsystem` for local multiplayer and controller assignment flows that the default engine input path does not cover well. See [Input Device User Subsystem](INPUT_DEVICE_USER_SUBSYSTEM.md) for setup patterns.
