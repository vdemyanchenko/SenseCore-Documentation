# Diagnostics and Support

Contact [demianchenko.dev@outlook.com](mailto:demianchenko.dev@outlook.com) for plugin support.

## What to Include

- Unreal Engine version and plugin version.
- OS version; on Mac, include Apple Silicon or Intel.
- PIE or packaged game, controller models/count and USB/Bluetooth connection.
- Short reproduction steps and expected/actual behavior.
- Output from `Get DualSense Diagnostic Report` and relevant logs.
- For Mac input issues, `Slate.MacControllerPreferGCImpl` and the raw/supplemental input settings.

For PCM issues, also include the endpoint ID only if needed privately, endpoint format, source/submix routing, runtime state and whether queue counters increase during playback.

## Diagnostic Report

`Get DualSense Diagnostic Report` returns engine/platform/build information, input settings, connected controllers, routing IDs, audio endpoint formats and renderer status.

`Include Sensitive Identifiers` is false by default, omitting device paths and platform endpoint/container identifiers. Shipping builds always omit them. Enable the option only when support needs those identifiers, and share that report privately. Logs and custom status widgets can contain identifiers independently of this option; review them before public posting.

## Status Panel

Create a widget derived from `DualSenseStatusPanelWidget` and add it to the viewport to inspect controllers, input mappings, sensor values and audio endpoint state. It can build its default display without a supplied widget asset.

- `bAutoRefresh` enables periodic snapshots; `RefreshIntervalSeconds` defaults to `0.1` seconds.
- Call `RefreshStatusSnapshot` for a manual update.
- Use the exposed arrays for a custom debug UI. See `DualSenseStatusPanelWidget.h` for optional designer bindings and controller-card customization.

This is a diagnostic view. On Mac, listed devices and names do not establish correct independent player routing.

## Logs and Audio Status

Search the Unreal output log for `DualSense` and capture the portion covering reproduction. For PCM failures, call `Get DualSense Advanced Haptics Endpoint Status`; a false return means no renderer status exists for that ID, not necessarily that the controller is disconnected.

See [Advanced Haptics](ADVANCED_HAPTICS.md#status) for states and counters, or [Troubleshooting](TROUBLESHOOTING.md) for corrective steps.
