# Diagnostics

SenseCore includes diagnostic tools for support, QA, and local development. The goal is to capture enough routing, controller, and endpoint state to debug an issue without asking users to expose private device identifiers by default.

## Support Report

Call `Get DualSense Diagnostic Report` from Blueprint and copy the returned string into a private support ticket or an on-screen debug widget.

The report includes:

- engine, platform, and build configuration;
- input and force-feedback policy switches;
- connected controller count, transport, product ID, and Unreal routing IDs;
- advanced-haptics endpoint formats, runtime availability, queue counters, and error state.

`Include Sensitive Identifiers` is `false` by default. Enabling it in a non-Shipping build also includes HID device paths and platform audio endpoint identifiers. Shipping builds always ignore this option.

Only include sensitive identifiers in private support requests. Do not post reports with device paths, endpoint ids, container ids, or hardware identifiers in public issue trackers, forums, screenshots, or videos.

## Log Collection

Collect logs while reproducing the issue. Useful log areas include the core controller device, input routing, input-device user subsystem, device manager, advanced haptics, and audio endpoint code.

Do not enable verbose logging permanently in a Shipping build.

For reports involving audio haptics, include the connection type, endpoint format, whether ordinary system audio reaches the controller audio endpoint, and whether underrun or dropped-frame counters continue increasing.

## Advanced Haptics Status

For reports involving USB PCM haptics, include:

| Item | Why it matters |
|---|---|
| Connection type | Advanced PCM haptics should be validated over USB |
| Endpoint format | Confirms sample rate and channel count |
| Runtime state | Shows whether the renderer is `Ready`, `Opening`, or `Recovering` |
| Queue counters | Helps identify underruns or overflow |
| Last error code | Helps identify platform audio endpoint failures |
| MetaSound routing | Confirms the audio graph is feeding the endpoint/submix |

An occasional counter change during startup is not automatically a failure. A continuously increasing underrun count during playback means the producer is not feeding audio fast enough. A continuously increasing dropped-frame count means the producer is feeding faster than the endpoint consumes.

## Support Checklist

| Item | Example |
|---|---|
| Unreal Engine version | `5.6.1` |
| OS version | `Windows 11 24H2` or `macOS 15.x` |
| Controller model | `DualSense` or `DualSense Edge` |
| Connection mode | `USB` or `Bluetooth` |
| Repro steps | Short numbered list |
| Expected result | What should have happened |
| Actual result | What happened instead |
| Diagnostic report | Output from `Get DualSense Diagnostic Report` |
| Logs | Relevant `DualSense` log lines |
| Screenshots or video | Optional, only when they clarify the issue |

## Automation Tests

Automation tests are compiled only when Unreal enables development automation tests. They are intended for development validation and are not required for runtime use of the plugin.
