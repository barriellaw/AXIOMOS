# Prototype image pipeline validation

**Status:** Runbook draft; no Axiom image has been built or booted. This document defines the evidence to collect during Roadmap Stage 1. It does not claim the Fedora 45 pipeline or the QEMU profile has been validated.

## Scope

Build a project-customized Fedora 45 Kinoite x86-64 image through the accepted treefile → OSTree commit → Image Builder path, then boot it to an interactive Plasma session. This is a prototype validation, not a production release process. Secure Boot and Axiom-owned Secure Boot signing are out of scope for this gate (DEC-036).

The build must run on a supported Fedora build host with the Fedora 45 composition inputs and Image Builder tooling available. Do not treat a different host or a newer Fedora release as equivalent without recording it as a deviation. Image Builder's supported image types and OSTree inputs vary by distro and tool version, so confirm the actual installed tool's capabilities rather than copying a command from another release's documentation. See the [Image Builder usage guide](https://osbuild.org/docs/developer-guide/projects/image-builder/usage/) and Fedora's [Atomic Desktop Image Builder change](https://fedoraproject.org/wiki/Changes/BuildAtomicDesktopsWithImageBuilder).

## Preflight

Before composing, record:

- Build host distribution, release, architecture, kernel, and available disk space.
- Source revision and treefile path; list any uncommitted changes.
- Fedora repository configuration and the repository metadata identity used by the compose.
- Image Builder and related compose-tool versions, and the advertised Fedora 45 image types and OSTree options.
- Available QEMU, firmware, and image-inspection tools on the validation host.

Stop and record a blocker if the selected Fedora 45 tooling cannot compose the treefile, retain or identify its resolved package inputs, or pass the resulting OSTree commit into an x86-64 qcow2 build. Do not substitute another image type without recording the deviation and its effect on the gate.

## Build and input evidence

For each attempt, record the exact commands and their exit status. Preserve logs and relevant configuration with the report. Capture at least:

- Source revision, treefile, and configuration digest.
- Repository URLs and metadata identity, resolved RPM NEVRAs, and package-signing key fingerprints.
- Compose and Image Builder versions, configuration, and any inputs that could not be pinned or retained.
- OSTree commit checksum and qcow2 SHA-256 digest, if those artifacts are produced.
- Failures, retries, manual interventions, and differences between runs.

Retain metadata and package inputs where practical, as required by DEC-039. These records establish traceable prototype inputs; they do not establish reproducible output. Do not claim reproducibility without the independent matching rebuild specified by DEC-041 and DEC-026.

## Fixed QEMU baseline

Use one x86-64 VM profile for the baseline report (DEC-038 and DEC-040):

| Setting | Baseline |
|---|---|
| Machine | Q35 |
| CPU | host passthrough, 4 vCPUs |
| Memory | 8192 MiB |
| Disk | 40 GiB qcow2, VirtIO block device |
| Firmware | UEFI with Secure Boot disabled |
| Display | VirtIO VGA, local display window |
| Network | QEMU user-mode networking, VirtIO network device |
| Boot state | Fresh VM state for first boot; record any later persistent state separately |

These device selections make the accepted resource profile concrete for testing. Record the QEMU version, exact firmware files, full launch command, and any host-specific firmware setup. If Fedora 45's generated image cannot boot with this profile, report the failure and the smallest changed setting; do not silently redefine the baseline. Optional higher-resource experiments must be reported separately.

## Boot acceptance evidence

The boot gate passes only when the built image starts in the baseline profile and reaches an interactive KDE Plasma session that identifies the Axiom build. Record:

1. Image filename, size, SHA-256 digest, and OSTree checksum.
2. Exact VM launch command, QEMU version, firmware identity, and any deviations.
3. Whether firmware, bootloader, and graphical login complete; include relevant logs for failures.
4. A screenshot showing the interactive session and build identity.
5. Fedora deployment identity and confirmation that the expected KDE/Flatpak components are present.
6. Known limitations, manual interventions, and whether the VM was started from a clean state.

This gate does not evaluate the newcomer update/rollback interface or the full application-installation contract; those have later acceptance steps in the roadmap and DEC-037.

## Build report template

Copy this section into a dated report for every validation attempt:

```text
Date and operator:
Result: not run / blocked / failed / passed
Build host (distribution, release, architecture, kernel):
Source revision and treefile digest:
Repository metadata identity:
Resolved RPM NEVRAs and signing-key fingerprints:
Compose and Image Builder versions/configuration:
Build commands and exit status:
OSTree checksum:
qcow2 filename, size, SHA-256:
QEMU version and exact launch command:
UEFI firmware identity:
Baseline deviations:
Boot outcome and build identity shown:
Logs and screenshot locations:
Inputs not pinned or retained:
Failures, retries, and manual steps:
Repeat-build comparison (if performed; do not infer reproducibility):
Known limitations:
```
