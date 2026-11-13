# Open questions

Foundational product and architecture decisions are recorded in [Decision records](DECISIONS.md). The questions below are the remaining choices that affect the first build and validation gates. Resolve a question when its answer is needed; do not block early pipeline experiments on decisions reserved for production.

Question numbers are stable identifiers and are never reused or renumbered, because other documents cite them. Numbering starts at 5 because questions 1 through 4 were resolved into accepted decision records; see [Resolved](#resolved) below.

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
- Question 6, the default-app delivery conflict, is resolved by [DEC-048](DECISIONS.md#dec-048-default-application-delivery-for-brave-and-artcraft) and [DEC-050](DECISIONS.md#dec-050-permit-the-artcraft-oci-remote-as-a-second-default-application-source): both defaults ship as Flatpaks, and ArtCraft's own OCI remote is permitted as a second default source, superseding that clause of DEC-020. The remote's integrity properties and the launcher's in-app installation behavior remain validation items, not open decisions.
- Repository layout, the OSTree remote and version scheme, build identity, the Rust toolchain baseline, verification strategy, and treefile derivation are recorded in DEC-043 through DEC-049.
- Prototype input retention, QEMU baseline resources, and reproducibility evidence are recorded in DEC-039 through DEC-041. Validate the selected Fedora 45 tooling and exact virtual device choices during Stage 1; these decisions do not assert that pinning or output reproducibility has already been demonstrated.
