# Blueprint API

SenseCore exposes gameplay-facing controller features through `UDualSenseBlueprintLibrary`. Player-targeted nodes require a local `PlayerController`. On Windows, device selection follows its platform user. macOS uses native input and has primary-device/single-controller fallbacks; see [Platform Notes](PLATFORM_NOTES.md).

## Shared Behavior

- Output nodes act on local hardware. In networked games, call them on the owning client.
- Null/non-local player targets or an unavailable subsystem/device cause player-targeted calls to fail. Read output fields only after a successful read.
- Most lighting, audio-control and trigger setters return `true` after updating cached state and attempting a HID write. Write failures are not propagated by these setters; `true` does not confirm that the physical effect occurred.
- Vibration propagates HID-write failure when a write is attempted. While active PCM owns the actuators, it can return `true` without applying rumble; the request is not queued.
- Trigger validation varies by effect. The tables below describe clamps, rejection and stop behavior. Trigger frequency/pattern fields are controller parameters; do not assume physical units that are not stated.
- Persistent input/assignment setters change shared settings across game instances and save to config. See [Configuration](CONFIGURATION.md).

## API Index

Groups below organize this reference; they are not literal editor category names. Most output and trigger nodes are in `DualSense|Player`.

| Node | Group | Purpose |
|---|---|---|
| `Is DualSense Connected` | Devices | Checks whether a connected DualSense resolves for a local player |
| `Get Connected DualSense Devices` | Devices | Lists manager-owned DualSense-family devices |
| `Set Preferred DualSense Device` | Devices | Selects one DualSense for a local player when several are mapped |
| `Clear Preferred DualSense Device` | Devices | Clears a player's preferred controller override |
| `Get Input Device Id As Int` | Devices | Converts `FInputDeviceId` for UI/logs |
| `Get Platform User Id As Int` | Devices | Converts `FPlatformUserId` for UI/logs |
| `Is Input Device Id Valid` | Devices | Validates an input device id |
| `Is Platform User Id Valid` | Devices | Validates a platform user id |
| `Get Pressed Input Keys For Player` | Input | Checks supplied keys on a player |
| `Get Pressed DualSense Input Keys For Player` | Input | Checks standard, supplemental, and custom DualSense keys |
| `Set DualSense Gamepad Input Enabled` | Input | Enables/disables raw HID standard gamepad input publishing |
| `Is DualSense Gamepad Input Enabled` | Input | Reads raw HID gamepad publishing state |
| `Set DualSense Engine Force Feedback Enabled` | Input | Enables/disables Unreal legacy force-feedback forwarding |
| `Is DualSense Engine Force Feedback Enabled` | Input | Reads force-feedback forwarding state |
| `Set DualSense Supplemental Button Input Enabled` | Input | Sets and saves additional button publishing |
| `Is DualSense Supplemental Button Input Enabled` | Input | Reads additional button publishing state |
| `Get DualSense Connection Type` | Player | Reads USB/Bluetooth/None for a player |
| `Get DualSense Motion State` | Player | Reads raw motion sensor state |
| `Get DualSense Touch State` | Player | Reads one touchpad contact |
| `Get DualSense Battery State` | Player | Reads battery charge state |
| `Get DualSense Headset State` | Player | Reads headset and microphone state |
| `Set DualSense Vibration` | Output | Plays compatible rumble |
| `Set DualSense Lightbar Color` | Output | Sets lightbar color |
| `Set DualSense Player LEDs` | Output | Sets player LED preset |
| `Set DualSense Player LED Mask` | Output | Sets custom player LED bitmask |
| `Set DualSense Mute LED` | Output | Sets mute LED mode |
| `Set DualSense Microphone Muted` | Output | Toggles controller microphone mute |
| `Set DualSense LED Brightness` | Output | Sets LED brightness |
| `Set DualSense Headphone Volume` | Output | Sets headphone volume |
| `Set DualSense Speaker Volume` | Output | Sets speaker volume |
| `Set DualSense Microphone Volume` | Output | Sets microphone volume |
| `Set DualSense Audio Routing` | Output | Selects controller audio routing |
| `Set DualSense Vibration Attenuation` | Output | Sets rumble and trigger attenuation |
| `Set DualSense Trigger Feedback` | Triggers | Sets basic trigger resistance by zone |
| `Set DualSense Trigger Continuous Resistance` | Triggers | Sets continuous byte-position resistance |
| `Set DualSense Trigger Section Resistance` | Triggers | Sets resistance between byte positions |
| `Set DualSense Trigger Weapon` | Triggers | Sets weapon-style resistance |
| `Set DualSense Trigger Bow` | Triggers | Sets bow-style resistance and snap force |
| `Set DualSense Trigger Galloping` | Triggers | Sets alternating gallop-style trigger effect |
| `Set DualSense Trigger Machine` | Triggers | Sets machine-style trigger pattern |
| `Set DualSense Trigger Vibration` | Triggers | Sets trigger vibration by zone |
| `Set DualSense Trigger Slope Feedback` | Triggers | Sets resistance ramp by zone |
| `Set DualSense Trigger Multi Position Feedback` | Triggers | Sets ten-zone feedback strengths |
| `Set DualSense Trigger Multi Position Vibration` | Triggers | Sets ten-zone vibration strengths |
| `Set DualSense Trigger Automatic Weapon` | Triggers | Sets automatic-weapon style trigger effect |
| `Set DualSense Trigger Custom` | Triggers | Sends raw 11-byte trigger payload |
| `Stop DualSense Triggers` | Triggers | Clears one or both trigger effects |
| `Get DualSense Advanced Haptics Audio Endpoints` | Advanced Haptics | Lists USB PCM haptics endpoints |
| `Get DualSense Advanced Haptics Endpoint Status` | Advanced Haptics | Reads endpoint runtime status |
| `Get DualSense Diagnostic Report` | Diagnostics | Creates a support report |

