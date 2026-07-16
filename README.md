# Localyzer Connect for Trados

A Trados Studio plugin that provides an sdlxliff preview for Trados Cloud and `.pxml` files, integrating with Lingoport's Localyzer platform.

## Versions

This repository maintains two parallel release lines because Trados Studio moved from 32-bit to 64-bit between major versions.

| Trados Studio | Plugin version | Branch | Architecture |
|---|---|---|---|
| 2024 (Studio 18) | 1.x | `release/trados-2024` | 32-bit (x86) |
| 2026 (Studio 19) | 2.x | `release/trados-2026` | 64-bit (x64) |

Pick the version matching your Trados Studio install. The 1.x line continues to receive maintenance for users on Trados Studio 2024.

## Installation

Download the latest `.sdlplugin` from the [Releases](https://github.com/paulfilkin/Localyzer-Connect-for-Trados/releases) page, then either:

- Double-click the `.sdlplugin` file to launch the installer, or
- Open Trados Studio's Plugin Manager and install from there

Restart Trados Studio after installation.

## Trados Studio 2026 Upgrade

Trados Studio 2026 is the first 64-bit release of Trados Studio. Plugins built for Trados Studio 2024 (32-bit) will not load in 2026, and vice versa, so a separate plugin line is required.

The 2.x line is built against Trados Studio 2026 GA. The initial 2.x work was done against the 2026 Beta SDK (`Studio19Beta`); the current release (`v2.0.0-trados2026`) is built against the GA SDK (`Studio19`, 19.0.0.3043) and has been verified to install and run in Trados Studio 2026 GA. The earlier Beta build remains available as the `v2.0.0-trados2026-beta` tag.

What changed between 1.x and 2.x:

- Target platform changed from x86 to x64
- Trados SDK references retargeted from Studio 18 to Studio 19 install location
- All HintPath references use `$(ProgramW6432)` so the build works regardless of MSBuild bitness
- Plugin deployment path updated for Studio 19
- Manifest `RequiredProduct` updated to cover Trados Studio 2026 (19.0 to 19.0.9)
- `Microsoft.Web.WebView2` bumped to match the version shipped in Studio 2026
- Modern .NET SDK resource compatibility shim added for embedded icon resources

No public API changes were required in the plugin code itself. The Trados SDK assemblies the plugin depends on remain at the same namespaces and types between Studio 18 and Studio 19.

## Building From Source

### Prerequisites

- Visual Studio 2022 (or Build Tools for Visual Studio) with the .NET desktop development workload
- Trados Studio installed locally (the build references SDK assemblies from the install folder via `HintPath`)
  - For the 1.x line: Trados Studio 2024 at the default 32-bit install location
  - For the 2.x line: Trados Studio 2026 at the default 64-bit install location (`Studio19`)
- .NET Framework 4.8 Developer Pack

### Build

```powershell
git clone https://github.com/paulfilkin/Localyzer-Connect-for-Trados.git
cd Localyzer-Connect-for-Trados
git checkout release/trados-2026   # or release/trados-2024
```

Then either open `Localyzer.Connect.sln` in Visual Studio and build, or from the command line use Visual Studio's MSBuild:

```powershell
& "<path to MSBuild.exe>" Localyzer.Connect.sln /t:Restore
& "<path to MSBuild.exe>" Localyzer.Connect.sln /p:Configuration=Release
```

Note: the `dotnet build` CLI is not supported. The Trados plugin packaging task requires the desktop-framework MSBuild assemblies bundled with Visual Studio, which the .NET SDK CLI does not ship.

The build output `.sdlplugin` will be at:

```
Localyzer.Connect\bin\Release\net48\Localyzer.Connect.sdlplugin
```

## Repository Layout

```
.
├── Guide/                          Getting-started documentation
├── Localyzer.Connect/              Plugin source
│   ├── pluginpackage.manifest.xml  Trados plugin manifest
│   ├── Properties/                 Assembly info and plugin properties
│   ├── Resources/                  Icons and HTML resources
│   ├── Views/                      View parts and ribbon controls
│   └── localyzer.connect.snk       Strong-name signing key
├── LICENSE                         Apache 2.0
├── Localyzer.Connect.sln           Solution file
└── README.md                       This file
```

## Licence

Apache License 2.0 - see [LICENSE](LICENSE).

Copyright 2025-2026 multifarious
