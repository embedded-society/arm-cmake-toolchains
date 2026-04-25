---
name: Bug Report
about: Report a bug to help us improve
title: '[BUG] '
labels: bug
assignees: ''
---

## Bug Description

A clear and concise description of what the bug is.

## Steps to Reproduce

1.
2.
3.

If possible, include the **minimal CMakeLists.txt** that reproduces the problem.

## Expected Behaviour

What you expected to happen.

## Actual Behaviour

What actually happened. Include the full CMake configure / build output where relevant.

## Error Messages / Logs

```text
Paste any CMake errors, generator errors, or compiler invocations here.
```

## Environment

- **Host OS:** <!-- e.g. Ubuntu 24.04, Windows 11, macOS 14 -->
- **CMake version:** <!-- `cmake --version` -->
- **Generator:** <!-- Ninja, Unix Makefiles, MSBuild, etc. -->
- **Toolchain file used:** <!-- arm_none_eabi_gcc.cmake or arm_none_eabi_llvm.cmake -->
- **ARM toolchain version:** <!-- `arm-none-eabi-gcc --version` or `clang --version` -->
- **Target ARM Cortex variant:** <!-- e.g. Cortex-M0, Cortex-M4, Cortex-R5, Cortex-A53 -->
- **Compile flags:** <!-- e.g. -mcpu=cortex-m0 -mthumb (Cortex-M); -mcpu=cortex-r5 -marm (Cortex-R); -mcpu=cortex-a53 (Cortex-A) -->
- **Repository commit / version:** <!-- short SHA or tag of arm-cmake-toolchains -->

## Additional Context

Add any other context about the problem here (e.g. CMake preset, environment variables, custom paths).
