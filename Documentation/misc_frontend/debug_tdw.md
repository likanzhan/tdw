# Debug TDW

Beyond basic coding practices (breakpoints, unit tests, etc.) in your controller, here are several strategies for debugging your controller code:

### 1. Send Log Messages

```python
c.communicate({"$type": "send_log_messages"})
```

If you send this [command](../api/command_api.md), you will receive [Log Messages](../api/output_data.md) per frame. These messages will help tell you if you've made a mistake. They _won't_ tell you if the build has crashed (because in that case, the build can't send anything!)

### 2. Use the DebugController class

[`DebugController`](../python/debug_controller.md) is a subclass of [`Controller`](../python/controller.md) that will record every list of commands sent to the build. You can review these commands to reproduce bugs or identify problems.

Because `DebugController` holds all previous commands in memory, it is not recommended for long-term simulations.

See `Python/example_controllers/debug.py` for example implementation.

### 3. Read the Player Log

To locate the player log: https://docs.unity3d.com/Manual/LogFiles.html.

This will contain all messages, warnings, errors, and exceptions logged by the Editor or standalone player (the build).

#### Common Errors and Warnings

##### `Open GL Error`

These happen in Linux if you use certain model (typically lamps) or streamed scenes. 

**Fix:** Run the build in OpenGL 4.2:

```bash
./TDW.x86_64 -force-glcore42
```

The build defaults to OpenGL 4.5. OpenGL 4.2 seems to work reliably, but the build will have lower image quality.

##### `NullReferenceException`

This usually means that one or more fields in your commands is incorrect (i.e. you're sending the ID of an object that doesn't exist in the scene).

**Fix:** Proofread your commands. If problems still persist, add a GitHub Issue.

##### `WARNING: Shader Unsupported`

Harmless; it is auto-generated by Unity3D.

##### ` The referenced script on this Behaviour (Game Object '<NAME>') is missing!`

Harmless.

##### `XR: OpenVR Error!`

This error is logged when you don't have the required libraries to use VR. Unless you want to use VR, it's harmless.

##### `Desktop is 0 x 0 @ 0 Hz`

This usually occurs on Linux headless servers. It means that you haven't set up X or launched your build on a valid display. If you see this error in the log, it means that the build can't respond to any commands that you send from a controller.

**Fix:** See `bash/run_build.sh` for how to launch the build on a headless server; note that there must be a valid virtual display.

#### Common Crashes and Exceptions

##### [Linux]: Segfault

Segfaults are rare and relatively hard to debug. Below is one possible solution; if it doesn't work, please contact us for help.

**Fix:** Make sure that unzip didn't fail and that the executable (`TDW.x86_64`) is in the same directory as `TDW_Data`:

```
TDW/
....TDW_Data/
....TDW.x86_64
```

##### [Windows]: `The code execution cannot proceed because UnityPlayer.dll was not found. Reinstalling the program may fix this problem.`

**Fix:**  Make sure that the executable `TDW.exe` is in the same folder as the other unzipped files:

```
TDW/
....MonoBleedingEdge/
....TDW_Data/
....UnityCrashHandler64.exe
....UnityPlayer.dll
....TDW.exe
```

#### Common OS X Problems

See [OS X documentation](osx.md).