# Third-party components and provenance

| Component | Source / revision | Included location / terms |
| --- | --- | --- |
| Shared HPR OpenXR code | Supplied NFSHPR VR Beta2 Update8 source v6 | `upstream/Source/src`; MIT notice in `upstream/Source/LICENSE.txt` |
| OpenXR SDK / loader | [KhronosGroup/OpenXR-SDK](https://github.com/KhronosGroup/OpenXR-SDK), `f2448a8797c85814aa892efc1ab8707900fbcc78` | `upstream/Source/third_party/OpenXR-SDK`; per-file Apache-2.0 OR MIT and other retained notices |
| MinHook | [TsudaKageyu/minhook](https://github.com/TsudaKageyu/minhook), v1.3.4, supplied local changes retained | `upstream/Source/third_party/minhook`, `upstream/Source/licenses`; BSD-style notices |
| FSR 1 | [GPUOpen-Effects/FidelityFX-FSR](https://github.com/GPUOpen-Effects/FidelityFX-FSR), `a21ffb8f6c13233ba336352bdff293894c706575` | `upstream/Source/third_party/fsr1`; MIT |
| Pacing / motion support | [Krashgamer/NFSMW2012FpsUnlock](https://github.com/Krashgamer/NFSMW2012FpsUnlock), adapted helpers | `src/mw_pacer_support`, `src/mw_motion_support`; MIT notices retained, MW span-aware smoothing is modified |
| OFXR-Bridge V438 | [djules75/OFXR-Bridge](https://github.com/djules75/OFXR-Bridge), tag `0.2.13.1`, commit `20ece84979dbda66b5e7e0aada7e7bb21c267378` | `third_party/frame-generation/OFXR-x86`; LGPL-3.0, modified for Win32 |
| FidelityFX SDK | [GPUOpen-LibrariesAndSDKs/FidelityFX-SDK](https://github.com/GPUOpen-LibrariesAndSDKs/FidelityFX-SDK), v1.1.4, `c6efa6bf7f2027b3ec94f28578bb5965eabb9e55` | `third_party/frame-generation/FidelityFX-SDK-v1.1.4`; MIT, explicit Win32 build changes |
| OpenVR / Vulkan / NVIDIA OF headers | Retained OFXR dependencies and their notices | `third_party/frame-generation/external` and `OFXR-x86/licenses`; see original notices |

The exact modified OFXR source, AMD SDK source and required API headers are included.
`third_party/frame-generation/README.md` describes the Win32 changes. AMD's build-tool
binaries are obtained separately from the pinned official checkout and are not source files.
No native DLSS frame-generation DLL, game code/assets, captured shader bytecode or memory
dumps are redistributed. The NVIDIA dependency is retained interface headers, not a driver DLL.

Corresponding OFXR source is embedded by `installer/build-source-archive.py` in every new
setup build and can be extracted with `--extract-source`. The layer remains independently
replaceable in the installed `MostWantedVR/frame-generation` directory.
