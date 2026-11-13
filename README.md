# Axiom OS

Axiom OS is the working name for a project to build a user-focused desktop operating system around the Linux kernel, with Rust as the preferred language for new project-owned system software.

The Linux kernel remains the kernel: this project does not aim to replace it with a Rust microkernel or write a kernel from scratch. The intended work is to define and build the software, system image, and developer experience that turn a Linux kernel into a distinct OS.

This repository currently contains project definition documents only. No implementation has been started, and the documents below describe intent and constraints rather than functioning software.

## Start here

**The rules:**

- [Decision records](docs/DECISIONS.md) — every accepted choice, with its context and trade-offs. **The authoritative rule source**; where another document disagrees with it, this one wins.
- [Requirements traceability](docs/REQUIREMENTS.md) — each decision restated as a concrete, checkable requirement with how to verify it. Start here to learn what to build.
- [Open questions](docs/OPEN_QUESTIONS.md) — what is deliberately still undecided.

**The project:**

- [Project charter](docs/PROJECT.md) — purpose, scope, and guiding principles.
- [Product vision](docs/VISION.md) — intended audience experience and product goals.
- [System outline](docs/SYSTEM.md) — a conceptual map of the OS and its major boundaries.
- [Roadmap](docs/ROADMAP.md) — staged work from definition through a first bootable system.
- [Foundation research](docs/FOUNDATION_RESEARCH.md) — preliminary comparison of Linux foundation candidates.

**Specifications and procedures:**

- [Build host setup](docs/BUILD_HOST.md) — the supported Fedora 45 build environment, and the two experimental routes.
- [Repository layout](docs/REPO_LAYOUT.md) — where implementation artifacts will live, and why the boundaries fall where they do.
- [Treefile specification](docs/TREEFILE_SPEC.md) — what the Axiom treefile must contain, exclude, and configure.
- [Prototype image-pipeline validation](docs/IMAGE_PIPELINE.md) — Stage 1 preflight, fixed QEMU baseline, and build evidence template.
- [Application installation contract](docs/APP_INSTALLATION.md) — first-release Flatpak source, permission, and user-control expectations.

**Working here:**

- [Glossary](docs/GLOSSARY.md) — terminology, the status vocabulary, and words to avoid.
- [Documentation conventions](docs/DOC_CONVENTIONS.md) — where files go, how to phrase claims, how to keep this index current.
- [Contribution guide](CONTRIBUTING.md) — how to propose changes and contribution licensing.
- [LLM contribution guide](LLM_GUIDE.md) — instructions for AI tools working in this repository; [`CLAUDE.md`](CLAUDE.md) and [`AGENTS.md`](AGENTS.md) point here.

## Current status

**Stage: validate the custom image pipeline.** Fedora 45 Kinoite is the selected base for the initial prototype, and the Stage 1 runbook is prepared.

**No Axiom image has been built or booted.** The recorded workspace-host preflight is blocked before composition because that Ubuntu host lacks Fedora's compose and Image Builder tools. The next engineering step is to run the Fedora 45 capability probes on a supported Fedora build host.

The documentation needed to do that is in place: repository layout, the OSTree remote and version scheme, build identity, the Rust toolchain baseline, verification strategy, default-application delivery, and treefile derivation are recorded as accepted decisions (DEC-043 through DEC-050), and the build-host, repository-layout, and treefile specifications are written. Four further records covering repository process rather than the system itself — documentation consistency checks, text line-ending normalization, an agent rule and skill layout, and branch roles (DEC-051 through DEC-054) — are Proposed and awaiting review; they are not rules yet, and nothing they describe has been created.

See the [build host setup](docs/BUILD_HOST.md), [treefile specification](docs/TREEFILE_SPEC.md), [image-pipeline runbook](docs/IMAGE_PIPELINE.md), [preflight report](docs/validation/2026-11-07-host-preflight.md), [requirements](docs/REQUIREMENTS.md), [roadmap](docs/ROADMAP.md), and [open questions](docs/OPEN_QUESTIONS.md).

Unless stated otherwise, original Axiom project content and project-owned code are licensed under [Apache-2.0](LICENSE). Third-party components retain their respective licenses.
