# Repository layout

**Status:** Layout specification, recorded by
[DEC-043](DECISIONS.md#dec-043-repository-layout-for-implementation-artifacts). Only the
documentation paths exist today; the rest are reserved so specifications can cite stable
paths. Directories are created when work reaches them, not in advance — do not create
them to match this document.

## Reserved layout

```text
README.md                 index and current status
CLAUDE.md, AGENTS.md      agent entrypoints; thin pointers to LLM_GUIDE.md
LLM_GUIDE.md              canonical guide for automated contributors
CONTRIBUTING.md, LICENSE  contribution terms (DEC-033)

docs/                     project documentation
docs/validation/          dated validation reports, one per attempt

image/                    system composition inputs
image/treefile/           Axiom treefile and any included fragments
image/treefile/fedora/    vendored Fedora Atomic Desktop config, pinned (DEC-049)
image/lockfiles/          architecture-specific package lockfiles (DEC-031)
image/repos/              named Fedora repository definitions (DEC-031)
image/blueprints/         Image Builder blueprint(s) for the qcow2 artifact

crates/                   Axiom-owned Rust components, one directory per crate
crates/Cargo.toml         workspace root (DEC-046)
rust-toolchain.toml       pinned toolchain (DEC-046)

build/                    build and validation entry points
build/checks/             composition and boot checks (DEC-047)

release/                  provenance manifest template and release procedure (DEC-026)
```

## Why the boundaries fall here

The split is not stylistic; it follows boundaries the decisions already draw.

- **`image/` separate from `build/`** —
  [DEC-008](DECISIONS.md#dec-008-custom-kinoite-image-pipeline) separates package
  selection (the treefile) from artifact assembly (Image Builder). Keeping inputs apart
  from the orchestration that consumes them keeps that separation visible.
- **`image/treefile/fedora/` separate from the Axiom treefile** —
  [DEC-049](DECISIONS.md#dec-049-treefile-derivation-from-fedoras-configuration) derives
  Axiom's tree by including Fedora's at a pinned revision. Vendoring it in its own
  directory is what makes the Axiom diff reviewable: everything Axiom changed is in the
  Axiom treefile, and everything inherited is visibly upstream.
- **`release/` separate from `build/`** —
  [DEC-016](DECISIONS.md#dec-016-separate-release-signing-from-image-builds) requires
  release signing keys to be unavailable to build workers. Separate directories make the
  boundary legible, though directory structure alone does not enforce it; the access
  control does.
- **`crates/` as one workspace** —
  [DEC-046](DECISIONS.md#dec-046-rust-toolchain-baseline-and-component-conventions) pins
  one toolchain and one lockfile across all components, which a single workspace provides.

## Rules

- **Paths are citable and therefore load-bearing.** Specifications reference them; moving
  one invalidates those references. Treat a move as a change needing a decision.
- **One crate per component**, each with the record
  [DEC-029](DECISIONS.md#dec-029-rust-and-upstream-component-ownership) requires: purpose,
  owner, privilege boundary, maintenance plan.
- **Lockfiles are source.**
  [DEC-031](DECISIONS.md#dec-031-pin-and-audit-production-build-inputs) requires lockfile
  changes to be reviewed as source changes, so they are committed, never generated into an
  ignored path.
- **Nothing outside `docs/` may be created yet.** The documentation-only gate in
  [`LLM_GUIDE.md`](../LLM_GUIDE.md#the-documentation-only-gate) holds until a task
  explicitly moves into implementation. This document reserves paths; it does not
  authorize populating them.
