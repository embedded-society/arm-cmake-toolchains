# Contributing to arm-cmake-toolchains

Thank you for your interest in contributing! This repository hosts CMake toolchain files for bare-metal ARM Cortex development —
Cortex-M, Cortex-R, and Cortex-A targets are all in scope. The surface is small, so the contribution flow is light,
but a few conventions help keep the toolchains broadly usable.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How to Contribute](#how-to-contribute)
    - [Reporting Bugs](#reporting-bugs)
    - [Suggesting Features](#suggesting-features)
    - [Pull Requests](#pull-requests)
- [Development Setup](#development-setup)
- [Testing Your Change](#testing-your-change)
- [Coding Standards](#coding-standards)
- [Commit Messages](#commit-messages)
- [Documentation](#documentation)

---

## Code of Conduct

This project adheres to the Contributor Covenant Code of Conduct. By participating, you are expected to uphold this code.
Please see [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) for details.

---

## How to Contribute

### Reporting Bugs

Before submitting a bug report:

1. Check the [existing issues](https://github.com/embedded-society/arm-cmake-toolchains/issues) to avoid duplicates.
2. Make sure you're using the latest version of the toolchain file from `main`.
3. Gather the details the **bug report** issue template asks for — host OS, CMake and ARM-toolchain versions,
   generator, target Cortex variant and flags, a minimal reproducing project, and the full configure / build output.

When submitting, use the **bug report** issue template; it lists every field to fill in so nothing is missed.

### Suggesting Features

We welcome suggestions for additional toolchains, new defaults, or extra `set(...)` lines that benefit a wide range of consumers. Before submitting:

1. Check [existing issues](https://github.com/embedded-society/arm-cmake-toolchains/issues) for similar ideas.
2. Consider whether the change belongs in the toolchain file itself or in your downstream project's `CMakeLists.txt`.
   [README.md § Scope](README.md#scope) is the canonical list of what a toolchain file may and may not configure.

When submitting, use the **feature request** issue template.

### Pull Requests

#### Before You Start

1. For non-trivial changes, open an issue first to discuss the approach.
2. Fork the repository.
3. Create a feature branch from `main` with a descriptive name (kebab-case), e.g.
   `fix-llvm-sysroot-detection`, `update-cmake-min-version`, `improve-objcopy-defaults`.
4. Make your changes following the [coding standards](#coding-standards).

#### PR Requirements

- [ ] The change has been tested against at least one real ARM Cortex target build (see [Testing Your Change](#testing-your-change))
- [ ] CMake configures cleanly with no errors or unexpected warnings
- [ ] No trailing whitespace, final newline present (`.editorconfig` will guide you)
- [ ] British spelling used in comments and documentation
- [ ] Style matches [STYLE.md](STYLE.md)
- [ ] [CHANGELOG.md](CHANGELOG.md) is **not** updated unless a release is being cut — it stays empty until v1.0.0

#### PR Process

1. Submit your PR against the `main` branch.
2. Fill out the PR template completely.
3. CODEOWNERS reviewers are requested automatically; address any feedback.
4. Once approved, a maintainer will merge.

---

## Development Setup

### Prerequisites

- **CMake** 3.29 or newer
- **An ARM toolchain**, at least one of:
    - [Arm GNU Toolchain](https://developer.arm.com/Tools%20and%20Software/GNU%20Toolchain) (`arm-none-eabi-gcc`)
    - [LLVM Embedded Toolchain for Arm](https://github.com/ARM-software/LLVM-embedded-toolchain-for-Arm) (`clang` with `arm-none-eabi` target)
- **A build generator** such as Ninja or Make
- A Git client

### Clone

```bash
git clone git@github.com:embedded-society/arm-cmake-toolchains.git
cd arm-cmake-toolchains
```

The repository is two `.cmake` files plus governance documents. There is no build system to run on the toolchains themselves — they are consumed by downstream CMake projects.

---

## Testing Your Change

Because a toolchain file is metadata, "testing" means using it to drive a real build. The minimum check is:

1. Create a tiny scratch project somewhere outside this repo:

    ```text
    test-toolchain/
        CMakeLists.txt
        main.c
    ```

    `CMakeLists.txt`:

    ```cmake
    cmake_minimum_required(VERSION 3.29)
    project(test_toolchain LANGUAGES C CXX)
    add_executable(test_toolchain main.c)
    target_compile_options(test_toolchain PRIVATE -mcpu=cortex-m0 -mthumb)
    target_link_options(test_toolchain PRIVATE -mcpu=cortex-m0 -mthumb --specs=nosys.specs)
    ```

    The flags above target a Cortex-M0; replace with `-mcpu=cortex-r5 -marm` for a Cortex-R target,
    `-mcpu=cortex-a53` for a Cortex-A target, etc. The toolchain itself supports every ARM Cortex
    variant the underlying compiler does — pick whichever is relevant to your change.

    `main.c`:

    ```c
    int main(void) { return 0; }
    ```

2. Configure and build using your modified toolchain:

    ```bash
    cmake -B build -G Ninja \
        -DCMAKE_TOOLCHAIN_FILE=/absolute/path/to/your/modified/arm_none_eabi_gcc.cmake
    cmake --build build
    ```

3. Verify the produced ELF can be inspected with the corresponding `arm-none-eabi-*` tools (`size`, `objdump`).

If your change targets the LLVM toolchain, repeat with `arm_none_eabi_llvm.cmake`. Where reasonable, please test against
multiple Cortex variants (e.g. one Cortex-M and one Cortex-R) and mention the matrix you covered in the PR description.

---

## Coding Standards

CMake style, Markdown style, YAML / JSON / JavaScript style, and English style are all defined in **[STYLE.md](STYLE.md)** —
it is the single source of truth for every convention in this repo. Read it before your first contribution; this guide does
not restate its rules, so that they can never drift out of sync.

---

## Commit Messages

Imperative-mood subject line, capitalised first word, no trailing period, under 72 characters. Examples from this repo's history:

```text
Update copyright year to 2026
Add Dependabot config for GitHub Actions
Clarify toolchain description for ARM development
```

If the change needs more than the subject line, leave a blank line and add a body that explains the **why**, not the **what**
— the diff already shows the what. Reference issues in the footer (`Fixes #123`).

---

## Documentation

This table is the **canonical file-purpose registry** for the repository: every documentation file owns exactly one kind of
content, and that content lives only there. When you add information, put it in the file that owns it and link from elsewhere —
never copy it. If two files would say the same thing, one of them is wrong.

| File | Owns (and nothing else) |
|------|-------------------------|
| `README.md` | Project intro, the toolchain-vs-downstream scope rule, and install / usage / customisation instructions |
| `CONTRIBUTING.md` | This file — contribution flow, dev setup, testing procedure, and this registry |
| `STYLE.md` | Every style and convention rule (CMake, Markdown, YAML, JSON, JavaScript, English) — how prose and code should look |
| `SECURITY.md` | Threat model, supply-chain integrity, and vulnerability reporting |
| `CODE_OF_CONDUCT.md` | Community code of conduct (verbatim Contributor Covenant 3.0 — do not edit) |
| `CHANGELOG.md` | Release history — reserved, populated when v1.0.0 is cut |
| `.github/PULL_REQUEST_TEMPLATE.md` | The PR checklist form (links to the rules above; states none of them) |
| `.github/ISSUE_TEMPLATE/bug_report.md` | The bug-report form and its environment-field list |
| `.github/ISSUE_TEMPLATE/feature_request.md` | The feature-request form |

STYLE.md § Single Source of Truth maps each *kind of information* to its canonical file; this table maps each *file* to the
information it owns. The two are the same principle from opposite directions — keep them consistent.

When you change behaviour visible to consumers (default tool paths, sysroot logic, etc.), update `README.md` in the same PR.

---

## Questions?

- Open a [Discussion](https://github.com/embedded-society/arm-cmake-toolchains/discussions) for questions.
- Check existing issues and discussions first.
- Be patient — maintainers are volunteers.

Thank you for contributing!