## Shared Types

### `EDualSenseTriggerHand`

| Value | Meaning |
|---|---|
| `Left` | Apply to left trigger |
| `Right` | Apply to right trigger |
| `Both` | Apply to both triggers |

### `FDualSenseDeviceInfo`

| Field | Values | Notes |
|---|---|---|
| `DevicePath` | Session-specific string | Not guaranteed across reconnects; re-enumerate |
| `InputDeviceId` | Unreal input device id | May be invalid until Unreal registers the device |
| `PlatformUserId` | Unreal platform user id | Used for player-routed output |
| `ProductId` | USB product id | Regular DualSense and DualSense Edge are recognized |
| `ConnectionType` | `None`, `USB`, `Bluetooth` | PCM haptics require USB |
| `bIsDualSenseEdge` | `true`/`false` | True for DualSense Edge |
| `bSupportsAdaptiveTriggers` | `true`/`false` | Capability flag |
| `bSupportsRearButtons` | `true`/`false` | Declared capability for Edge rear buttons; not a hardware self-test |

## Device and Routing

### Is DualSense Connected

Checks whether a connected DualSense resolves for the target local player.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `TargetPlayer` | Input | `PlayerController` | Local controller | Null or non-local targets return `false` |
| `ReturnValue` | Output | `bool` | `true`/`false` | `true` when a connected DualSense resolves for the player; Mac fallbacks apply |

### Get Connected DualSense Devices

Returns all manager-owned DualSense-family devices.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `ContextObject` | Input | `UObject` | Object with valid world | Returns empty array if no game instance/subsystem is available |
| `ReturnValue` | Output | `FDualSenseDeviceInfo[]` | Array | See `FDualSenseDeviceInfo` |

### Set Preferred DualSense Device

