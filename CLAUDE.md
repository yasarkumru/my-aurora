# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A bootc container image project based on [Universal Blue Aurora](https://universal-blue.org/). It builds a customized, immutable Linux OS image that can be deployed as QCOW2, RAW disk, or ISO installer.

Build pipeline: `Containerfile` → Podman image → Bootc Image Builder → bootable VM images.

## Commands

This project uses [Just](https://just.systems/) as the task runner.

```bash
just build           # Build the container image with Podman
just build-qcow2     # Convert container image to QCOW2 VM disk
just build-raw       # Convert container image to RAW disk image
just build-iso       # Build an Anaconda ISO installer
just rebuild-qcow2   # Force rebuild QCOW2 from scratch
just run-vm-qcow2    # Launch a QCOW2 VM
just spawn-vm        # Run VM via systemd-vmspawn
just lint            # Run shellcheck on all Bash scripts
just format          # Run shfmt to format Bash scripts
just clean           # Remove build artifacts
```

## Architecture

| File/Dir | Purpose |
|---|---|
| `Containerfile` | Main image definition; extends `ghcr.io/ublue-os/aurora:stable` |
| `build_files/build.sh` | Customization script mounted read-only and executed inside the container build |
| `disk_config/disk.toml` | Bootc Image Builder config for QCOW2/RAW (20 GB btrfs, no user) |
| `disk_config/iso.toml` | Bootc Image Builder config for ISO (Anaconda kickstart, creates user) |
| `.github/workflows/build.yml` | CI: builds and pushes image to `ghcr.io/yasarkumru/my-aurora` on push/PR/schedule |
| `.github/workflows/build-disk.yml` | CI: manually triggered ISO build, uploads artifact |

Image signing uses Cosign; keys are in `cosign.*` files at the repo root.

Renovate is configured to auto-merge GitHub Action pin/digest updates (`.github/renovate.json5`).
