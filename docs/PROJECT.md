# Project charter

The working project name is **Axiom OS**. The name and product identity should be confirmed before a public release.

## Purpose

Define and build a maintainable desktop operating system based on the Linux kernel, using Rust as the preferred language for new project-owned system software where it is a good fit. The project should make clear choices about the complete system around the kernel: how it is assembled, configured, updated, operated, and developed. The intended user experience and product goals are summarized in [Product vision](VISION.md).

## Why this project exists

Linux offers a mature kernel, broad hardware support, and a large ecosystem. Rust offers memory safety and strong tooling for systems software. Axiom OS explores how to combine those strengths into a focused, understandable OS rather than treating a kernel alone as a complete product.

The initial audience is desktop newcomers. The product direction is to give them accessible system controls while retaining advanced control for experienced users. The initial problem and smallest useful release are recorded in [Decision records](DECISIONS.md): a reproducible x86-64 VM desktop demonstrating understandable system updates and rollback.

## Goals

- Build on an upstream Linux kernel and make the kernel relationship explicit.
- Make Rust the default choice for new project-authored system components when technical and ecosystem constraints support it.
- Make routine system changes understandable and reversible wherever the underlying technology permits.
- Provide a unified, transparent way to manage applications and system updates over time.
- Define a reproducible path from source and dependencies to a system image.
- Document system boundaries, security expectations, update strategy, and supported hardware before those decisions become costly to change.
- Keep the project approachable for contributors and legible to automated coding agents.

## Non-goals

- Writing a replacement kernel or presenting Axiom OS as a new kernel architecture.
- Rewriting all Linux user space in Rust regardless of value, compatibility, or maintenance cost.
- Promising compatibility with every Windows application, game, device, or peripheral.
- Inventing custom replacements for mature Linux components without a documented reason.
- Claiming a particular audience, interface, distribution base, package manager, init system, or release model before a decision is recorded.

## Guiding principles

1. **Upstream first.** Prefer upstream Linux and established interfaces; minimize long-lived patches.
2. **Safety with pragmatism.** Prefer Rust for new code where it improves safety and maintainability. Interoperate with existing C and other language components when replacing them would add risk without clear benefit.
3. **Reproducibility.** Build inputs and release artifacts should be traceable and repeatable.
4. **Small, explicit decisions.** Record trade-offs and revisit assumptions as evidence changes.
5. **Secure defaults.** Treat update integrity, least privilege, and vulnerability response as core system design concerns.
6. **Evidence over claims.** Describe functionality only after it can be demonstrated and verified.

## Initial success criteria

The project definition stage is complete when maintainers have documented the target audience, initial hardware target, system composition strategy, image/build approach, boot and update model, and first demonstrable milestone. These choices should be recorded in `docs/DECISIONS.md` before implementation is treated as underway.
