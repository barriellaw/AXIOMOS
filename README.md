# Axiom OS

Axiom OS is the working name for a project to build a user-focused desktop operating system around the Linux kernel, with Rust as the preferred language for new project-owned system software.

The Linux kernel remains the kernel: this project does not aim to replace it with a Rust microkernel or write a kernel from scratch. The intended work is to define and build the software, system image, and developer experience that turn a Linux kernel into a distinct OS.

This repository currently contains project definition documents only. No implementation has been started, and the documents below describe intent and constraints rather than functioning software.

## Start here

- [Project charter](docs/PROJECT.md) — purpose, scope, and guiding principles.
- [Product vision](docs/VISION.md) — intended audience experience and product goals.
- [System outline](docs/SYSTEM.md) — a conceptual map of the OS and its major boundaries.
- [Roadmap](docs/ROADMAP.md) — staged work from definition through a first bootable system.
- [Prototype image-pipeline validation](docs/IMAGE_PIPELINE.md) — Stage 1 preflight, fixed QEMU baseline, and build evidence template.
- [Application installation contract](docs/APP_INSTALLATION.md) — first-release Flatpak source, permission, and user-control expectations.
- [Contribution guide](CONTRIBUTING.md) — how to propose changes and contribution licensing.
- [LLM contribution guide](LLM_GUIDE.md) — instructions for AI tools working in this repository.
- [Open questions](docs/OPEN_QUESTIONS.md) — decisions that still need research or discussion.
- [Foundation research](docs/FOUNDATION_RESEARCH.md) — preliminary comparison of Linux foundation candidates.

## Current status

**Stage: decision baseline recorded; image-pipeline validation is next.** Fedora 45 Kinoite is the selected base for the initial prototype. There are no code, build, or boot artifacts yet. The next engineering milestone is to validate the custom image pipeline and produce a bootable x86-64 QEMU image. The current gate and remaining decisions are tracked in the [roadmap](docs/ROADMAP.md) and [open questions](docs/OPEN_QUESTIONS.md).

Unless stated otherwise, original Axiom project content and project-owned code are licensed under [Apache-2.0](LICENSE). Third-party components retain their respective licenses.
