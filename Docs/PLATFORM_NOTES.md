# Platform Notes

SenseCore is a runtime Unreal Engine plugin for Windows and macOS.

## Compatibility Matrix

| Platform | Input | Output | Advanced haptics | Notes |
|---|---|---|---|---|
| Windows USB | Supported | Supported | Supported | Recommended path for full feature coverage |
| Windows Bluetooth | Supported | Supported where the OS/device permits HID output | Not recommended | Bluetooth transport may differ by adapter and driver stack |
| macOS USB | Supported | Supported | Supported through the platform audio endpoint path | Test permissions and audio endpoint availability on target macOS versions |
| macOS Bluetooth | Supported | Supported where the OS/device permits HID output | Not recommended | Native gamepad layer may also publish standard axes |

## Windows Notes

- SenseCore uses raw HID for controller input and output reports.
- USB advanced haptics use the platform audio endpoint associated with the physical controller.
- If another tool or virtual gamepad layer owns the same physical controller, disable duplicate input or force-feedback paths to avoid double events.
- For packaged builds, validate both editor and packaged runtime behavior with the exact controller connection mode you plan to support.

## macOS Notes

- macOS may publish standard gamepad axes through the native gamepad layer.
- SenseCore disables raw analog input by default on Mac to reduce duplicate axis events while keeping supplemental DualSense buttons available.
- Prefer Unreal's legacy macOS controller path for DualSense projects by adding this to project configuration:

```ini
[SystemSettings]
Slate.MacControllerPreferGCImpl=0
```

The newer native Game Controller implementation can publish the DualSense touchpad click as `Special Left` while not publishing the actual `Special Left`/Create button consistently. With `Slate.MacControllerPreferGCImpl=0`, SenseCore's raw HID path can publish the DualSense supplemental buttons more predictably while avoiding confusing touchpad/Create overlap.

- USB is recommended for full output and advanced haptics validation.

## Connection Modes

| Connection | Recommended for |
|---|---|
| USB | Full feature set, advanced haptics, development validation, capture videos |
| Bluetooth | Standard gameplay input, supplemental buttons, battery checks, casual play |

## Unsupported Platforms

Linux, consoles, Android, iOS, and visionOS are not included in the current plugin allow list. Console support would require platform-holder SDK integration and is outside this plugin's scope.
