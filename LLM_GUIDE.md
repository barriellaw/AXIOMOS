# LLM contribution guide

This repository is defining a new OS project. Treat its documentation as the current product and architecture specification.

## Project facts

- Axiom OS is the working project name; the OS is built on the upstream Linux kernel.
- Rust is the primary language for project-owned system software where practical.
- Axiom OS is not a new kernel, and it is not a claim that the Linux kernel itself is written in Rust.
- At this stage, the repository is documentation-only. Do not create implementation, configuration, build, or boot files unless a task explicitly asks to move into implementation.
- Product goals in `docs/VISION.md` are aspirations, not claims that features already exist or guarantees of universal compatibility.

## How to work here

1. Read `README.md`, `docs/PROJECT.md`, `docs/SYSTEM.md`, and relevant entries in `docs/OPEN_QUESTIONS.md` before proposing a direction.
2. Keep confirmed decisions, proposals, and unresolved questions distinct. Do not present assumptions as settled architecture.
3. Prefer upstream Linux interfaces and established standards. Avoid proposing a custom kernel fork or bespoke subsystem without a clear need.
4. Keep Rust's role realistic: Linux already provides the kernel and many existing user-space components. State where Rust is intended to be used and where existing components may remain appropriate.
5. When a task needs a decision that is not documented, record the decision and its trade-offs in the appropriate project document; do not silently invent a requirement.
6. Keep documentation concise, internally consistent, and useful to both maintainers and future implementation agents.
7. Do not claim that an image boots, a feature works, or a milestone is complete without evidence.

## Scope boundary

The project may eventually include a Linux-based system image, Rust system services and tools, packaging, installation and update mechanisms, and a user-facing environment. Their exact choices remain open until documented. This guide does not authorize implementing them by itself.
