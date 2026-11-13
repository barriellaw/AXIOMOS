# Build host setup

**Status:** Setup specification. No Axiom image has been built on any host. Package names
and commands below are starting points to confirm on the host, not verified instructions —
`IMAGE_PIPELINE.md` requires confirming the installed tool's actual capabilities rather
than copying commands from another release's documentation.

This document exists because Stage 1 is blocked on an environment gap, not a technical
one. The recorded preflight
([2026-11-07](validation/2026-11-07-host-preflight.md)) ran on an Ubuntu workstation that
has QEMU but none of Fedora's composition tooling, so no compose, image build, or boot was
attempted. [DEC-030](DECISIONS.md#dec-030-first-release-artifact-and-build-host) names an
x86-64 Fedora host matching the selected base release as the supported build and
validation environment.

## Routes

[DEC-030](DECISIONS.md#dec-030-first-release-artifact-and-build-host) keeps unvalidated
environments unsupported, so only one route below may be used for a supported validation
report. The others may be used for experiments, which must say so.

| Route | Status | Use it when |
|---|---|---|
| 1. Dedicated Fedora 45 host — physical, or a VM dedicated to the task | **Supported** | Producing a validation report that counts toward a gate |
| 2. Fedora 45 guest on a non-Fedora workstation | Experiment | You have no Fedora machine and want to explore the pipeline |
| 3. Fedora 45 container or toolbx for the compose step only | Experiment | Probing composition behavior without provisioning a host |

A route 2 or 3 result is still worth filing as a validation report — a blocked or
experimental finding is evidence (see [`DOC_CONVENTIONS.md`](DOC_CONVENTIONS.md)). It just
may not be cited as passing a gate.

## Route 1 — dedicated Fedora 45 host (supported)

### Requirements

- x86-64 architecture, matching
  [DEC-035](DECISIONS.md#dec-035-initial-fedora-release)'s Fedora 45 base and
  [DEC-030](DECISIONS.md#dec-030-first-release-artifact-and-build-host)'s host rule.
- Root privileges. `rpm-ostree compose tree` writes an OSTree repository and manipulates
  mounts; it is not a user-level operation. Confirm the exact privilege requirement on the
  host rather than assuming `sudo` alone is sufficient.
- Disk space for three distinct things, which are easy to conflate:
  1. the OSTree repository, which accumulates one commit per compose;
  2. the RPM download and dependency-resolution cache;
  3. the qcow2 output artifact.

  The 40 GiB figure in [DEC-040](DECISIONS.md#dec-040-prototype-qemu-baseline-values) is
  the **guest's virtual disk**, not a host requirement, and a qcow2 file is sparse so it
  starts far smaller. **Do not treat any figure here as a requirement.** Record available
  space before and after each compose, as the preflight already does, and let Stage 1
  establish the real figure. Report it in the build report so the next contributor has a
  number that came from a measurement.

### Tooling

The probe in
[`IMAGE_PIPELINE.md`](IMAGE_PIPELINE.md#release-specific-interface-probe) queries
`rpm-ostree` and `image-builder`, so those are the package names to try first. Fedora has
moved Atomic Desktop image building to Image Builder, so a Fedora 45 host may provide
`image-builder` where older documentation refers to `osbuild-composer` or `composer-cli`.
Confirm which exists rather than assuming:

```sh
rpm -q rpm-ostree ostree image-builder
```

Install what is missing through the host's package manager, then record the installed
versions. Also confirm the QEMU-side tools needed for the boot gate: `qemu-system-x86_64`,
`qemu-img`, a non-Secure-Boot UEFI firmware code file, and its matching writable
variable-store template. The preflight report notes that firmware file locations differ
across distributions, so identify the actual paths rather than reusing another host's.

### Before composing

Run the read-only capability probes in
[`IMAGE_PIPELINE.md`](IMAGE_PIPELINE.md#release-specific-interface-probe) and record their
output verbatim. They establish whether Fedora 45 actually offers the Kinoite qcow2 image
type and the OSTree input options the pipeline depends on. Use the discovered type name
and option names to construct the build command.

**Stop and record a blocker** if the toolchain cannot compose the treefile, cannot
identify or retain its resolved package inputs, or cannot accept the resulting OSTree
commit as input to an x86-64 qcow2 build. Do not substitute a different image type to get
a result; record the deviation and its effect on the gate.

Then record the full preflight set listed in
[`IMAGE_PIPELINE.md`](IMAGE_PIPELINE.md#preflight), and the vendored Fedora configuration
revision required by
[DEC-049](DECISIONS.md#dec-049-treefile-derivation-from-fedoras-configuration).

## Route 2 — Fedora 45 guest on a non-Fedora workstation

Provision a Fedora 45 x86-64 guest and treat it as a route 1 host once it is running. Two
things need stating in the report:

- **Nested virtualization.** The boot gate runs QEMU *inside* this guest. If nesting is
  unavailable, the Axiom VM runs without hardware acceleration — slow, and a difference
  from the baseline profile worth recording. Confirm whether nesting is enabled and report
  it either way.
- **Resources.** The guest needs enough disk for the compose work described above, on top
  of its own installation.

This route is an experiment under DEC-030 because the complete pipeline has not been
validated in it. It is the most practical route when no Fedora machine is available, and a
finding from it is still useful evidence.

## Route 3 — container or toolbx for the compose step

Run a Fedora 45 container for composition and keep QEMU on the workstation, splitting the
pipeline across two environments.

The obstacle is privilege: `rpm-ostree compose tree` needs capabilities that an
unprivileged container does not have, and whether a sufficiently privileged container can
complete a compose on Fedora 45 **is itself an open validation item**. Do not present this
route as working until a report shows a compose completing in it. Splitting the pipeline
also means the build host and the validation host differ, which must be recorded as a
deviation because the report then describes two environments rather than one.

## Reporting

File a dated report at `docs/validation/YYYY-MM-DD-<topic>.md` for every attempt, naming
the route used and the result (`not run` / `blocked` / `failed` / `passed`). Use the build
report template in
[`IMAGE_PIPELINE.md`](IMAGE_PIPELINE.md#build-report-template). Then update the README
"Current status" so it matches the newest report, per
[`DOC_CONVENTIONS.md`](DOC_CONVENTIONS.md#keeping-the-readme-current).
