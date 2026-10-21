# Most Wanted VR

![Most Wanted VR](MWLogobQ.png)

OpenXR VR mod for **Need for Speed Most Wanted (2012)**. Created by **sqofer**.
This repository contains the renderer, English Windows launcher, single-EXE installer,
modified Win32 frame-generation layer, and build/test sources for version **0.2**.

## Features

- Stereo D3D11 rendering with tracked headset views and headset-based visibility culling.
- Automatic driver-seat positioning when switching vehicles; manual seat adjustments.
- Stable vehicle-aligned camera, steering-only driver animation, hidden helmet and vehicle glass.
- A single head-following HUD panel and fixed **1920 x 1080** desktop mirror, independent of headset resolution.
- VR corrections for sky, stars, shadows, depth, lens effects and exposure.
- Optional FSR 1 upscaling and integrated OFXR/FidelityFX intermediate-frame generation.
- Source-motion smoothing across the game's simulation cadence.
- A branded English launcher, wheel/gamepad recenter bindings and one-file setup.

## Requirements

Windows 10/11 x64, the **32-bit NFS13.exe version 1.5.0.0**, a working OpenXR runtime,
and a compatible headset. Only this tested executable is supported:

```text
SHA-256: 3AF4DC5F206D3B41D2DCF46EF8EF41575E56834AE73DE826708A2A44C44FE971
```

No game executable, game assets, captured shaders, runtime dumps or personal settings are included.
This is an unofficial mod; game names and trademarks belong to their respective owners.

## Install and use

Build the single `MostWantedVR-Setup.exe` as described in [BUILDING.md](BUILDING.md),
close the game, and select its folder in setup. Open the installed launcher, choose
your per-eye resolution, connect the headset, then use **Save and launch**.
The launcher starts the Steam version of the game. It does not change the system OpenXR runtime.

| Key | Action |
| --- | --- |
| F6 | Toggle stereo / flat presentation |
| F10 | Recenter headset tracking; an additional wheel/gamepad/keyboard binding can be assigned in the launcher |
| Backslash (`\`) | Reset manual seat offsets and pitch, preserving the headset tracking origin |
| P / L | Seat forward / back |
| `;` / `'` | Seat left / right |
| O / K | Seat up / down |

Normal gameplay disables continuous profiling and shader/capture collection.
Configuration defaults are in [config/NFSMW_OpenXR.ini](config/NFSMW_OpenXR.ini).

## Build

Install Visual Studio 2022 C++ tools, a Windows SDK and CMake 3.24+:

```powershell
powershell -ExecutionPolicy Bypass -File scripts/Build.ps1
powershell -ExecutionPolicy Bypass -File scripts/Build-FrameGeneration.ps1
powershell -ExecutionPolicy Bypass -File installer/Build.ps1 -Compiler "C:\Program Files (x86)\Inno Setup 6\ISCC.exe"
```

The first command builds the renderer, loader, launcher and runs portable tests.
Frame-generation build tools are fetched from a pinned official AMD revision on first use.
Installer packaging additionally requires Python 3 and Inno Setup 6.7+.
See [BUILDING.md](BUILDING.md) for prerequisites, outputs and optional integration tests.

## Status and limitations

This is a beta. Frame generation can improve presentation smoothness but does not
turn source rendering into native 90 FPS. Minor periodic stutter can remain.
Hardware, scene complexity, runtime scheduling and resolution affect performance.
The integrated Win32 bridge uses the tested **2x D3D11/FidelityFX** path; 3x is disabled.
Private captured-shader regression fixtures are deliberately excluded; public GPU tests
use synthetic shaders, and private-reference tests only register when explicitly supplied.
Other game executable revisions are rejected by signature/hash checks.

## Source layout

| Directory | Contents |
| --- | --- |
| `src/` | Most Wanted x86 proxy, stereo renderer, camera, motion and game integration |
| `tests/` | Public CPU, D3D11/WARP and hardware tests; optional XR probes |
| `launcher/` | .NET Framework 4.8 WPF UI and x64 controller-input bridge |
| `installer/` | Inno Setup engine, WPF bootstrapper and source packaging |
| `config/` | Portable defaults and process-local layer manifest |
| `upstream/Source/` | Required shared HPR rendering code, headers and dependencies |
| `third_party/frame-generation/` | Exact modified Win32 OFXR and FidelityFX source, headers and notices |

## Licenses and attribution

See [LICENSE.md](LICENSE.md), [THIRD_PARTY.md](THIRD_PARTY.md) and the retained
component notices. Upstream licenses retain their original scope; no new blanket
license has been assigned to the Most Wanted-specific additions or supplied artwork.

## Support the creator

The mod is free. You can support the developer by purchasing **CYBRID** on
[Steam](https://store.steampowered.com/app/1636850/CYBRID/) or
[Meta Quest](https://www.meta.com/en-gb/experiences/cybrid/24008454042113681/).

created by sqofer
