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

*This security policy was last updated on 2026-04-25.*
