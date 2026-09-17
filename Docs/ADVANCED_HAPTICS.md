# Advanced Haptics and MetaSound

SenseCore supports USB PCM haptics by exposing a custom Unreal Audio Mixer endpoint for DualSense voice-coil actuator routing. This is the recommended production path for advanced haptics and MetaSound workflows.

## Requirements

| Requirement | Notes |
|---|---|
| USB connection | Advanced PCM haptics are intended for USB DualSense connections |
| Audio Mixer | The endpoint is implemented through Unreal's audio endpoint system |
| Four-channel output | The endpoint reports four channels at 48 kHz |
| Controller route | The endpoint can be selected by input device id, platform user id, controller device path, or explicit endpoint id |

Bluetooth controllers can still use regular HID input and supported output features, but USB should be used for authored PCM haptics.

## Endpoint Settings

`UDualSenseHapticsEndpointSettings` exposes these settings:

| Setting | Purpose |
|---|---|
| `InputDeviceId` | Optional Enhanced Input device id. Takes priority over platform user id when set |
| `PlatformUserId` | Optional platform user whose preferred or last-active DualSense should receive PCM |
| `ControllerDevicePath` | Optional HID device path for selecting a specific controller session |
| `EndpointId` | Optional explicit audio endpoint id |
| `MaximumAmplitude` | Safety clamp for PCM output. Default is `0.25` |
| `bMuteControllerSpeakerChannels` | Mutes normal speaker channels when routing haptics |
| `bRouteFrontChannelsToActuators` | Copies front left/right input into actuator channels 3/4 for simple MetaSound authoring |

For most projects, choose one routing policy and keep it consistent:

| Use case | Recommended setting |
|---|---|
| Single local player | `PlatformUserId` or default preferred player routing |
| Local multiplayer | `InputDeviceId` or `PlatformUserId` per player |
| Debugging a specific device | `ControllerDevicePath` or explicit `EndpointId` |
| Fixed installation setup | Explicit `EndpointId` |

## Recommended Workflow

1. Enumerate candidates with `Get DualSense Advanced Haptics Audio Endpoints`.
2. Select only an endpoint where `Can Attempt Advanced Haptics` is true.
3. Configure a `DualSenseHaptics` Endpoint Submix with the matching endpoint settings.
4. Route authored audio to that submix.
5. Observe endpoint runtime status and queue counters while testing.

When `bRouteFrontChannelsToActuators` is enabled, a conventional stereo signal can be copied into the actuator channel pair. Disable it if your MetaSound already authors all four channels explicitly.

## Reading Status

- `Ready` means the renderer is open.
- `Recovering` means the endpoint encountered an error and is attempting recovery.
- `Queue Underrun Frames` counts frames requested when no PCM was queued.
- `Dropped Frames` counts old queued frames discarded to keep latency bounded.
- `Last Error Code` contains the latest platform audio endpoint error value; zero means no current error.

An occasional counter change during startup is not automatically audible. A continuously increasing underrun count during playback means the producer is not feeding audio fast enough. A continuously increasing dropped-frame count means the producer is feeding faster than the endpoint consumes.

## Interaction with Compatible Rumble

DualSense actuators can be used either by compatible rumble or by PCM haptics. SenseCore tracks actuator route ownership so ordinary rumble does not stomp active PCM output. When switching gameplay states, prefer explicit state changes:

- Stop or fade MetaSound haptics before starting long compatible rumble.
- Use short compatible rumble bursts for legacy feedback.
- Use PCM haptics for detailed textures, engine vibration, environmental effects, and authored tactile patterns.

## Recommended Authoring Ranges

| Signal type | Suggested peak |
|---|---|
| Subtle texture | `0.03..0.08` |
| Continuous mechanical vibration | `0.08..0.16` |
| Strong short pulse | `0.16..0.25` |

Avoid continuous full-scale signals. They are uncomfortable and may mask finer tactile detail.
