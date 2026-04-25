# arm-cmake-toolchains

CMake toolchain files for bare-metal ARM Cortex development.

## Available Toolchains

| File | Toolchain |
|------|-----------|
| `arm_none_eabi_gcc.cmake` | ARM GNU Toolchain (GCC) |
| `arm_none_eabi_llvm.cmake` | LLVM/Clang with `arm-none-eabi` target |

Both toolchains configure all standard CMake tools (compiler, linker, objcopy, size, etc.) and set up proper sysroot detection.

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