Selects a preferred connected DualSense for the local player. On Windows, the device must be mapped to that platform user. On Mac, an unbound HID device may also be selected; this does not establish independent multiplayer input routing.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `TargetPlayer` | Input | `PlayerController` | Local controller | Preference is stored for this player's platform user |
| `DevicePath` | Input | `string` | Path from `Get Connected DualSense Devices` | Valid only for the current connection session |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` if target/player/device mapping is invalid |

### Clear Preferred DualSense Device

Clears the preferred DualSense override for a local player.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `TargetPlayer` | Input | `PlayerController` | Local controller | No effect for null or non-local targets |

### Get Input Device Id As Int

Converts an `FInputDeviceId` into an integer for UI or logs.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `InputDeviceId` | Input | `FInputDeviceId` | Any | Invalid ids return `INDEX_NONE` |
| `ReturnValue` | Output | `int32` | Id or `INDEX_NONE` | Display/debug helper |

### Get Platform User Id As Int

Converts an `FPlatformUserId` into an integer for UI or logs.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `PlatformUserId` | Input | `FPlatformUserId` | Any | Invalid ids return `INDEX_NONE` |
| `ReturnValue` | Output | `int32` | Id or `INDEX_NONE` | Display/debug helper |

### Is Input Device Id Valid

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `InputDeviceId` | Input | `FInputDeviceId` | Any | Passed to Unreal validity check |
| `ReturnValue` | Output | `bool` | `true`/`false` | `true` when valid |

### Is Platform User Id Valid

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `PlatformUserId` | Input | `FPlatformUserId` | Any | Passed to Unreal validity check |
| `ReturnValue` | Output | `bool` | `true`/`false` | `true` when valid |

## Input

### Get Pressed Input Keys For Player

Checks a supplied list of keys on a player.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `TargetPlayer` | Input | `PlayerController` | Any | Null targets return an empty array |
| `KeysToCheck` | Input | `FKey[]` | Valid Unreal keys | Invalid keys are ignored |
| `bIncludeAnalogValues` | Input | `bool` | `true`/`false` | When true, uses `GetInputAnalogKeyState`; nearly zero pressed digital values become `1.0` |
| `ReturnValue` | Output | `FDualSensePressedInputKey[]` | Pressed keys only | Empty if no checked keys are pressed |

`FDualSensePressedInputKey`:

| Field | Values | Notes |
|---|---|---|
| `Key` | Pressed key | Original key |
| `DisplayName` | Localized display text | From Unreal key metadata |
| `Value` | Usually `0..1` | Analog value or digital fallback `1.0` |

### Get Pressed DualSense Input Keys For Player

Checks standard gamepad keys, DualSense supplemental keys, and caller-provided additional keys.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `TargetPlayer` | Input | `PlayerController` | Any | Null targets return an empty array |
| `bIncludeStandardGamepadKeys` | Input | `bool` | `true`/`false` | Face buttons, D-pad, shoulders, trigger thresholds, sticks, Create/Options equivalents |
| `bIncludeDualSenseSupplementalKeys` | Input | `bool` | `true`/`false` | PS, mic, touchpad click, Edge function/rear buttons |
| `AdditionalKeys` | Input | `FKey[]` | Valid Unreal keys | Added to the checked set |
| `bIncludeAnalogValues` | Input | `bool` | `true`/`false` | Same behavior as `Get Pressed Input Keys For Player` |
| `ReturnValue` | Output | `FDualSensePressedInputKey[]` | Pressed keys only | Touchpad click suppresses duplicate `SpecialLeft` only in this returned list; input events are unchanged |

### Set DualSense Gamepad Input Enabled

Controls raw HID buttons, D-pad, sticks and triggers together through **Enable Raw Gamepad Input** in Project Settings > Plugins > SenseCore DualSense.

The settings object is the source of truth for input publishing and the Blueprint getter. The setter updates it and saves `bEnableRawStandardGamepadInputByDefault` in the `[/Script/DualSenseSupport.DualSenseSupportSettings]` section: project `Config/DefaultGame.ini` in the editor (including PIE), or user `Game.ini` in a packaged game. Changes persist across PIE sessions and restarts. Project Settings edits take effect on the next input tick.

Default: enabled on Windows, disabled on Mac. Supplemental buttons are controlled separately. Keep raw standard input disabled for the recommended Mac native-input setup. See [Configuration](CONFIGURATION.md) for migration keys.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `bEnabled` | Input | `bool` | `true`/`false` | Disable when another input layer owns standard buttons, sticks, triggers, and D-pad input |

### Is DualSense Gamepad Input Enabled

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `ReturnValue` | Output | `bool` | `true`/`false` | Current raw HID gamepad input publishing state |

### Set DualSense Engine Force Feedback Enabled

Controls forwarding of Unreal legacy force feedback to mapped DualSense devices.

The setter updates Project Settings and saves `bEnableEngineForceFeedbackByDefault`, using the same persistence rules as the gamepad-input setter. The getter reads the settings object directly; there is no separate console-variable override.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `bEnabled` | Input | `bool` | `true`/`false` | Disable when another system owns ordinary rumble |

### Is DualSense Engine Force Feedback Enabled

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `ReturnValue` | Output | `bool` | `true`/`false` | Current force-feedback forwarding state |

### Set / Is DualSense Supplemental Button Input Enabled

`Set DualSense Supplemental Button Input Enabled(bEnabled)` updates and saves `bEnableSupplementalButtonInputByDefault`. It controls touchpad click, PS, microphone and Edge buttons independently of standard gamepad input. `Is DualSense Supplemental Button Input Enabled` reads the current setting. Changes persist across PIE sessions and restarts, using the same save locations as the other input settings.

This switch does not control raw touch contact reads. On Mac, supplemental touchpad click can coexist with native `Gamepad Special Left`; see [Platform Notes](PLATFORM_NOTES.md).

## Player State

### Get DualSense Connection Type

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `TargetPlayer` | Input | `PlayerController` | Local controller | Uses player's platform user |
| `ReturnValue` | Output | `EDualSenseConnectionType` | `None`, `USB`, `Bluetooth` | `None` if no mapped connected DualSense exists |

### Get DualSense Motion State

Gyroscope, accelerometer, timestamp and temperature fields are raw report values. The API does not calculate controller orientation or convert these values to physical units.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `TargetPlayer` | Input | `PlayerController` | Local controller | Uses player's mapped DualSense |
| `MotionState` | Output | `FDualSenseMotionState` | Raw sensor values | Gyro/accelerometer are not converted to physical units |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` if no connected device or input report exists |

