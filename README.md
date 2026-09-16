# SenseCore
### DualSense & DualSense Edge Support for Unreal Engine 5

SenseCore is an Unreal Engine plugin that provides direct access to
DualSense and DualSense Edge controller features through Blueprint and C++ APIs.

Use adaptive triggers, haptics, motion sensors, touchpad input, controller LEDs,
multi-controller routing, device information, and other DualSense-specific
features without implementing low-level HID communication yourself.

<!-- TODO:
Add the main SenseCore banner here when the final image is ready.

Example:
![SenseCore](Images/SenseCore-Banner.png)
-->

---

## Compatibility

| | Support |
|---|---|
| **Unreal Engine** | UE 5.8 |
| **Windows** | Supported |
| **macOS** | Supported |
| **DualSense** | Supported |
| **DualSense Edge** | Supported |
| **USB** | Supported |
| **Bluetooth** | Supported* |
| **Blueprint** | Supported |
| **C++** | Supported |

\* Some controller features depend on the operating system and connection type.
See [Platform Notes](Docs/PLATFORM_NOTES.md) for details.

<!-- TODO:
Before release:
- Confirm the exact UE versions you officially support.
- Decide whether to write "5.8+" or only "5.8".
- Verify every item above using the final release build.
-->

---

## Features

### Controller Input

Access standard controller input and DualSense-specific controls:

- Face buttons
- D-pad
- Analog sticks
- Analog triggers
- Touchpad click
- PS button
- Microphone button
- DualSense Edge rear buttons
- Touchpad contacts

### Adaptive Triggers

Control the resistance and behavior of the L2 and R2 adaptive triggers.

SenseCore provides multiple trigger effect modes including:

- Feedback
- Weapon
- Vibration
- Slope Feedback
- Trigger reset / Off

Trigger positions can be configured with high precision through the
Blueprint and C++ APIs.

<!-- TODO:
Add an Adaptive Trigger Blueprint screenshot.

Suggested:
Images/AdaptiveTriggers/WeaponTrigger.png
-->

### Haptics & Vibration

SenseCore supports standard controller vibration as well as advanced
USB audio-based haptics.

Advanced haptics can be integrated with Unreal Engine's Audio Mixer
and MetaSounds, allowing controller feedback to be generated from
audio signals.

Examples include:

- Surface feedback
- Weapon effects
- Impacts
- Explosions
- Procedural vibration
- Audio-driven effects

> Advanced PCM haptics are intended for supported USB connections.

[Advanced Haptics & MetaSound →](Docs/ADVANCED_HAPTICS.md)

<!-- TODO:
Check the exact filename of your Advanced Haptics documentation and
change the link above if necessary.
-->

### Motion Sensors

Read motion data directly from the controller:

- Gyroscope
- Accelerometer
- Sensor timestamp
- Sensor temperature

This can be used for gyro aiming, motion-controlled gameplay,
controller visualization, and other motion-based mechanics.

### Touchpad

Access both DualSense touch contacts independently.

Available information includes:

- Touch active state
- Contact ID
- Raw coordinates
- Normalized coordinates
- Touchpad click

This enables gestures, drawing, cursor control, camera input,
and other touch-based interactions.

### Controller Output

Control supported DualSense output features:

- Lightbar color
- Player LEDs
- Microphone mute LED
- Microphone mute state
- Compatible vibration
- Adaptive triggers

<!-- TODO:
Verify whether every item above works over both USB and Bluetooth.
Anything connection-specific should be documented in PLATFORM_NOTES.md.
-->

### Multiple Controllers & Local Multiplayer

SenseCore includes device and player-routing APIs for projects using
multiple controllers.

Features include:

- Connected DualSense device discovery
- Input Device ID access
- Platform User ID access
- Preferred controller selection
- Local-player based output routing
- Multi-device status
- Press-to-join workflows
- Auto-join workflows

This allows controller-specific effects to be sent to the correct
local player.

### Device Information

Query information about connected controllers including:

- Connection type
- Product ID
- Input Device ID
- Platform User ID
- DualSense / DualSense Edge identification
- Adaptive trigger support
- Rear button support
- Battery state
- Headset state

### Diagnostics

SenseCore provides runtime diagnostic tools to help identify
controller and configuration issues.

Available information includes:

- Connected HID devices
- Input device mappings
- Platform user mappings
- Connection state
- Advanced haptics endpoint state
- Queue statistics
- Error information
- Plugin configuration
- Support diagnostic report

<!-- TODO:
Add a screenshot of the Device Monitor / diagnostic widget when its
final UI is ready.

Make it clear whether the Device Monitor itself is included in the
distributed plugin or is only part of the demo project.
-->

---

# Getting Started

## 1. Install SenseCore

Copy the SenseCore plugin into your project's `Plugins` directory:

```text
YourProject/
└── Plugins/
    └── SenseCore/
