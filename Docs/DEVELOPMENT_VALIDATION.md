# Development Validation

This page is for source builds and maintenance. Published hardware test results are recorded in [Compatibility](COMPATIBILITY.md).

## Builds

Build each engine/platform package separately. On Windows, use `Scripts/BuildCompatibility.ps1` with `-EngineRoot` and `-OutputRoot`. `-StrictIncludes` also checks compilation without PCH and unity builds. Each engine needs its compatible compiler and SDK.

BuildPlugin checks Editor Development, Game Development and Game Shipping compilation; it does not cook or run a sample game. Keep a host project with `-NoDeleteHostProject` when using RunUAT directly. Build Mac packages on a Mac.

## Automation

Run `DualSense.` development automation tests in the host project. `Scripts/TestCompatibility.ps1` accepts `-EngineRoot`, `-HostProject` and `-ReportRoot` and checks the exported test report. Tests cover protocol layout, identity parsing, device selection, settings sources, metadata and parts of haptics routing. They do not establish physical-controller compatibility.

## Regression Checks

After behavior changes, repeat relevant hardware checks in PIE and a packaged game:

- Standard and supplemental input, held-input release, USB/Bluetooth handoff and reconnect order.
- Independent users and effects with the available controllers; record exact models and count.
- All affected output effects, touch/motion/status reads and unavailable-data flags.
- PCM creation, settings changes, silence/resume, endpoint recovery and teardown.
- Settings persistence in editor and packaged runtime.
- Higher platform-user IDs across PIE restart, join flow and viewport focus.

UE 5.4–5.5 uses the submix-driven synchronous endpoint callback; UE 5.6+ uses the asynchronous path. Changes affecting audio scheduling need checks on both paths.

Record engine patch, OS version, architecture, connection, controller model and runtime mode with results. Keep untested combinations explicitly unvalidated. Use separate test projects or copies of sample assets when retaining older-engine compatibility.
