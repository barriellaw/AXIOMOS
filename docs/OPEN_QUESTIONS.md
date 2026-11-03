# Open questions

These are foundational decisions, not implied defaults. Resolve them with explicit rationale before they become implementation assumptions.

## Suggested decision order

Answer these in sequence. Later choices depend on earlier ones, so keep implementation details open until the product scope is clear.

1. **Audience and problem:** Choose who the first version serves and what need it addresses.
2. **First useful release:** Define the smallest outcome that would make the project useful to that audience.
3. **Initial target:** Choose a hardware or virtual machine target that can demonstrate that outcome.
4. **System composition and delivery:** Select the base system and image approach that fit the target and the team's maintenance capacity.
5. **Security and governance:** Set the minimum operating, update, support, and contribution expectations before implementation expands.

The component-level choices below (such as bootloader and service manager) should follow from these decisions. This order is guidance, not a set of preselected answers.

## Product

- Confirm whether **Axiom OS** is the lasting project name or only a codename.

## Platform and system composition

- Which init/service manager, libc, and base user-space components should Axiom inherit from Fedora Kinoite, and which should it replace?
- Which components are project-owned Rust software, and which should remain established upstream components?

## Build and delivery

- Which image formats and development environments need initial support?
- How will dependencies and build inputs be pinned and audited?
- Is the first artifact intended for virtual machines, physical installation, cloud images, containers, or another environment?

## Security and operations

- How will updates be authenticated, applied, recovered, and rolled back?
- Which signing-key format, storage, backup, rotation, and trust-recovery process can Axiom maintain?
- What build-input provenance records and review process should production releases require?
- Which supported desktop tools should Axiom recommend for inspecting and changing Flatpak permissions?
- What logging, remote access, and default network behavior are appropriate for the audience?
- What security response targets can the project maintain?

## Governance

- Who approves architecture and release decisions?
- What license and contribution policy should apply?
- What compatibility and stability guarantees should users be able to expect?

When resolved, add entries to `docs/DECISIONS.md` with date, status, context, decision, alternatives, and consequences. Keep this file for questions that remain open.
