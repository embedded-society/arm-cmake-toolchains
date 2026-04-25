## Description

Brief description of the changes in this PR.

## Related Issue

Fixes #(issue number)

## Type of Change

- [ ] Bug fix (non-breaking change that fixes an issue)
- [ ] New feature (non-breaking change that adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update
- [ ] Refactoring (no functional changes)
- [ ] CI/CD changes

## Testing

- [ ] I have integrated the modified toolchain file into a sample CMake project and confirmed it configures and builds against an ARM Cortex target
- [ ] I have tested with at least one of: `arm-none-eabi-gcc`, LLVM `clang` with the `arm-none-eabi` target
- [ ] CMake configures cleanly (no errors, no unexpected warnings)

**Toolchains and host environment used:**

- Host OS: <!-- e.g. Ubuntu 24.04, Windows 11, macOS 14 -->
- CMake version: <!-- `cmake --version` -->
- Generator: <!-- Ninja, Make, etc. -->
- ARM toolchain(s) and version(s): <!-- e.g. arm-none-eabi-gcc 13.3.1; LLVM 19.1.0 -->
- ARM Cortex variant(s) tested: <!-- e.g. Cortex-M0, Cortex-R5, Cortex-A53 -->

## Code Quality

- [ ] No trailing whitespace; final newline present (`.editorconfig`)
- [ ] British spelling used in comments and documentation (see [STYLE.md](../STYLE.md))
- [ ] CMake style matches the conventions in [STYLE.md](../STYLE.md)

## Additional Notes

Any additional context reviewers should know.
