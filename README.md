# arm-cmake-toolchains

CMake toolchain files for bare-metal ARM Cortex development.

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
