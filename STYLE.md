# Style Guide

Style conventions for `arm-cmake-toolchains`. Covers CMake (the primary content of this repo), Markdown, YAML, JSON, JavaScript, and English.

---

## Table of Contents

- [General Rules](#general-rules)
- [Single Source of Truth](#single-source-of-truth)
- [CMake](#cmake)
- [Markdown](#markdown)
- [YAML](#yaml)
- [JSON](#json)
- [JavaScript](#javascript)
- [British English 🇬🇧](#british-english-)
- [Tooling](#tooling)

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

**Do not column-align trailing comments.** Use a single space before the comment marker (`#`, `//`), regardless of language. Padding to line up
comments across rows churns the diff whenever the longest line changes and drifts out of alignment over time.

---

## Single Source of Truth

Avoid duplicating information across files. Each piece of information has one canonical location; everywhere else links to it
rather than restating it. This table maps each *kind of information* to the file that owns it. For the reciprocal view — each
*file* and the single kind of content it owns — see [CONTRIBUTING.md § Documentation](CONTRIBUTING.md#documentation), the
canonical file-purpose registry. Keep the two consistent.

| Information | Canonical Source |
|-------------|------------------|
| Project intro and overview | `README.md` (intro) |
| Usage, install, and customisation instructions | `README.md` § Usage / § Customisation |
| Available toolchains and what they do | `README.md` § Available Toolchains |
| Contribution flow, dev setup, testing procedure | `CONTRIBUTING.md` |
| File-purpose registry (which file owns what) | `CONTRIBUTING.md` § Documentation |
| Style and convention rules (all languages + English) | `STYLE.md` (this file) |
| Toolchain-vs-downstream scope rule | `README.md` § Scope |
| Formatting rules (indent, EOL, charset) | `.editorconfig` |
| Markdown lint rules | `.markdownlint.json`, `.markdownlint-cli2.jsonc` |
| Local task shortcuts | `Taskfile.yml` |
| Pinned lint tooling versions | `package.json`, `package-lock.json` |
| Threat model and security policy | `SECURITY.md` |
| Code of Conduct | `CODE_OF_CONDUCT.md` |
| Release history | `CHANGELOG.md` (will be populated at v1.0.0) |

Reference the canonical source from elsewhere; don't restate it. When updating information, update the canonical source first.

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
IF(NOT DEFINED CMAKE_C_COMPILER)          # uppercase command
  SET(CMAKE_C_COMPILER arm-none-eabi-gcc) # 2-space indent
ENDIF(NOT DEFINED CMAKE_C_COMPILER)       # repeated condition
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

Trailing comments also use a single space before the `#` — do not pad them to line up across rows (see [General Rules](#general-rules)):

```cmake
# Correct — single space before each #
set(CMAKE_C_COMPILER arm-none-eabi-gcc) # C compiler
set(CMAKE_AR arm-none-eabi-gcc-ar) # archiver

# Wrong — padded to align the comment column
set(CMAKE_C_COMPILER arm-none-eabi-gcc)    # C compiler
set(CMAKE_AR arm-none-eabi-gcc-ar)         # archiver
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

### Linting

Markdown is the one file type in this repo with an automated linter. Rules live in `.markdownlint.json`; the file
globs and ignores live in `.markdownlint-cli2.jsonc` — so a bare invocation lints the whole repo and stays in sync with CI.

```bash
npm ci # install the pinned markdownlint-cli2 (once)
npm run lint # lint every Markdown file — fails on any violation
npm run lint:fix # auto-fix the fixable violations in place
```

`Taskfile.yml` wraps the same npm scripts (`task lint`, `task fix`) for anyone who prefers [Task](https://taskfile.dev).
Both routes call the identical tooling, so local runs and CI never disagree. See [Tooling](#tooling) for the full picture.

---

## YAML

**Indentation.** 4 spaces for structure levels — aligned with the project-wide convention.

**List item continuation.** The keys of a list item continue 2 spaces after the dash — aligned with the first
character following the dash-and-space (standard YAML behaviour, not negotiable). Deeper nesting adds a further 2 spaces per level:

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

**Column breakdown:**

| Element | Column | Explanation |
|---------|--------|-------------|
| `-` | 4 | Parent indent (4 spaces from `updates:`) |
| `package-ecosystem:`, `directory:`, `schedule:` | 6 | 2 spaces after the dash-and-space |
| `interval:`, `day:`, `time:`, `timezone:` | 8 | 2 spaces from `schedule:` (nested map) |

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

## JavaScript

### Scope

JavaScript appears only in CI helper scripts under `.github/scripts/` (e.g. `cleanup-caches.js`), invoked from
workflows via `actions/github-script`. There is no application JavaScript in this repo, and no bundler or transpiler —
scripts run on the Node.js version provided by the GitHub Actions runner. Keep them dependency-free and self-contained.

### Formatting

| Setting | Value |
|---------|-------|
| Indentation | 4 spaces (no tabs) |
| String quotes | Double quotes |
| Statement terminators | Always use semicolons |
| Final newline | Always |

### Conventions

- Export the entry point as `module.exports = async ({ github, context, core }) => { ... }` — the signature
  `actions/github-script` provides. Use `core.info` / `core.warning` / `core.error` / `core.setFailed` for output,
  not `console.log`.
- Naming: `camelCase` for functions and variables, `SCREAMING_SNAKE_CASE` for constants, `PascalCase` for classes.
- Prefer `const`; use `let` only when reassignment is genuinely needed. Never use `var`.
- Give each script a top-of-file block comment (`/** ... */`) describing what it does and any environment
  variables it reads (e.g. `INPUT_DRY_RUN`).
- British spelling in comments and log strings 🇬🇧; identifiers from the GitHub API keep their upstream spelling
  (`last_accessed_at`, `size_in_bytes`, etc.).

There is no JavaScript linter or formatter wired up; match the style of the existing scripts by hand.

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

## Tooling

The toolchain files themselves are metadata — there is nothing to compile in this repo, so most "quality tooling"
here is documentation linting and CI hygiene rather than a build. Everything below runs the same way locally and in CI.

### What is checked, and how

| Concern | Enforced by | Local command |
|---------|-------------|---------------|
| Indentation, charset, final newline, line endings | `.editorconfig` (+ EditorConfig plugin) | Editor-integrated; no command |
| Markdown correctness | `markdownlint-cli2` (pinned in `package.json`) | `npm run lint` / `task lint` |
| CMake behaves against a real target | Manual build of a scratch project | See `CONTRIBUTING.md` § Testing Your Change |
| CI itself | `.github/workflows/*.yml` | Runs on PR and on `main` |

### Pinned versions

Lint tooling is pinned in `package-lock.json`; `npm ci` installs the exact version CI uses. Do not run
`npm install` (which can drift the lockfile) — use `npm ci` so local and CI stay bit-for-bit identical.
Dependabot keeps the pins current; version bumps arrive as reviewable PRs.

### Task runner (optional convenience)

`Taskfile.yml` provides thin wrappers over the npm scripts for those who use [Task](https://taskfile.dev):

```bash
task # list available tasks
task install # npm ci (skipped when node_modules is up to date)
task lint # lint all Markdown
task fix # auto-fix fixable Markdown violations
```

Task is entirely optional — the underlying `npm run` commands are the source of truth and are what CI invokes.

---

*Last updated: 2026-07-04.*
