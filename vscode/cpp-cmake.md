# Create new CMake project

### Extensions

```text
twxs.cmake
josetr.cmake-language-support-vscode
ms-vscode.cmake-tools
cheshirekow.cmake-format
go2sh.cmake-integration-vscode
ms-vscode.makefile-tools
```

Change `settings.json`.

### New way

CMake: Configure
CMake: Build

Optional:

- CMake: Select a Kit
- CMake: Edit User-Local CMake Kits
- CMake: Select Variant

### Old way


All  docs: <https://github.com/microsoft/vscode-cmake-tools/tree/main/docs#cmake-tools-for-visual-studio-code-documentation>

1) Create `CMakeLists.txt`:

    >Cmake: Quick Start

2) Create a `CMakePresets.json` file:
    * >Cmake: Quick Start -> Add a New Preset -> Create from Compilers
    * >CMake: Select Configure Preset
