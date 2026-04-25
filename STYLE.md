# Style Guide

Style conventions for `arm-cmake-toolchains`. Covers CMake (the primary content of this repo), Markdown, YAML, JSON, and English.

---

## Table of Contents

- [General Rules](#general-rules)
- [Single Source of Truth](#single-source-of-truth)
- [CMake](#cmake)
- [Markdown](#markdown)
- [YAML](#yaml)
- [JSON](#json)
- [British English 🇬🇧](#british-english-)

---

## General Rules

| Rule | Setting |
|------|---------|
| Indentation | 4 spaces (no tabs) |
| Charset | UTF-8 |
| Final newline | Always |
| Trailing whitespace | Trim (except Markdown, where it's significant for line breaks) |
| Line endings | LF in repo (`.gitattributes` normalises on checkout) |

These rules are enforced by `.editorconfig`. Install the EditorConfig plugin for your editor:

- **VS Code:** [EditorConfig for VS Code](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)

There is no hard column limit, but aim to keep lines comfortably below ~120 characters where it doesn't hurt readability.

---

## Single Source of Truth

Avoid duplicating information across files. Each piece of information should have one canonical location.

| Information | Canonical Source |
|-------------|------------------|
| Usage and install instructions | `README.md` § Usage |
| Available toolchains and what they do | `README.md` § Available Toolchains |
| Contribution flow | `CONTRIBUTING.md` |
| Style rules | `STYLE.md` (this file) |
| Security policy | `SECURITY.md` |
| Code of Conduct | `CODE_OF_CONDUCT.md` |
| Release history | `CHANGELOG.md` (will be populated at v1.0.0) |

Reference the canonical source from elsewhere; don't restate it.

---

## CMake

The two toolchain files are the primary content of this repo. They follow modern-CMake conventions.

### Formatting

- 4-space indentation; no tabs.
- One command per line; do not chain commands on a single line.
- Lowercase command names: `set`, `if`, `endif`, `foreach`, `endforeach`, `function`, `endfunction`, `execute_process`, `message`.
  Uppercase command names (`SET`, `IF`) are deprecated — never use them.
- `endif()`, `endforeach()`, `endfunction()` etc. take **empty parentheses** — do not repeat the condition or function name inside them.
- Multi-argument commands that span multiple lines indent each continuation by 4 spaces; the closing `)` goes on its own line at the original indent.

```cmake
# Correct
if(NOT DEFINED CMAKE_C_COMPILER)
    set(CMAKE_C_COMPILER arm-none-eabi-gcc)
endif()

execute_process(COMMAND ${CMAKE_C_COMPILER} -print-sysroot
    OUTPUT_VARIABLE CMAKE_SYSROOT
    OUTPUT_STRIP_TRAILING_WHITESPACE
    COMMAND_ERROR_IS_FATAL ANY
)
```

```cmake
# Wrong
IF(NOT DEFINED CMAKE_C_COMPILER)                        # uppercase command
  SET(CMAKE_C_COMPILER arm-none-eabi-gcc)               # 2-space indent
ENDIF(NOT DEFINED CMAKE_C_COMPILER)                     # repeated condition
```

### Naming

| Item | Convention | Example |
|------|------------|---------|
| Standard CMake variables | As defined by CMake | `CMAKE_C_COMPILER`, `CMAKE_SYSROOT` |
| Project-defined cache / option variables | `SCREAMING_SNAKE_CASE` | `EMBEDDED_SOCIETY_USE_LLVM` |
| Local / loop variables | `snake_case` | `target_arch`, `tool_path` |
| Functions and macros | `snake_case` | `embedded_society_set_arch()` |

### Defaults

Every default that the toolchain sets on the consumer's behalf must be guarded by `if(NOT DEFINED ...)` so the consumer can override
it from the command line, a preset, or a cache variable:

```cmake
if(NOT DEFINED CMAKE_C_COMPILER)
    set(CMAKE_C_COMPILER arm-none-eabi-gcc)
endif()
```

This is what lets a user say `cmake -DCMAKE_C_COMPILER=/opt/arm-gnu-toolchain-14/bin/arm-none-eabi-gcc -DCMAKE_TOOLCHAIN_FILE=...` without the toolchain stomping their override.

### Quoting

- Quote string values that may contain spaces, paths, or be empty: `set(CMAKE_C_COMPILER "/path with spaces/arm-none-eabi-gcc")`.
- Bare words for known-safe identifiers and program names: `set(CMAKE_AR arm-none-eabi-gcc-ar)`.
- Always quote when interpolating user-supplied paths: `set(SYSROOT "${CMAKE_SYSROOT}")`.

### Comments

Use `#` followed by a single space. Block comments are a series of `#` lines — there is no multi-line comment syntax in CMake.

```cmake
# Detect the compiler's sysroot if the consumer didn't set it manually.
if(NOT DEFINED CMAKE_SYSROOT)
    execute_process(...)
endif()
```

The licence header at the top of every `.cmake` file uses Apache 2.0 boilerplate, hash-prefixed:

```cmake
# Copyright (C) 2026 The Embedded Society <https://github.com/embedded-society/arm-cmake-toolchains>

# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at

#     http://www.apache.org/licenses/LICENSE-2.0

# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
```

The licence header text itself is American English (`License`) — that is the registered name of the Apache 2.0 licence. Do not anglicise it.

### Scope

Toolchain files in this repo configure **compilers, ancillary tools, sysroot detection, and CMake root-path search behaviour**.
Things that belong in the consumer's `CMakeLists.txt`, not here:

- `-mcpu=...`, `-mthumb`, `-mfloat-abi=...` and other architecture-specific flags
- Linker scripts (`-T`)
- Newlib / nano / nosys spec selection
- Optimisation flags (`-O...`)
- Project-level warnings (`-Wall`, `-Werror`, …)

If you find yourself wanting to add any of those to the toolchain file, that's a strong sign the change belongs downstream.

---

## Markdown

### Headings

Use ATX-style headings with blank lines before and after:

```markdown
## Section Title

Content here.
```

### Lists

Use `-` for unordered lists and `1.` for ordered lists. Indent nested items by 4 spaces (matching the project-wide indentation).

### Code Blocks

Always specify the language (or `text` for plain text):

````markdown
```cmake
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
```
````

### Trailing Whitespace

Markdown files are exempt from trailing-whitespace trimming — two trailing spaces are sometimes used as line breaks (`<br>` equivalent).

### Links

Prefer reference-style links only when a URL is reused in many places. Inline links are fine in most cases.
Use angle brackets for bare URLs (`<https://example.com>`) so they render correctly.

---

## YAML

**Indentation.** 4 spaces for structure levels — aligned with the project-wide convention.

**List item continuation.** YAML lists continue with 2-space alignment from the dash character (this is standard YAML behaviour, not negotiable):

```yaml
updates:
    - package-ecosystem: "github-actions"
      directory: "/"
      schedule:
          interval: "weekly"
          day: "saturday"
          time: "00:00"
          timezone: "UTC"
```

The `package-ecosystem` and its sibling keys (`directory`, `schedule`) are at 6 columns: 4 (parent indent) + 2 (after the dash and space).

**Quoting.** Quote strings that look like booleans, numbers, dates, or have special characters: `"yes"`, `"00:00"`, `"weekly"`. Bare strings are fine for unambiguous identifiers.

---

## JSON

**4-space indentation.** Trailing commas are not allowed in JSON. End every file with a final newline.

```json
{
    "key": "value",
    "nested": {
        "item": 123
    }
}
```

---

## British English 🇬🇧

Use **British English** in all prose, documentation, comments, commit messages, and PR descriptions:

| American | British |
|----------|---------|
| color | colour |
| behavior | behaviour |
| organize | organise |
| organization | organisation |
| recognize | recognise |
| characterize | characterise |
| minimize | minimise |
| optimize | optimise |
| center | centre |
| license (noun) | licence |
| defense | defence |
| analyze | analyse |
| catalog | catalogue |

**Hard exception — never anglicise:**

1. **Legal documents.** The Apache 2.0 licence header text says "License" because that is the legal text.
   Same applies to any licence file, terms of service, etc. — leave them exactly as published.
2. **Proper nouns.** "Apache License", "Organization for Ethical Source", "World Health Organization", etc. are registered names; keep them as-is.
3. **Code identifiers and library APIs.** Any identifier defined upstream — CMake variable names like
   `CMAKE_FIND_ROOT_PATH_MODE_PROGRAM`, GitHub Actions inputs, etc. — keeps its original spelling.

When in doubt: prose is British, identifiers and legal text are exact-as-given.

---

*Last updated: 2026-04-25.*
