# Axiom-owned Rust components

**Status:** Standards specification. **No Axiom component exists.** No crate, workspace or
toolchain file has been created, and the documentation-only gate in
[`LLM_GUIDE.md`](../LLM_GUIDE.md#the-documentation-only-gate) forbids creating them until a
task explicitly moves into implementation. This document says what a component must satisfy
when that happens.

The language policy is [DEC-029](DECISIONS.md#dec-029-rust-and-upstream-component-ownership)
and the toolchain baseline is
[DEC-046](DECISIONS.md#dec-046-rust-toolchain-baseline-and-component-conventions).

## When a component is justified

Rust is the default for **new** Axiom-owned system software, not a reason to write software.
DEC-029 prefers established upstream components for existing system responsibilities and
forbids rewriting or forking them solely to use Rust. Before proposing a component, answer:

1. **What user-facing need does it serve?** DEC-029 requires one. "It would be cleaner in
   Rust" is not a need.
2. **Does something upstream already do this?** DEC-028 inherits the kernel, systemd, glibc
   and the rpm-ostree/OSTree stack, and DEC-005 requires researching existing components
   before proposing replacements.
3. **Who maintains it, and for how long?** A component with no owner is a liability the
   project carries at every base-release migration.

If the answers are thin, the component is not justified yet. Record the reasoning either way
— a rejected proposal is useful to the next person who has the same idea.

## The component record

[DEC-029](DECISIONS.md#dec-029-rust-and-upstream-component-ownership) requires every
Axiom-owned component to document its purpose, owner, privilege boundary and maintenance
plan. Keep the record with the crate, and give it this shape:

```markdown
# <component name>

- **Purpose:** the user-facing need it serves, in one or two sentences.
- **Owner:** who maintains it.
- **Privilege boundary:** what privileges it holds, what it requests from elsewhere, and
  how it is authorized. State explicitly if it holds none.
- **Interfaces consumed:** the external interfaces it calls, and why each is the supported
  path rather than a convenient one.
- **Dependencies:** each direct dependency and why it is needed (DEC-046); note which are
  packaged in Fedora.
- **Maintenance plan:** what a base-release migration requires of it, and what happens if
  an upstream interface it depends on changes.
- **Threat-model notes:** which DEC-012 assets it touches, and what it does not protect
  against.
```

A component whose privilege boundary is "none" is the good case and should say so plainly,
because that is the claim DEC-010 depends on.

## Workspace and toolchain

Per [DEC-046](DECISIONS.md#dec-046-rust-toolchain-baseline-and-component-conventions), and
using the paths reserved by
[DEC-043](DECISIONS.md#dec-043-repository-layout-for-implementation-artifacts):

| Rule | Requirement |
|---|---|
| Layout | One Cargo workspace under `crates/`, one crate per component |
| Toolchain | A committed `rust-toolchain.toml` naming an exact stable version, preferring the version packaged by the selected Fedora base release |
| Minimum version | The pinned version. No separate, older floor is maintained |
| Lockfile | `Cargo.lock` committed for all workspace members |
| Warnings | Denied in the check run, not merely reported |

Pinning to Fedora's packaged toolchain is what keeps component builds consistent with the
base system and avoids a second toolchain in the image pipeline. The cost is lagging upstream
Rust and a toolchain bump at each base-release migration; that bump belongs in the
maintenance plan of every component.

## Dependencies

- Prefer crates already packaged in Fedora. Where a dependency is not packaged, record why
  it is needed in the component record (DEC-046).
- Treat build inputs as untrusted (DEC-012). A dependency is a build input.
- Every `unsafe` block carries a comment stating the invariant it upholds (DEC-046). A block
  with no such comment is a defect regardless of whether it is correct.

## Calling privileged services

This is the convention with the most consequence, because
[DEC-010](DECISIONS.md#dec-010-privilege-boundary-for-rollback-controls) makes it the basis
of the rollback design.

- **Call documented D-Bus interfaces. Never shell out to the `rpm-ostree` command-line
  tool** from a graphical component (DEC-046). The CLI obscures which authorization path was
  taken, and DEC-010's boundary is only checkable if the call is explicit.
- Default to `zbus` unless a component record justifies another client (DEC-046).
- **Add no setuid helper, no root daemon, and no polkit rule that broadens access**
  (DEC-010). Rely on the authorization policy already present.
- Handle authorization denial, authentication prompts, a missing authentication agent, and
  operation failure as ordinary outcomes, not exceptional ones. The states to exercise are
  listed in
  [`UPDATE_ROLLBACK_SPEC.md`](UPDATE_ROLLBACK_SPEC.md#failure-states-that-must-be-exercised).
- Confirm interface and action names against the target system rather than this document.
  DEC-010 requires it, and release-specific names must not be inferred from another release's
  documentation.

## Errors and logging

- Structured error types at component boundaries (DEC-046). An error crossing into the
  interface should carry enough detail to explain itself to a non-technical user.
- Log to the journal (DEC-046). Logs stay local under Fedora's bounded journald defaults
  (DEC-023).
- **Never log credentials or full user file paths** (DEC-046, DEC-023). DEC-023 also warns
  that logs can contain sensitive system detail, which is why there is no automatic log
  upload and why any future support bundle needs a reviewable contents list.

## Likely first components

Named here so their scope is visible before anyone writes them, not as a commitment:

| Candidate | Purpose | Governing decisions |
|---|---|---|
| Rollback control surface | The accessible "undo last system update" action | DEC-009, DEC-010 |
| Check runner | The single entry point for composition and boot checks in `build/checks/` | DEC-047 |

The check runner is build tooling rather than shipped system software, but DEC-047 makes it
Axiom-owned code, so it needs a component record like any other. Neither may be created while
the documentation-only gate holds.

## Verification

Component requirements appear in [`REQUIREMENTS.md`](REQUIREMENTS.md) as DEC-046a through
DEC-046d and DEC-010a through DEC-010d. The privilege-boundary claims are checked by
inspection — shipped polkit rules, setuid binaries, process invocations — which makes them
**composition checks** under
[DEC-047](DECISIONS.md#dec-047-verification-strategy-and-evidence-retention); the behavioral
failure states are **interaction checks**. See [`TESTING.md`](TESTING.md).
