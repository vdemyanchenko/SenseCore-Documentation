# Platform Notes

SenseCore is a runtime Unreal Engine plugin for Windows and macOS.

## Compatibility Matrix

| Platform | Input | Output | Advanced haptics | Notes |
|---|---|---|---|---|
| Windows USB | Supported | Supported | Supported | Recommended path for full feature coverage |
| Windows Bluetooth | Supported | Limited by adapter/driver HID output support | Not supported | Bluetooth transport may differ by adapter and driver stack |
| macOS USB | Supported | Supported | Supported through the platform audio endpoint path | Test permissions and audio endpoint availability on target macOS versions |
| macOS Bluetooth | Supported through SenseCore input; native input may be incomplete | Limited by macOS HID output support | Not supported | Enable SenseCore gamepad input if native Bluetooth input is missing standard controls |

## Windows Notes

- SenseCore uses raw HID for controller input and output reports.
- USB advanced haptics use the platform audio endpoint associated with the physical controller.
- If another tool or virtual gamepad layer owns the same physical controller, disable duplicate input or force-feedback paths to avoid double events.
- For packaged builds, validate both editor and packaged runtime behavior with the exact controller connection mode you plan to support.

## macOS Notes

- macOS may publish standard gamepad input through the native gamepad layer.
- SenseCore disables raw standard gamepad input by default on Mac to reduce duplicate button and axis events while keeping supplemental DualSense buttons available.
- Unreal's native macOS Game Controller input path is the more stable standard gamepad input path:

```ini
[SystemSettings]
Slate.MacControllerPreferGCImpl=1
```

This mode can incorrectly publish the DualSense touchpad click as `Special Left` and may not publish the actual `Special Left`/Create button consistently.

- Unreal's legacy macOS controller path maps standard DualSense buttons more predictably:

```ini
[SystemSettings]
Slate.MacControllerPreferGCImpl=0
```

This mode can be less stable when SenseCore is also connected to the controller through raw HID. For Bluetooth input, enabling SenseCore's gamepad input path with `Set DualSense Gamepad Input Enabled` may be required.

- USB is recommended for full output and advanced haptics validation.

## Connection Modes

| Connection | Recommended for |
|---|---|
| USB | Full feature set, advanced haptics, development validation, capture videos |
| Bluetooth | Standard gameplay input, supplemental buttons, battery checks, casual play |

## Unsupported Platforms

Linux, consoles, Android, iOS, and visionOS are not included in the current plugin allow list. Console support would require platform-holder SDK integration and is outside this plugin's scope.
