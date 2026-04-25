---
name: Feature Request
about: Suggest an idea for this project
title: '[FEATURE] '
labels: enhancement
assignees: ''
---

## Problem Statement

A clear and concise description of the problem you're trying to solve.

Example: "I'm frustrated when [...]" or "It would be helpful to [...]"

## Proposed Solution

A clear and concise description of what you want to happen.

If your proposal involves architecture-specific flags (`-mcpu`, `-mthumb`, `-mfloat-abi`, linker scripts, specs files, optimisation levels, …),
please first read [STYLE.md § CMake / Scope](../../STYLE.md#scope) — those typically belong in the consumer's `CMakeLists.txt`,
not in the toolchain file.

## Alternatives Considered

A description of any alternative solutions or features you've considered.

## Additional Context

Add any other context, links to upstream documentation, or examples about the feature request here.

## Checklist

- [ ] I have searched for similar feature requests
- [ ] I have considered whether the change belongs in the toolchain file or in a downstream `CMakeLists.txt`
- [ ] I am willing to help implement this feature (optional)
