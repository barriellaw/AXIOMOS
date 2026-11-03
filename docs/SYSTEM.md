# Conceptual system outline

This document describes the intended shape of the system without selecting concrete implementations prematurely. It is a map for discussion, not a build specification.

## System boundary

Axiom OS is the complete operating system experience assembled around the Linux kernel. The kernel provides core mechanisms such as scheduling, memory management, device drivers, and system calls. Project decisions above that boundary determine the boot flow, system services, user environment, packaging, updates, and supported hardware.

Axiom OS should track upstream Linux unless a documented requirement justifies a patch. Any such patch should have an owner, rationale, and plan for upstreaming or removal.

## Conceptual layers

1. **Firmware and boot** — the initial target is a QEMU x86-64 virtual machine using Fedora Kinoite's UEFI/GPT boot profile; Secure Boot signing and key ownership, kernel, and early userspace details remain open.
2. **Base system** — essential files, libraries, device management, networking, identity, logging, and service management. Component choices are undecided.
3. **Rust system software** — project-authored services, administrative tools, and system utilities, selected based on clear requirements and suitable Rust ecosystem support.
4. **User-facing environment** — KDE Plasma on Fedora Kinoite is the selected initial desktop environment. The first-release app path is Discover with Flatpak from the full Flathub remote; package layering stays outside the newcomer workflow. See the [application installation contract](APP_INSTALLATION.md).
5. **Build, delivery, and maintenance** — the initial artifact is a reproducible QEMU image, built through the accepted treefile → OSTree commit → Image Builder pipeline; physical installation and release processes remain open.

## Language policy

“Rust-based” describes the project's direction for new, project-owned system software. It does not mean every component must be Rust. Linux kernel code and much of the broader Linux ecosystem are written in C or other languages; reusing mature components is compatible with the project goals. Component-by-component language choices should consider safety, interoperability, maintainability, and the cost of ownership.

## Boundaries to preserve

- Keep the Linux kernel distinct from project-owned user space and system integration.
- Prefer standard Linux interfaces between components.
- Avoid coupling project identity to a single language where upstream compatibility or mature software matters more.
- Document security boundaries and privilege requirements for each system service as architecture becomes concrete.

## Decisions still needed

See [Decision records](DECISIONS.md) for accepted choices and [Open questions](OPEN_QUESTIONS.md) for unresolved details. Fedora Atomic Desktop/Kinoite with KDE Plasma and Fedora's UEFI/GPT boot profile are selected for the initial target; Secure Boot signing, the exact image format, and implementation details of the accepted update and rollback experience remain open.
