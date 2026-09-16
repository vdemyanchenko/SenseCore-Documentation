# API stability

## Stable gameplay surface

The player-targeted functions on `UDualSenseBlueprintLibrary` are the documented gameplay API. They use an Unreal local player and platform user rather than assuming a single primary controller.

Device paths are exposed through device info and endpoint routing settings for explicit multi-device tools. A device path is a session identifier and must not be saved between connections.

## Compatibility names

The plugin descriptor is named `SenseCore`, while the runtime module and reflected C++ types retain their existing `DualSenseSupport` and `DualSense` names. This preserves `/Script/DualSenseSupport` object paths and existing Blueprint references.

## Internal subsystem API

The game-instance subsystem keeps lower-level helper methods for internal routing and cleanup. Public gameplay documentation should point users to the player-targeted Blueprint library and the input-device user subsystem rather than raw device-path helpers.

## Diagnostics

The support report is safe by default and remains available in Shipping. Adding counters or status fields is non-breaking; changing HID read/write scheduling requires renewed hardware stress testing.
