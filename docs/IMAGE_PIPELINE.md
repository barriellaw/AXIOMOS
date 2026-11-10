# Prototype image pipeline validation

**Status:** Runbook draft; no Axiom image has been built or booted. This document defines the evidence to collect during Roadmap Stage 1. It does not claim the Fedora 45 pipeline or the QEMU profile has been validated.

The initial workspace-host preflight is recorded in [validation/2026-11-07-host-preflight.md](validation/2026-11-07-host-preflight.md); it is blocked before composition because the Fedora tools are not installed on that Ubuntu host.

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

### Release-specific interface probe

The upstream interfaces are documented separately: [`rpm-ostree compose tree`](https://coreos.github.io/rpm-ostree/compose-server/) accepts a treefile and creates an OSTree commit; [Image Builder's OSTree workflow](https://osbuild.org/docs/developer-guide/projects/image-builder/usage/) accepts an OSTree URL and ref for OSTree-based image types. However, the published [Kinoite qcow2 image description](https://osbuild.org/docs/user-guide/image-descriptions/fedora-43/kinoite-qcow2/) located during planning is Fedora 43, and does not establish the Fedora 45 CLI or image-type availability. Treat the connection between the two tools on Fedora 45 as an open validation item.

On the supported build host, capture these read-only capability probes before writing a build command:

```sh
rpm -q rpm-ostree image-builder
rpm-ostree compose tree --help
image-builder list --filter distro:fedora-45 --filter type:qcow2
image-builder build --help
```

Record the output and use the discovered Fedora 45 type name and OSTree options to construct the actual command. If the Fedora 45 Kinoite qcow2 type or OSTree inputs are unavailable, stop there and record the observed output and a proposed supported alternative for review. Do not infer Fedora 45 behavior from the Fedora 43 description or from generic Image Builder examples.

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

### Linux launch template

After identifying the non-Secure-Boot UEFI code image and a matching writable variable-store template on the validation host, run the VM with an independent copy of the variable store for a clean first boot. Firmware paths differ across distributions; do not assume the paths below or reuse a changed variable store when recording a fresh-boot result.

```sh
cp "$OVMF_VARS_TEMPLATE" /tmp/axiom-uefi-vars.fd
qemu-img create -f qcow2 -F qcow2 -b "$AXIOM_IMAGE" /tmp/axiom-run.qcow2 40G
qemu-system-x86_64 \
  -machine q35 \
  -cpu host \
  -smp 4 \
  -m 8192 \
  -drive if=pflash,format=raw,readonly=on,file="$OVMF_CODE" \
  -drive if=pflash,format=raw,file=/tmp/axiom-uefi-vars.fd \
  -drive file=/tmp/axiom-run.qcow2,format=qcow2,if=virtio \
  -device virtio-vga \
  -netdev user,id=net0 \
  -device virtio-net-pci,netdev=net0 \
  -display gtk
```

Set `AXIOM_IMAGE` to the built qcow2 artifact, `OVMF_CODE` to the UEFI firmware code file, and `OVMF_VARS_TEMPLATE` to its matching, non-Secure-Boot variable-store template. The fresh qcow2 overlay gives the guest a 40 GiB virtual disk while preserving the built artifact; create a new overlay and variable store for each clean first-boot attempt. The QEMU GTK display frontend must be available on the validation host. Keep the exact expanded command and `qemu-img info` output for the overlay in the report. This is a command template only; it has not been exercised with an Axiom image, and the exact QEMU machine version and Fedora firmware pairing remain to be confirmed on the supported build/validation host.

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
Runtime overlay filename, backing image, virtual size:
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
