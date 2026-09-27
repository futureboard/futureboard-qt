# Futureboard Qt Archive

> [!IMPORTANT]
> **Archived legacy source.** This repository preserves an early Qt-based version of Futureboard Studio from 2024 for historical and reference purposes. It is no longer the active Futureboard Studio codebase and is not maintained or supported.

Futureboard Qt is an early desktop DAW prototype that predates the current native Rust/GPUI architecture. The surviving source shows the transition from a Qt 6 / QML application toward ideas that later evolved into the modern Futureboard Studio.

For the current project, see [futureboard/futureboard-studio](https://github.com/futureboard/futureboard-studio).

## Snapshot

The latest development commits in this archive date from October-November 2024. The repository was later republished as an archive.

This codebase contains incomplete, experimental, and platform-specific code. It should be treated as a historical snapshot rather than a production-ready DAW.

## Architecture

The main application is a C++17 Qt 6 application built with CMake.

```text
Futureboard
├─ C++17 application core
├─ Qt 6
│  ├─ Qt Core / Gui
│  ├─ Qt Widgets
│  ├─ Qt Quick / QML
│  └─ Qt Quick Widgets
├─ PortAudio
├─ QML desktop interface
│  ├─ Arrangement / timeline
│  ├─ Track controls
│  ├─ Transport
│  ├─ Command palette
│  ├─ Chord circle
│  ├─ Preferences
│  └─ Early plug-in editor UI
├─ Windows audio-device discovery
└─ Standalone plug-in scanner
```

The desktop shell combines a native Qt Widgets menu bar with a `QQuickView` that loads the main QML interface.

### Audio

The main CMake project links PortAudio. The archived startup code also contains Windows-specific audio-device enumeration through the Windows MMDevice/COM APIs.

This audio layer is incomplete and does not represent the architecture used by current Futureboard Studio.

### Plug-in scanner

`applications/pluginscanner/` contains a separate C++ plug-in scanner. Its source includes early work for:

- VST2
- VST3
- CLAP
- JUCE-based plug-in inspection
- XML plug-in cache/preset data through pugixml
- Windows, macOS, and Linux search-path / architecture handling

Some code paths are incomplete or experimental.

### Web UI experiment

`webui/` is an early React 18 + Vite + TypeScript scaffold. It is not the primary interface of this Qt version and appears to have been an experimental side path.

## Repository layout

```text
.
├─ applications/
│  └─ pluginscanner/        Standalone legacy plug-in scanner
├─ resources/
│  ├─ audio/metronome/      Metronome samples
│  ├─ fonts/                Bundled UI fonts
│  ├─ icons/                Legacy UI assets
│  └─ qml/
│     ├─ desktop/            Main desktop QML interface
│     └─ mobile/             Early mobile UI experiments
├─ src/
│  ├─ core/
│  │  └─ audioengine/       Early audio/platform code
│  └─ gui/desktop/          Qt Widgets desktop shell
├─ webui/                   Experimental React/Vite scaffold
├─ CMakeLists.txt
├─ vcpkg.json
└─ buildbase.cmd
```

## Historical build notes

The checked-in build scripts are Windows-oriented.

Expected tooling includes:

- CMake 3.16 or newer
- A C++17 compiler
- Qt 6 with Core, Gui, Quick, Widgets, and QuickWidgets
- vcpkg
- PortAudio
- Windows SDK for the Windows-specific device code

The root `vcpkg.json` also lists FFTW3, SDL2, zlib, and LAME.

A typical historical Windows setup used `QTDIR` and `VCPKG_ROOT`, followed by:

```powershell
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build --config Debug
```

The old `buildbase.cmd` additionally runs `windeployqt` against the resulting executable.

The plug-in scanner has its own CMake project and dependencies, including JUCE, pugixml, and CLAP.

> Building this repository today may require dependency/version fixes. No effort is made to keep the archive buildable on current toolchains.

## What replaced this?

The current Futureboard Studio codebase moved away from this Qt/QML architecture to a native Rust/GPUI application with separate native plug-in hosting and scanning processes.

This repository is kept so the early design, experiments, and implementation history are not lost.

## License

Original Futureboard code in this repository is released under the **GNU General Public License v3.0 only (GPL-3.0-only)**. See [LICENSE](LICENSE).

Third-party code, SDK material, fonts, samples, and other bundled assets may have their own licenses and copyright holders. Those materials are **not relicensed** by the repository-level GPL license. In particular, review vendored SDKs and third-party assets before copying or redistributing them.

## Status

**Archived / unmaintained.**

No bug fixes, feature work, compatibility updates, releases, or support should be expected from this repository.
