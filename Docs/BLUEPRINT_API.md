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

## Player State Nodes

| Node | Purpose |
|---|---|
| `Get DualSense Connection Type` | Returns USB, Bluetooth, or None for the target player |
| `Get DualSense Motion State` | Reads raw gyro, accelerometer, timestamp, and temperature values |
| `Get DualSense Touch State` | Reads one touch point from the touchpad |
| `Get DualSense Battery State` | Reads battery percentage and charge status |
| `Get DualSense Headset State` | Reads headset/microphone status when available |

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

## Advanced Haptics Status Nodes

| Node | Purpose |
|---|---|
| `Get DualSense Advanced Haptics Audio Endpoints` | Lists audio endpoints that can be used for USB PCM haptics routing |
| `Get DualSense Advanced Haptics Endpoint Status` | Reads runtime renderer status for an endpoint |

Audio signal generation for advanced haptics is handled through the Unreal Audio Mixer endpoint path rather than direct Blueprint playback nodes.

## Input Device User Subsystem

SenseCore also includes `UInputDeviceUserSubsystem` for local multiplayer and controller assignment flows that the default engine input path does not cover well. See [Input Device User Subsystem](INPUT_DEVICE_USER_SUBSYSTEM.md) for setup patterns.
