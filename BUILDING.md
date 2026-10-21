# Building Most Wanted VR 0.2

## Toolchain

- Windows 10/11 x64.
- Visual Studio 2022 (Community, Professional or Build Tools) with **Desktop development with C++**,
  MSVC x86/x64 tools, MASM, and a Windows 10/11 SDK including `fxc.exe`.
- CMake **3.24 or later**; scripts can also find the CMake bundled with Visual Studio.
- .NET Framework **4.8 developer tools** / WPF. Launcher scripts use Framework64 `csc.exe`.
- Git with HTTPS access to GitHub, needed only to obtain official pinned AMD shader-compiler binaries.
- Python 3 and **Inno Setup 6.7+** for building the single-file installer.

Build in a writable directory. All temporary files, logs and outputs stay under the
checkout. The scripts do not install into or edit an existing game installation.
No absolute developer-workstation paths are required. The game renderer must be **Win32**;
the launcher, input bridge and setup UI are **x64**.

## Renderer and launcher

```powershell
powershell -ExecutionPolicy Bypass -File scripts/Build.ps1
```

This configures `build-x86` using `-A Win32 -DMW_ENABLE_HELMET_EXPERIMENTAL=ON`, builds
Release, runs CTest and builds the launcher. To build without tests, add `-SkipTests`.
Use `-CMake "path-to-cmake.exe"` to select another CMake executable.
Debug is supported for the renderer with `-Configuration Debug`; installer packaging uses Release.

Outputs:

- `build-x86/stereo/Release/d3d11.dll`
- `build-x86/openxr/src/loader/Release/openxr_loader.dll`
- `outputs/launcher/NFSMW2012_VR_Launcher.exe` and its `.config` / `LauncherInput.dll`

The launcher embeds the renderer hash, so rebuild it after changing the renderer.
The original HPR x64 entry-point hooks are not used in the Most Wanted DLL.

## Integrated frame generation

```powershell
powershell -ExecutionPolicy Bypass -File scripts/Build-FrameGeneration.ps1
```

All modified OFXR and FidelityFX source is included under `third_party/frame-generation`.
The script obtains the SDK's official `.exe` / `.dll` / `.lib` build tools via a sparse
checkout pinned to AMD revision `c6efa6bf7f2027b3ec94f28578bb5965eabb9e55`, then copies only
those build dependencies into the included SDK tree. Source modifications are preserved.
The downloaded binaries and generated libraries are ignored by Git and excluded from source ZIPs.
Review `scripts/Fetch-FrameGenerationTools.ps1` before running if needed.

The SDK target uses the legacy option name `DX12_X64` together with the explicit
`FFX_EXPERIMENTAL_X86=ON` Win32 override. Only the optical-flow backend is built.
The OFXR standalone tray utility and unvalidated 3x path are not packaged.

Output: `build-ofxr-x86/Release/XR_APILAYER_XRFrameBridge_diagnostic.dll`.
The historical `_diagnostic` DLL filename is an upstream integration/export name;
normal runtime logging is disabled in the packaged configuration.

## Single-EXE installer

After both builds complete:

```powershell
powershell -ExecutionPolicy Bypass -File installer/Build.ps1 -Compiler "C:\Program Files (x86)\Inno Setup 6\ISCC.exe"
```

`ISCC.exe` can also be discovered on PATH. Pass `-Python "path-to-python.exe"` if Python
is not available as `python`. The output is `outputs/release/MostWantedVR-Setup.exe`.
It contains the runtime and an extractable corresponding-source archive for the modified
LGPL bridge. Source files are not installed or loaded during gameplay.

```powershell
.\outputs\release\MostWantedVR-Setup.exe --extract-source .\OFXR-Source.zip
```

The destination ZIP must not already exist. The layer is a separately replaceable DLL
under `MostWantedVR/frame-generation`; no global layer registration is performed.
Installed runtime: `d3d11.dll` beside the game plus launcher, input bridge, loader,
three configuration files, OFXR DLL and consolidated notices under `MostWantedVR`.
Setup also creates its uninstaller/ownership record and can create a shortcut.

## Verification

```powershell
ctest --test-dir build-x86 -C Release --output-on-failure
.\outputs\launcher\NFSMW2012_VR_Launcher.exe --self-test .\logs\launcher-tests.log
.\outputs\release\MostWantedVR-Setup.exe --self-test .\logs\setup-ui-tests.log
```

Tests do not need game files, except the optional installer integration test below.
Hardware GPU tests require an available D3D11 adapter; WARP tests provide software coverage.
The renderer's private-reference shader tests only register if `MW_REFERENCE_SHADER_DIR`
is explicitly supplied at CMake configure time. No captured game shader is included here.
The full upstream OFXR experimental test suite is not represented as fully passing;
the distributed configuration is limited to the validated 2x D3D11 path.

Optional real installer install/update/uninstall test (copies your own supported executable
into `work/installer-qa`, uses a separate setup registry identity, never targets your game folder):

```powershell
powershell -ExecutionPolicy Bypass -File installer/Test.ps1 -GameExecutable "path-to-your-game\NFS13.exe"
```

## Publishing to GitHub

Upload the contents of this source folder as the repository root. Keep compiled installers
in GitHub Releases, rather than in Git history. `.gitignore` excludes build products,
downloaded tools, logs, dumps, archives and machine-specific `config/Launcher.ini`.
Check [LICENSE.md](LICENSE.md) before choosing a repository-wide license.

The manual **Windows source build** workflow in GitHub Actions builds the renderer and
launcher and runs public tests using WARP, with hardware-only tests excluded. It does not
download frame-generation tools or package a release automatically.

To create another clean source bundle after editing:

```powershell
python scripts/Pack-Source.py path-to-a-new-output-folder
```

The script writes a clean folder, ZIP, SHA-256 checksum and per-file source manifest using
explicit source roots. Build outputs, downloaded tools and personal settings are excluded.
Manifest hashes describe the exact ZIP bytes; Git checkout line-ending conversion can change them.
