# Diagnostics

## Support report

Call `Get DualSense Diagnostic Report` from Blueprint and copy the returned string into a support ticket or an on-screen debug widget. The report includes:

- engine, platform, and build configuration;
- input and force-feedback policy switches;
- connected controller count, transport, product ID, and Unreal routing IDs;
- advanced-haptics endpoint formats, runtime availability, queue counters, and errors.

`Include Sensitive Identifiers` is false by default. Enabling it in a non-Shipping build also includes HID device paths and Windows audio endpoint identifiers. Shipping builds always ignore this option.

## Log collection

Collect logs while reproducing the issue. Useful categories include the core controller, input, subsystem, device manager, advanced haptics, and audio endpoint logs. Do not enable verbose logging permanently in a Shipping build.

For reports involving audio haptics, include the connection type, endpoint format, whether ordinary Windows audio reaches the controller, and whether underrun or dropped-frame counters continue increasing.

## Advanced Haptics Status

For reports involving audio haptics, include:

| Item | Why it matters |
|---|---|
| Connection type | Advanced PCM haptics should be validated over USB |
| Endpoint format | Confirms sample rate and channel count |
| Runtime state | Shows whether the renderer is `Ready`, `Opening`, or `Recovering` |
| Queue counters | Helps identify underruns or overflow |
| MetaSound routing | Confirms the audio graph is feeding the endpoint |

Automation tests are compiled only when Unreal enables development automation tests.
