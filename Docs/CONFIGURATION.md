# Configuration

Open **Project Settings > Plugins > SenseCore DualSense**.

## Persistent Settings

| Setting | Default | Behavior |
|---|---|---|
| Enable Raw Gamepad Input | Windows: on; Mac: off | Publishes standard buttons, D-pad, sticks and triggers from SenseCore HID input |
| Enable Supplemental Button Input | On | Publishes touchpad click, PS, microphone and Edge button events |
| Enable Engine Force Feedback | On | Forwards Unreal's legacy force feedback to DualSense; direct SenseCore output calls remain independent |
| User Assignment Mode | UE Automatic | Uses engine assignment or restores remembered runtime users |
| Legacy User Policy (UE 5.4–5.5) | Primary User (Single Player) | Windows UE 5.4–5.5 only: primary user or legacy engine mapper; reconnect devices after changing |

The first three settings have corresponding `Set DualSense ... Enabled` Blueprint nodes. `Set User Assignment Mode` belongs to `Input Device User Subsystem`. Legacy User Policy is changed in Project Settings or config.

These setters **save the shared setting**. In the editor, including PIE, they update the project's `Config/DefaultGame.ini`. Packaged games save changes to user `Game.ini`. Each setter saves its own property. Settings affect all game instances in the process and persist across PIE sessions and restarts.

Assignment policy is persistent; remembered device/user mappings are held in module memory and are discarded when the module/process ends. See [Local Multiplayer](INPUT_DEVICE_USER_SUBSYSTEM.md) for mode changes and reconnect behavior.

## Config Keys

```ini
[/Script/DualSenseSupport.DualSenseSupportSettings]
bEnableSupplementalButtonInputByDefault=True
bEnableEngineForceFeedbackByDefault=True
UserAssignmentMode=UEAutomatic
LegacyUserPolicy=PrimaryUser
```

Omit `bEnableRawStandardGamepadInputByDefault` to use the platform default. Set it to `True` for SenseCore-owned standard input or `False` for native/other input ownership. In a cross-platform project, place overrides in `Config/Windows/WindowsGame.ini` or `Config/Mac/MacGame.ini` so a Windows setting does not enable duplicate input on Mac.

Alternative enum values are `UserAssignmentMode=KeepRuntimeAssignment` and `LegacyUserPolicy=FollowEngine`. For local multiplayer on Windows UE 5.4–5.5, choose `FollowEngine` when controllers need separate users. See [Engine Differences](ENGINE_COMPATIBILITY.md).

Close the editor before editing config files manually. Project Settings changes are observed at runtime; Legacy User Policy applies on the next connection. Platform overrides take precedence over base project defaults at load time.

## Session Controls

Auto-join, press-to-join and join keys are not saved by their setters. Defaults are auto-join off, press-to-join on, and `Gamepad Face Button Bottom` as the join key. Configure them when opening your lobby or starting a game instance.

Haptics routing and amplitude belong to the [endpoint settings](ADVANCED_HAPTICS.md). Status-panel refresh and layout are widget properties. Configure stick dead zones and sensitivity through Unreal input settings or Enhanced Input modifiers.
