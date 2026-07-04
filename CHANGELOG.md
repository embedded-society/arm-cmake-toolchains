# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] - 2026-07-04

Initial public release.

### Added

- `arm_none_eabi_gcc.cmake` — toolchain file for the
  [Arm GNU Toolchain](https://developer.arm.com/Tools%20and%20Software/GNU%20Toolchain)
  (`arm-none-eabi-gcc`). Locates the compiler and its ancillary tools, detects
  the sysroot via `-print-sysroot`, and configures CMake's root-path search.
- `arm_none_eabi_llvm.cmake` — toolchain file for the
  [LLVM Embedded Toolchain for Arm](https://github.com/ARM-software/LLVM-embedded-toolchain-for-Arm)
  (`clang` with the `arm-none-eabi` target). Uses `CMAKE_LINKER_TYPE` for linker
  selection and detects the sysroot via `clang --target=arm-none-eabi -print-sysroot`.
- Automatic sysroot detection and CMake root-path configuration in both toolchains,
  with respect for user-supplied `CMAKE_SYSROOT` / `CMAKE_FIND_ROOT_PATH` overrides.
- Governance and contributor documentation: `README.md`, `CONTRIBUTING.md`,
  `STYLE.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`, issue and pull-request templates.
- CI/CD: Markdown linting on pull requests and the main branch, stale-cache
  cleanup, Dependabot for GitHub Actions, and a tag-triggered release workflow
  that publishes a curated `arm-cmake-toolchains-vX.Y.Z.zip` bundle (both
  toolchain files, `CHANGELOG.md`, `LICENCE`, and a short README) as a release asset.

### Requirements

- CMake 3.29 or newer (required by the LLVM toolchain's use of `CMAKE_LINKER_TYPE`).

[1.0.0]: https://github.com/embedded-society/arm-cmake-toolchains/releases/tag/v1.0.0
