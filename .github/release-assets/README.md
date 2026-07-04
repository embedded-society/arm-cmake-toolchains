# arm-cmake-toolchains __VERSION__

CMake toolchain files for bare-metal ARM Cortex development — Cortex-M, Cortex-R, and Cortex-A targets.

This archive contains just the toolchain files and their licence. The full project — documentation, issue
tracker, and contribution guide — lives on GitHub:

<https://github.com/embedded-society/arm-cmake-toolchains>

## What's in this bundle

| File | Purpose |
|------|---------|
| `arm_none_eabi_gcc.cmake` | Toolchain file for the Arm GNU Toolchain (`arm-none-eabi-gcc`). |
| `arm_none_eabi_llvm.cmake` | Toolchain file for the LLVM Embedded Toolchain for Arm (`clang`). |
| `CHANGELOG.md` | Release history. |
| `LICENCE` | Apache License 2.0. |

## Usage

Copy the toolchain file you need next to your project, then point CMake at it:

```bash
cmake -B build -DCMAKE_TOOLCHAIN_FILE=arm_none_eabi_gcc.cmake
cmake --build build
```

Architecture flags (`-mcpu`, `-mthumb`, `-mfloat-abi`, …), linker scripts, and optimisation levels belong in your
own `CMakeLists.txt`, not in the toolchain file. See the project page for the full usage and customisation guide.

## Verifying this download

This archive ships with a signed build-provenance attestation. To confirm it was produced by the project's own
release workflow and has not been tampered with, run (needs the [GitHub CLI](https://cli.github.com/)):

```bash
gh attestation verify arm-cmake-toolchains-__VERSION__.zip --repo embedded-society/arm-cmake-toolchains
```

## Contributing

This is an open project and contributions are very welcome. If you have a suggestion, spot a bug, or want another
toolchain or target supported, please open an issue or pull request — it genuinely helps:

<https://github.com/embedded-society/arm-cmake-toolchains/issues>

Thank you for using arm-cmake-toolchains! 🙏