### Get DualSense Touch State

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `TargetPlayer` | Input | `PlayerController` | Local controller | Uses player's mapped DualSense |
| `TouchIndex` | Input | `int32` | `0` or `1` | Other values return `false` |
| `TouchState` | Output | `FDualSenseTouchState` | Touch contact data | Check `bActive`; `NormalizedX/Y` are clamped to `0..1` |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` if no input report exists |

### Get DualSense Battery State

`LevelPercent` is an estimate from a discrete report field. Check `Status` before interpreting a zero value as an empty battery.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `TargetPlayer` | Input | `PlayerController` | Local controller | Uses player's mapped DualSense |
| `BatteryState` | Output | `FDualSenseBatteryState` | Level/status | Status is `Unknown`, `Discharging`, `Charging`, `Full`, `NotCharging`, or `Error` |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` if no input report exists |

### Get DualSense Headset State

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `TargetPlayer` | Input | `PlayerController` | Local controller | Uses player's mapped DualSense |
| `HeadsetState` | Output | `FDualSenseHeadsetState` | Headphones/microphone flags | USB only: `bStatusAvailable` is false over Bluetooth on both platforms |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` if no input report exists |

## Output

All effects below take a local `TargetPlayer` and return `bool`. Shared Behavior defines common routing failures and success semantics; per-effect rejection rules are listed where applicable.

### Set DualSense Vibration

Requests compatible rumble for the selected local controller. Active PCM may suppress the update while returning true; see Shared Behavior.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `LeftMotor` | Input | `float` | Normalized `0..1` | Clamped and encoded to `0..255` |
| `RightMotor` | Input | `float` | Normalized `0..1` | Clamped and encoded to `0..255` |
| `Duration` | Input | `float` | Seconds; `0` means no timer | When `> 0`, schedules a stop for that physical device |

### Set DualSense Lightbar Color

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Color` | Input | `FLinearColor` | Components normally `0..1` | Multiplied by intensity, then clamped |
| `Intensity` | Input | `float` | Normalized `0..1` | Values outside range are clamped |

### Set DualSense Player LEDs

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Preset` | Input | `EDualSensePlayerLedPreset` | `Off`, `One`, `Two`, `Three`, `Four`, `All` | Encoded to controller player-indicator pattern |

### Set DualSense Player LED Mask

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `LedMask` | Input | `uint8` | `0..31` recommended | Implementation accepts `uint8`; only low 5 indicator bits are meaningful |

### Set DualSense Mute LED

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Mode` | Input | `EDualSenseMuteLedMode` | `Off`, `On`, `Pulse` | Encoded to controller mute LED field |

### Set DualSense Microphone Muted

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `bMuted` | Input | `bool` | `true`/`false` | Sets or clears the controller microphone mute bit |

### Set DualSense LED Brightness

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Brightness` | Input | `EDualSenseLedBrightness` | `High`, `Medium`, `Low` | Encoded directly from enum |

### Set DualSense Headphone Volume

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Volume` | Input | `float` | Normalized `0..1` | Clamped and encoded to `0..127` |

