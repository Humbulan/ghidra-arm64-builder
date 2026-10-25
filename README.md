# ghidra-arm64-builder

Build the Ghidra native decompiler for Linux ARM64
on GitHub's free ARM64 runners.

No local toolchain. No 8 GB RAM. No 30-min build.

## Problem

Ghidra 12.1.4 ships decompiler binaries for
linux_x86_64 and win_x86_64 only. Linux ARM64
users (Raspberry Pi 5, AWS Graviton, Apple
Silicon VMs, Android/proot) get:

    executable_missing: Matching native decompiler
    for linux/arm64 was not found

Building from source needs ~8 GB RAM and 30-90 min.
Constrained devices get OOM-killed before it finishes.

## Solution

This workflow runs `buildNatives_linux_arm_64` on
`ubuntu-24.04-arm` (8 GB RAM, ARM64 native) and
uploads `decompile` as an artifact. Total: ~2 min.

## Usage

    gh workflow run build.yml
    gh run watch
    gh run download -n ghidra-linux-arm64-decompiler

## Install

    GHIDRA=/path/to/ghidra_12.1.4_PUBLIC
    D="$GHIDRA/Ghidra/Features/Decompiler/os/linux_arm_64"
    mkdir -p "$D"
    cp decompile "$D/decompile"
    chmod +x "$D/decompile"

## Why these choices

- `ubuntu-24.04-arm`: `ubuntu-latest` is x86_64.
- `ref: Ghidra_12.1.4_build`: build tag, not release.
- Gradle 8.5: Ghidra build fails on Gradle 9.
- No `init` on fetchDependencies: avoids :Doclets error.

## Related

- [NationalSecurityAgency/ghidra](https://github.com/NationalSecurityAgency/ghidra)
- [morluto/rea](https://github.com/morluto/rea)
- [Humbulan/shab-polyglot](https://github.com/Humbulan/shab-polyglot)

## License

MIT

## Note on Android / Termux

This binary is glibc-linked (`/lib/ld-linux-aarch64.so.1`).
It runs inside proot Ubuntu or any glibc Linux, but not
directly under Termux's Bionic linker. Use
`proot-distro login ubuntu` and install it in the container.
