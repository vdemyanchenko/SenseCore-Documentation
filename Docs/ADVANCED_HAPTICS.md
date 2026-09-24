# Advanced Haptics and MetaSound

SenseCore sends PCM audio to DualSense actuators through the `DualSenseHaptics` Audio Mixer endpoint. This requires USB and a controller audio endpoint with at least four output channels. Bluetooth PCM haptics are not supported.

The Unreal endpoint accepts four channels at 48 kHz. Stereo authoring is supported by copying front left/right into actuator channels 3/4. See [Compatibility](COMPATIBILITY.md) for tested configurations, including the current Mac multi-controller limits.

## First Playback

Use one USB DualSense for this setup.

1. Call `Get DualSense Advanced Haptics Audio Endpoints`. Confirm a candidate has `Can Attempt Advanced Haptics = true`; this indicates a candidate format, not successful playback.
2. Create a **Sound Submix Endpoint** (`UEndpointSubmix`) asset and choose `DualSenseHaptics` as its endpoint type.
3. In its endpoint settings, leave `InputDeviceId` and `PlatformUserId` at `-1`, and leave `ControllerDevicePath` and `EndpointId` empty. Automatic selection requires exactly one enumerated candidate with advanced-haptics capability.
4. Keep `Route Front Channels To Actuators` enabled. On Windows, keep `Mute Controller Speaker Channels` enabled; this switch is not applied by the Mac renderer. Set `MaximumAmplitude` to `0.1` for the first test.
5. Create a stereo MetaSound Source with a short, low-level signal connected to its left/right audio outputs. Enable the source's base submix output and set **Base Submix** to the endpoint asset. If using submix sends instead, ensure the send is enabled and has a nonzero level.
6. Play the source through an Audio Component or your usual audio playback flow in PIE. A MetaSound graph must be running to feed the endpoint.
7. Query `Get DualSense Advanced Haptics Endpoint Status` with the enumerated endpoint ID. After playback starts, expect a renderer status; `Ready` means the renderer is open, not that a non-silent effect is being produced.

Signal path:

```text
Stereo MetaSound Source → Audio Mixer submix routing → DualSenseHaptics endpoint
                                                    → USB actuator channels 3/4
```

For a reusable game setup, stop or fade the audio source when the tactile effect ends. Use separate authored haptics signals when ordinary game audio does not produce the desired feel.

## Endpoint Settings

| Setting | Default | Behavior |
|---|---|---|
| `EndpointId` | Empty | Explicit platform audio endpoint; takes priority over controller selectors |
| `ControllerDevicePath` | Empty | Selects the endpoint associated with this current USB HID connection |
| `InputDeviceId` | `-1` | Unreal input device ID; used when explicit endpoint/path is absent |
| `PlatformUserId` | `-1` | User whose preferred or last-active DualSense is selected; used when other selectors are unset |
| `MaximumAmplitude` | `0.25` | Peak clamp; limited to `0..0.25` |
| `bMuteControllerSpeakerChannels` | `true` | Windows: silences ordinary speaker channels in this PCM stream. Ignored by the current Mac renderer |
| `bRouteFrontChannelsToActuators` | `true` | Uses front L/R input for actuator channels 3/4; disable for authored four-channel input |

Choose one selector. Precedence is explicit endpoint, controller path, input device ID, then platform user ID. If no selector is set, SenseCore selects the sole capable candidate only when exactly one candidate exists. An unresolved explicit controller selection does not fall back to an unrelated controller.

Device and user IDs are runtime values, not player-array indices. Enumerate current paths/endpoint IDs instead of relying on identifiers saved on another machine or before a reconnect.

Windows supports selecting per-player routes. Multiple physical DualSense PCM routes have not been hardware-tested. On macOS, automatic HID/audio association relies on one USB DualSense; independent multi-controller routing is not a validated configuration.

## Rumble Interaction

Recent non-silent PCM takes priority over compatible rumble. While PCM owns the actuators, rumble calls can return `true` without applying a rumble update. They are not queued for later playback. Stop or fade PCM before switching to compatible rumble, then issue the rumble effect after PCM releases the route.

Adaptive trigger effects use separate controls.

## Status

| Field | Meaning |
|---|---|
| `Disconnected` | No active connection to the endpoint |
| `Opening` | Renderer is opening the endpoint |
| `Ready` | Renderer is open |
| `Recovering` | Renderer encountered an error and is attempting recovery |
| `QueueUnderrunFrames` | Requested frames unavailable in the PCM queue |
| `DroppedFrames` | Old queued frames discarded during overflow |
| `QueuedFrames` | Current queued frame count |
| `LastErrorCode` | Platform audio error code; zero means no current error |

Windows error values originate from WASAPI; Mac values originate from CoreAudio. Include the OS and unmodified code in a support report.

Counters may change during startup or silence. During intended continuous playback, increasing underruns indicate missing queued audio; increasing dropped frames indicate queue overflow. Check source playback, routing and runtime status together.

Start with low signal levels and increase while testing the intended effect. `MaximumAmplitude` limits peaks; it does not normalize signals or guarantee a particular tactile strength.
