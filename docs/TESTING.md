# Verification strategy

**Status:** Strategy specification. No check has been written and no check has been run. The
single validation report on file records a blocked host preflight, not a test result.

This document defines what a check is, where checks live, and what may be claimed from one.
The governing decision is
[DEC-047](DECISIONS.md#dec-047-verification-strategy-and-evidence-retention), which exists
because [`ROADMAP.md`](ROADMAP.md) Stage 2 requires "repeatable checks for its core
behavior" without defining the term.

## The three classes

Every requirement in [`REQUIREMENTS.md`](REQUIREMENTS.md) is verified by one of these.

### Composition checks

Run on the build host, against the composed tree or image, **without booting it.**

- A package is present or absent — `flatpak-kcm` present (DEC-022), `flatseal` absent
  (DEC-022), no Brave or ArtCraft RPM in the commit (DEC-048).
- A unit is enabled or disabled — unattended system updates disabled (DEC-009), no SSH or
  desktop-sharing service enabled (DEC-023).
- A configuration value is set — `os-release` fields (DEC-045), journald bounds (DEC-023).
- No unexpected source is configured — only named Fedora repositories as composition inputs
  (DEC-031), only the `axiom` deployment remote (DEC-015).

Cheap, scriptable, and the right place for most of the matrix. Run them on every build.

### Boot checks

Run inside the baseline QEMU profile (DEC-038, DEC-040) against a booted image, asserting
machine-readable state from within the guest.

- Deployment identity and the deployment list.
- Enabled units and listening sockets — the credible check for DEC-023, because a unit can
  be disabled in the tree and still be started by something else.
- Configured Flatpak remotes: exactly Flathub and the ArtCraft OCI remote, and no third
  (DEC-020, DEC-050).
- `os-release` as the running system reports it (DEC-045).

A composition check proves what was built; a boot check proves what the built thing does on
start. Both are needed, because the tree is not the running system.

### Interaction checks

Require a person in the target Plasma session. Not automated for the first release.

- Whether the update summary is legible to a newcomer (DEC-009).
- Whether the rollback action is findable without knowing the word "deployment" (DEC-009).
- Whether each default app uninstalls individually through Discover (DEC-042, DEC-048).
- Whether the Flatpak permission page is reachable and its labels make sense (DEC-022).
- Whether the interface shows which remote supplied an application (DEC-050).

These are judgments, not assertions, which is exactly why they stay manual and why each is
recorded with a screenshot. The procedure for the update and rollback set is
[`UPDATE_ROLLBACK_SPEC.md`](UPDATE_ROLLBACK_SPEC.md).

## Choosing a class

Work down this list and stop at the first that can actually check the requirement:

1. **Composition**, if the requirement is a property of the built tree.
2. **Boot**, if it depends on the running system — anything about units, sockets, remotes as
   configured at runtime, or deployment state.
3. **Interaction**, if it depends on human judgment.

Do not promote a requirement to interaction because automating it is inconvenient; the
interaction set is the slowest and scarcest evidence the project has. Do not demote one to
composition because it is easier there — "the unit file says disabled" is not the same claim
as "nothing started it."

Where a requirement splits, split the check. The update procedure's deployment-list
assertions are boot checks even though the legibility judgment beside them is not.

## Where checks live

`build/checks/`, behind a single entry point, per DEC-047 and the paths reserved by
[DEC-043](DECISIONS.md#dec-043-repository-layout-for-implementation-artifacts). Composition
and boot checks together are the "repeatable checks" Stage 2's exit condition requires.

The entry point is Axiom-owned code and needs a component record under DEC-029 and the
conventions in [`RUST_COMPONENTS.md`](RUST_COMPONENTS.md). **None of it may be created while
the documentation-only gate holds** — that gate covers build and configuration files
explicitly, however small.

## What a check result may be used for

- **A check result is evidence only once it is in a dated report** in `docs/validation/`,
  per [`DOC_CONVENTIONS.md`](DOC_CONVENTIONS.md). A green run in someone's terminal is not
  evidence; it is not citable and not reviewable.
- **A gate is claimed passed only when every requirement in its scope has a recorded result**
  (DEC-047). Partial coverage is a partial result, recorded as such.
- **A check never establishes reproducibility.** That claim requires an independent rebuild
  producing matching declared outputs (DEC-026, DEC-041). Prototype evidence is traceable
  inputs, nothing more.
- **A passing check is not a support claim.** DEC-034 limits claims to the documented
  configuration and the tested workflows.

## Reports, and failures worth keeping

One report per attempt, including blocked and failed attempts — they are the most useful
reports the project has, because they stop the next contributor repeating the work. The
2026-11-07 host preflight is the model: it established what was absent and named the next
action, without attempting anything it could not.

Reports are immutable. A later attempt files a new report naming the old one. Then update the
README "Current status" so it does not outrun the newest report.

## Missing tools

A check whose tool is absent must **skip with a clear message, not fail.** A failure that
means "not installed" trains contributors to ignore failures, which costs more than the check
was worth. Record skips in the report; a skipped check is not a passed check.

## A proposed fourth class

[DEC-051](DECISIONS.md#dec-051-documentation-consistency-checks) proposes **documentation
checks** — link and anchor resolution, DEC-ID existence, status agreement, spelling — as a
fourth class covering the corpus rather than the product.

**That record is Proposed, not Accepted.** Do not implement from it, and do not create the
configuration files it describes; DEC-051 itself states that none is created unless and until
it is accepted. It is noted here so this document is not read as a complete account of
verification while that proposal is open.

If it is accepted, the distinction to preserve is the one DEC-051 draws itself: a
documentation check verifies internal consistency only, and a green run must never be cited
as evidence that anything builds, boots or works.

## Current state

| Class | Checks written | Checks run |
|---|---|---|
| Composition | none | none |
| Boot | none | none |
| Interaction | procedure specified for update and rollback | none |

The authoritative list of requirements and their state is
[`REQUIREMENTS.md`](REQUIREMENTS.md).
