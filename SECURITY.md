# Security Policy

## Supported Versions

| Version | Supported          |
|---------|--------------------|
| 0.x.x   | Yes (development)  |

Once we reach v1.0, we will maintain security updates for the current major version and one previous major version.

## Threat Model

The toolchain files in this repository are CMake configuration scripts. They:

- Set the location of the host's ARM cross-compiler (`arm-none-eabi-gcc`, `clang`, etc.) and ancillary tools (`ar`, `nm`, `objcopy`, …).
- Detect the compiler's sysroot via `execute_process(COMMAND ${CMAKE_C_COMPILER} -print-sysroot)`.
- Configure CMake's root-path search behaviour (`CMAKE_FIND_ROOT_PATH_MODE_*`).

A vulnerability in this context typically means one of:

1. The toolchain file causes CMake to invoke an unintended program (e.g. by accepting an attacker-controlled path that resolves to a malicious binary).
2. The toolchain file leaks information from the host environment that should remain private.
3. The toolchain file produces silently miscompiled output for a specific compiler version, in a way that is consequential to safety-critical or secure embedded systems.

If you believe you've found something matching one of those, please report it privately as described below.

### Supply-chain integrity

The threats above describe what a *consumer* risks by using this repository. The other half of the model is
protecting the repository *itself* from tampering. Because the repo is deliberately tiny — two `.cmake` files plus
governance docs and a thin CI setup — every part of it is small enough to review in full, and anything unexpected
should stand out immediately. The concrete risks:

1. **Malicious CI/CD changes.** A pull request that alters a workflow to exfiltrate secrets, escalate `permissions:`,
   pull in an unpinned or unfamiliar third-party action, or make the `github-script` cache-cleanup step do more than
   prune caches. CI runs with a token and (for some jobs) write scope, so this is the highest-value target in the repo.
2. **Unexpected files.** Any new file that isn't a toolchain file, a governance/Markdown document, or part of the
   known CI/lint tooling. Binaries, build artifacts, scripts, `curl | sh`-style installers, or vendored dependencies
   have no reason to appear here and should be treated as suspicious until explained.
3. **Fishy CMake.** A subtly altered `execute_process(...)`, an added `file(DOWNLOAD ...)` / `include(...)` of a remote
   or attacker-controlled path, or a command that runs a program other than the expected `arm-none-eabi-*` / `clang`
   tools. A change that broadens what the toolchain executes, rather than where it looks, deserves close scrutiny.

### What reviewers watch for

These invariants already hold in `main`; a change that weakens any of them is a red flag, not a routine diff:

- **Actions are pinned to full commit SHAs** (with a `# vX.Y.Z` comment), never to a mutable tag or branch.
- **Workflows use least-privilege `permissions:`** — read-only by default; write scope only where a job genuinely
  needs it (e.g. `actions: write` for cache cleanup) and never broader.
- **PR workflows restore caches but never save them**, so a fork PR cannot poison the shared cache.
- **Dependencies are pinned** in `package-lock.json` and installed with `npm ci`; version bumps arrive as reviewable
  Dependabot PRs, not as ad-hoc edits to the lockfile.
- **No new file appears without a clear reason** tied to the PR's stated purpose.
- **CMake changes stay within scope** — compiler/tool location, sysroot detection, root-path search — and introduce no
  new network access, no new external program invocation, and no new remote `include`/`DOWNLOAD`.

If you spot a merged change that violates one of these — or a PR that tries to — please report it privately as below,
even if you're not certain it's exploitable.

### Release integrity

Cutting a release is intentionally restricted, so a compromised contributor account cannot ship a malicious version:

- **Only an organisation admin can create a release.** A tag ruleset restricts creation, update, and deletion of all
  tags, with organisation admins as the sole bypass actor. Because the release workflow triggers on a pushed
  `vMAJOR.MINOR.PATCH` tag, nobody without that privilege can start a release — and the workflow's `GITHUB_TOKEN`
  cannot create the tag to trigger itself.
- **Release tags must point at a signed commit** (`required_signatures` on the same ruleset) and are immutable —
  they cannot be force-updated or deleted to retarget a published version.
- **The release asset is a plain copy of tracked files, not a compiled build.** Each release attaches a
  `arm-cmake-toolchains-vX.Y.Z.zip` bundle containing the two `.cmake` files, `CHANGELOG.md`, `LICENCE`, and a
  bundle-specific `README.md` — all copied verbatim from the signed, tagged commit by the same admin-gated workflow.
  There is no compilation step and nothing executable is generated, so the archive introduces no attack surface
  beyond the tagged source itself; consumers who prefer can ignore it and use the files at the tag directly.

See [CONTRIBUTING.md § Releasing](CONTRIBUTING.md#releasing) for the release procedure.

## Reporting a Vulnerability

**Please do NOT report security vulnerabilities through public GitHub issues.**

### How to Report

1. **Preferred:** use [GitHub Security Advisories](https://github.com/embedded-society/arm-cmake-toolchains/security/advisories/new) to report the vulnerability privately.

2. **Alternative:** email the maintainer directly at <matejg03@gmail.com>.

### What to Include

When reporting, please include:

- A clear description of the vulnerability and the conditions that trigger it.
- Steps to reproduce — including the exact toolchain version, CMake version, host OS, and any environment variables involved.
- Potential impact assessment.
- Any suggested fix or mitigation (optional but appreciated).

### Response Timeline

| Action | Timeframe |
|--------|-----------|
| Initial acknowledgement | Within 48 hours |
| Preliminary assessment | Within 1 week |
| Fix development | Depends on severity and complexity |
| Security advisory publication | After fix is available |

### What to Expect

1. **Acknowledgement.** We will acknowledge receipt of your report within 48 hours.

2. **Communication.** We will keep you informed of our progress and may ask for additional information.

3. **Credit.** Unless you prefer to remain anonymous, we will credit you in the security advisory and release notes.

4. **Disclosure.** We follow responsible disclosure practices. We ask that you give us reasonable time to address the issue before any public disclosure.

---

*This security policy was last updated on 2026-07-04.*