### Set DualSense Speaker Volume

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Volume` | Input | `float` | Normalized `0..1` | Clamped and encoded to `0..255` |

### Set DualSense Microphone Volume

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Volume` | Input | `float` | Normalized `0..1` | Clamped and encoded to `0..64` |

### Set DualSense Audio Routing

Sets controller audio-control fields. It does not create an OS playback/capture device or stream audio over Bluetooth. Volume and microphone-mute nodes likewise control controller fields.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Routing` | Input | `EDualSenseAudioRouting` | `Headphones`, `MonoHeadphones`, `Both`, `Speaker` | Encoded into controller audio control field |

### Set DualSense Vibration Attenuation

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `RumbleAttenuation` | Input | `float` | Normalized `0..1` | Clamped and encoded to `0..7` |
| `TriggerAttenuation` | Input | `float` | Normalized `0..1` | Clamped and encoded to `0..7` |

## Adaptive Triggers

### Set DualSense Trigger Feedback

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `StartPosition` | Input | `int32` | `0..9` | Values outside range are clamped |
| `Strength` | Input | `float` | Normalized `0..1` | Encoded to `0..8`; `0` stops selected trigger |

### Set DualSense Trigger Continuous Resistance

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `StartPosition` | Input | `float` | Normalized `0..1` | Encoded to byte `0..255` |
| `Strength` | Input | `float` | Normalized `0..1` | Encoded to byte `0..255`; `0` stops selected trigger |

### Set DualSense Trigger Section Resistance

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `StartPosition` | Input | `float` | Normalized `0..1` | Encoded to byte `0..255` |
| `EndPosition` | Input | `float` | Normalized `0..1` | Must encode greater than start when strength is active |
| `Strength` | Input | `float` | Normalized `0..1` | Encoded to byte `0..255`; `0` stops selected trigger |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` if active strength is requested and end is not greater than start |

### Set DualSense Trigger Weapon

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `StartPosition` | Input | `int32` | `2..7` | Values outside range are clamped |
| `EndPosition` | Input | `int32` | `StartPosition + 1..8` | Values outside range are clamped |
| `Strength` | Input | `float` | Normalized `0..1` | Encoded to `0..8`; `0` stops selected trigger |

### Set DualSense Trigger Bow

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `StartPosition` | Input | `int32` | `0..8` | Values outside range are clamped |
| `EndPosition` | Input | `int32` | `0..8`, greater than start | If start is not before end, selected trigger is stopped |
| `Strength` | Input | `float` | Normalized `0..1` | Encoded to `0..8`; `0` stops selected trigger |
| `SnapForce` | Input | `float` | Normalized `0..1` | Encoded to `0..8`; `0` stops selected trigger |
| `ReturnValue` | Output | `bool` | `true`/`false` | Calls trigger stop and returns that result for invalid inactive combinations |

### Set DualSense Trigger Galloping

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `StartPosition` | Input | `int32` | `0..8` | Values outside range are clamped |
| `EndPosition` | Input | `int32` | `0..9`, greater than start | Returns `false` if clamped start is not before end |
| `FirstFoot` | Input | `int32` | `0..6` | Values outside range are clamped |
| `SecondFoot` | Input | `int32` | `0..7`, greater than first foot | Returns `false` if clamped first foot is not before second foot |
| `Frequency` | Input | `int32` | `1..255` | Values outside range are clamped |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` for invalid start/end or foot ordering |

### Set DualSense Trigger Machine

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `StartPosition` | Input | `int32` | `0..8` | Values outside range are clamped |
| `EndPosition` | Input | `int32` | `0..9`, greater than start | Returns `false` if clamped start is not before end |
| `AmplitudeA` | Input | `float` | Normalized `0..1` | Encoded to `0..7` |
| `AmplitudeB` | Input | `float` | Normalized `0..1` | Encoded to `0..7` |
| `Frequency` | Input | `int32` | `1..255` | Values outside range are clamped |
| `Period` | Input | `int32` | `0..255` | Values outside range are clamped |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` if both amplitudes encode to `0` or start/end are invalid |

