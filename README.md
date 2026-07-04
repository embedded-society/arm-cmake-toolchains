# arm-cmake-toolchains

CMake toolchain files for bare-metal ARM Cortex development — Cortex-M, Cortex-R, and Cortex-A targets are all in scope.
Drop a toolchain file into your project, point CMake at it, and cross-compilation for ARM works out of the box: the file
locates the compiler and its ancillary tools, detects the sysroot, and configures CMake's root-path search. Architecture
flags, linker scripts, and optimisation levels stay in your project — see [Scope](#scope) below for the boundary between
what the toolchain configures and what belongs downstream.

## Scope

Toolchain files in this repo configure **compilers, ancillary tools, sysroot detection, and CMake root-path search behaviour**.
Things that belong in the consumer's `CMakeLists.txt`, not here:

- `-mcpu=...`, `-mthumb`, `-mfloat-abi=...` and other architecture-specific flags
- Linker scripts (`-T`)
- Newlib / nano / nosys spec selection
- Optimisation flags (`-O...`)
- Project-level warnings (`-Wall`, `-Werror`, …)

If you find yourself wanting to add any of those to the toolchain file, that's a strong sign the change belongs downstream.

## Available Toolchains

| File | Toolchain |
|------|-----------|
| `arm_none_eabi_gcc.cmake` | [Arm GNU Toolchain](https://developer.arm.com/Tools%20and%20Software/GNU%20Toolchain) (`arm-none-eabi-gcc`) |
| `arm_none_eabi_llvm.cmake` | [LLVM Embedded Toolchain for Arm](https://github.com/ARM-software/LLVM-embedded-toolchain-for-Arm) (`clang` with `arm-none-eabi` target) |

Both toolchains configure all standard CMake tools (compiler, linker, objcopy, size, etc.) and set up automatic sysroot detection via the compiler's `-print-sysroot`.

> **Important — for the LLVM toolchain file.** Sysroot detection runs
> `clang --target=arm-none-eabi -print-sysroot` and expects a real path back.
> This works with the **LLVM Embedded Toolchain for Arm** (which bundles picolibc,
> multilibs, startup files, and linker scripts), but **not with stock upstream LLVM** —
> stock `clang` can target ARM Cortex but ships no sysroot for `arm-none-eabi`, and
> `-print-sysroot` returns an empty string. If you must use stock LLVM, set
> `CMAKE_SYSROOT` and `CMAKE_FIND_ROOT_PATH` manually on the CMake command line and
> the toolchain will respect your overrides.

Want another toolchain, default, or target supported? Open a feature request — see
[CONTRIBUTING.md](CONTRIBUTING.md) for how suggestions are scoped.

## Usage

Copy the toolchain file you need into your project, then pass it to CMake:

```bash
cmake -B build -DCMAKE_TOOLCHAIN_FILE=arm_none_eabi_gcc.cmake
cmake --build build
```

Or with a preset:

```json
{
    "configurePresets": [
        {
            "name": "arm-gcc",
            "toolchainFile": "${sourceDir}/arm_none_eabi_gcc.cmake"
        }
    ]
}
```

## Customisation

All tool paths can be overridden before including the toolchain file. For example, to use a specific GCC version:

```bash
cmake -B build \
    -DCMAKE_C_COMPILER=/opt/arm-gnu-toolchain-13/bin/arm-none-eabi-gcc \
    -DCMAKE_CXX_COMPILER=/opt/arm-gnu-toolchain-13/bin/arm-none-eabi-g++ \
    -DCMAKE_TOOLCHAIN_FILE=arm_none_eabi_gcc.cmake
```

## Licence

This project is licensed under the Apache License Version 2.0.  
Copyright (C) 2026 The Embedded Society <https://github.com/embedded-society/arm-cmake-toolchains>.  
See the attached [LICENCE](./LICENCE) file for more info.
