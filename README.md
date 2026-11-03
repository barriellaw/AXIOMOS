# Rusty OS

Rusty OS is a project to build a coherent operating system experience around the Linux kernel, with Rust as the primary language for project-owned system software.

The Linux kernel remains the kernel: this project does not aim to replace it with a Rust microkernel or write a kernel from scratch. The intended work is to define and build the software, system image, and developer experience that turn a Linux kernel into a distinct OS.

This repository currently contains project definition documents only. No implementation has been started, and the documents below describe intent and constraints rather than functioning software.

## Start here

- [Project charter](docs/PROJECT.md) — purpose, scope, and guiding principles.
- [System outline](docs/SYSTEM.md) — a conceptual map of the OS and its major boundaries.
- [Roadmap](docs/ROADMAP.md) — staged work from definition through a first bootable system.
- [LLM contribution guide](LLM_GUIDE.md) — instructions for AI tools working in this repository.
- [Open questions](docs/OPEN_QUESTIONS.md) — decisions that still need research or discussion.

## Current status

**Stage: project definition.** There are no code, build, or boot artifacts yet. The first engineering milestone is to turn the open questions into explicit, documented choices before implementation begins.