### Set DualSense Trigger Vibration

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `StartPosition` | Input | `int32` | `0..9` | Values outside range are clamped |
| `Amplitude` | Input | `float` | Normalized `0..1` | Encoded to `0..8`; `0` stops selected trigger |
| `Frequency` | Input | `int32` | `1..255` when amplitude is non-zero | Returns `false` if amplitude is active and frequency is `0` |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` for invalid active frequency or no mapped device |

### Set DualSense Trigger Slope Feedback

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `StartPosition` | Input | `int32` | `0..8` | Values outside range are clamped |
| `EndPosition` | Input | `int32` | `StartPosition + 1..9` | Values outside range are clamped |
| `StartStrength` | Input | `float` | Normalized `0..1` | Encoded to `0..8` |
| `EndStrength` | Input | `float` | Normalized `0..1` | Encoded to `0..8`; both strengths `0` stop selected trigger |

### Set DualSense Trigger Multi Position Feedback

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `ZoneStrengths` | Input | `float[]` | Exactly 10 normalized floats | Each entry maps to a trigger zone and encodes to `0..8` |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` unless exactly 10 entries are supplied |

### Set DualSense Trigger Multi Position Vibration

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `ZoneStrengths` | Input | `float[]` | Exactly 10 normalized floats | Each entry maps to a trigger zone and encodes to `0..8` |
| `Frequency` | Input | `int32` | `1..255` when any zone is active | Returns `false` if any zone is active and frequency is `0` |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` unless exactly 10 entries are supplied |

### Set DualSense Trigger Automatic Weapon

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `StartPosition` | Input | `float` | Normalized `0..1` | Encoded to byte `0..255` |
| `Amplitude` | Input | `float` | Normalized `0..1` | Encoded to byte `0..255`; `0` stops selected trigger |
| `Frequency` | Input | `int32` | `0..255` | Values outside range are clamped; implementation does not reject `0` |

### Set DualSense Trigger Custom

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) |
| `EffectBytes` | Input | `uint8[]` | Exactly 11 bytes | Raw mode byte plus ten trigger parameters |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` for any payload length other than 11 |

### Stop DualSense Triggers

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `Hand` | Input | `EDualSenseTriggerHand` | `Left`, `Right`, `Both` | Selected trigger(s) to clear |

`FirstFoot`, `SecondFoot` and `Period` are controller pattern parameters. Use the accepted ranges above and tune by feel; they are not documented as seconds, distances or forces.

## Advanced Haptics Status

### Get DualSense Advanced Haptics Audio Endpoints

Lists audio endpoints that can be used for USB PCM haptics routing.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `ReturnValue` | Output | `FDualSenseAdvancedHapticsEndpointInfo[]` | Array | Includes endpoint id, friendly name, container id, channel count, sample rate, and capability flag |

### Get DualSense Advanced Haptics Endpoint Status

Reads runtime status for an advanced haptics endpoint.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `EndpointId` | Input | `string` | Endpoint id from enumeration or endpoint settings | Empty or unknown ids return `false` |
| `Status` | Output | `FDualSenseAdvancedHapticsEndpointStatus` | Status struct | State, last error, underruns, dropped frames, queued frames |
| `ReturnValue` | Output | `bool` | `true`/`false` | `false` if no renderer/status exists for that endpoint |

Audio signal generation for advanced haptics is handled through the Unreal Audio Mixer endpoint path rather than direct Blueprint playback nodes.

## Diagnostics

### Get DualSense Diagnostic Report

Creates a text support report.

| Pin | Direction | Type | Values | Notes |
|---|---|---|---|---|
| `ContextObject` | Input | `UObject` | Object with valid world | Used to find the game instance subsystem |
| `bIncludeSensitiveIdentifiers` | Input | `bool` | `true`/`false` | Shipping builds force this to `false` |
| `ReturnValue` | Output | `string` | Report text | Includes engine/platform/build, devices, haptics endpoints, and runtime status |

## Input Device User Subsystem

SenseCore also includes `UInputDeviceUserSubsystem` for local-player join and controller assignment flows. macOS multi-controller routing is not a validated configuration for this release. See [Input Device User Subsystem](INPUT_DEVICE_USER_SUBSYSTEM.md) for setup patterns.

## Primary-Device Subsystem Helpers

`DualSenseSupportSubsystem` exposes similarly named nodes without `TargetPlayer`. They operate on its primary device and should not be substituted for player-targeted calls in multiplayer. Its connection event and all-device cleanup are described in [API Compatibility](API_STABILITY.md#lower-level-interfaces).
