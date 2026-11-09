# Stage 1 host preflight: workspace host

**Date:** 2026-11-07 (America/New_York)  
**Result:** Blocked for image composition; QEMU-side tools are present.  
**Purpose:** Record which parts of the Stage 1 preflight can be established from the current project workspace. This is not a build or boot report.

## Host

- Distribution: Ubuntu 24.04.5 LTS (Noble), x86-64.
- Kernel: `7.0.0-42-generic`.
- Available space at the workspace filesystem: 1.8 TiB at inspection time.
- Repository revision: `c76cf0a`; the image-pipeline runbook had uncommitted edits during inspection.

## Tool availability

| Tool | Result |
|---|---|
| `rpm-ostree` | Not installed |
| `image-builder` | Not installed |
| `composer-cli` / `osbuild-composer` | Not installed |
| `ostree` | Not installed |
| `qemu-system-x86_64` | Available; QEMU 8.2.2 (Ubuntu package) |
| `qemu-img` | Available |
| OVMF firmware | Files are present under `/usr/share/ovmf` and `/usr/share/OVMF`; exact Fedora pairing is not established |

QEMU's device inventory includes Q35 and the VirtIO devices named by the runbook. This confirms only that this Ubuntu QEMU build can expose those devices. It does not validate the Fedora baseline, the generated image, or the firmware pairing.

## Gate disposition

The supported Fedora build-host checks in [`IMAGE_PIPELINE.md`](../IMAGE_PIPELINE.md) cannot run on this host because Fedora's composition and image-builder tools are absent. No Fedora 45 treefile compose, OSTree commit, qcow2 image, or QEMU boot was attempted. No image artifacts, logs, or screenshots were produced.

**Next action:** Run the release-specific interface probes on the supported Fedora host. If they pass, compose the tree, identify and retain inputs, build the qcow2, and continue with the boot acceptance evidence in the runbook. Do not count this host preflight as progress through the image-build or boot gates.
