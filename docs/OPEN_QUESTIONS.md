# Open questions

Foundational product and architecture decisions are recorded in [Decision records](DECISIONS.md). The questions below are the remaining choices that affect the first build and validation gates. Resolve a question when its answer is needed; do not block early pipeline experiments on decisions reserved for production.

## Before the first reproducible QEMU image

1. **What repository snapshot policy will make Fedora 45 inputs repeatable?** Choose how to retain and identify repository metadata and package inputs for each prototype build, then validate that the chosen treefile/lockfile workflow actually enforces them. DEC-031 sets the production requirement; this question is about selecting and measuring the prototype mechanism. See DEC-026, DEC-031, and DEC-035.
2. **What values define the fixed QEMU baseline profile?** Choose the CPU model, memory, disk size, display device, and network device from Fedora 45 image requirements, then document them in the build instructions. The fixed-baseline approach is accepted in DEC-038; x86-64 and UEFI/GPT are already set by DEC-004 and DEC-011.
3. **What constitutes a reproducible prototype build?** DEC-031 specifies strict production input pinning, but its exact lockfile workflow is unvalidated. During pipeline validation, determine whether the selected tooling can enforce the resolved RPM set and identify the metadata needed to repeat a build. Record measured limits; reserve a production reproducibility claim for an independent matching rebuild per DEC-026.

## Before first-release workflow evaluation

5. **How will the prototype demonstrate the newcomer update and rollback flow?** Confirm the required user-visible steps and evidence for update explanation/approval, staging and reboot, rollback authorization, and recovery after a failed boot. The product outcome is accepted in DEC-003, DEC-009, and DEC-010; the target-session procedure and acceptance evidence remain to be specified.
## Before production distribution

7. **How are release keys operated and recovered?** Define custody, offline storage, backups, maintainer access, rotation, revocation, and client trust transition before production signing. See DEC-014 through DEC-016 and DEC-025.
8. **What Secure Boot support and signing policy will Axiom offer?** Revisit this closer to public release, including supported firmware behavior, whether users enroll Axiom keys, key custody/rotation, and test coverage. Secure Boot is explicitly deferred from the initial prototype by DEC-036; OSTree update signing remains required by DEC-014.
9. **What physical-install and encryption recovery flow is supported?** DEC-013 requires LUKS2 for a future physical-install image, but the installer, recovery-key handling, and supported opt-out need a separate design before that scope starts.
10. **What are the final public name and release maintainers?** Confirm identity before production branding, and publish release-maintainer responsibilities before production images. See DEC-027 and DEC-032.

## Resolved

- Audience, problem, first useful release, QEMU architecture, Fedora 45 Kinoite/KDE foundation, image pipeline direction, update/rollback model, application source, security baseline, support window, and contribution license are recorded as accepted decisions in `DECISIONS.md`.
- Secure Boot is deferred until closer to public release; the first QEMU demo is gated on boot and system update/rollback, while the full Flatpak contract remains required for first-release readiness; the QEMU validation approach uses one fixed baseline profile (DEC-036 through DEC-038).
