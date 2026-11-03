# Open questions

These are foundational decisions, not implied defaults. Resolve them with explicit rationale before they become implementation assumptions.

## Product

- Who is the initial audience: developers, general desktop users, servers, embedded devices, or another group?
- What concrete problem or experience will distinguish Rusty OS from existing Linux distributions?
- What is the smallest useful first release?

## Platform and system composition

- What initial hardware or virtual machine target should define the first bootable milestone?
- Should the system start from an existing distribution or assemble a minimal system from upstream components?
- Which bootloader, init/service manager, libc, and base user-space components best fit that target?
- Which components are project-owned Rust software, and which should remain established upstream components?

## Build and delivery

- Which image formats and development environments need initial support?
- How will dependencies and build inputs be pinned and audited?
- Is the first artifact intended for virtual machines, physical installation, cloud images, containers, or another environment?

## Security and operations

- What threat model and privilege model should guide the base system?
- How will updates be authenticated, applied, recovered, and rolled back?
- What logging, remote access, and default network behavior are appropriate for the audience?
- What support lifetime and security response process can the project maintain?

## Governance

- Who approves architecture and release decisions?
- What license and contribution policy should apply?
- What compatibility and stability guarantees should users be able to expect?

When resolved, add entries to `docs/DECISIONS.md` with date, status, context, decision, alternatives, and consequences. Keep this file for questions that remain open.
