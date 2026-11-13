# LLM contribution guide

This repository is defining a new OS project. Treat its documentation as the current
product and architecture specification.

## Project facts

- Axiom OS is the working project name; the OS is built on the upstream Linux kernel.
- Rust is the primary language for project-owned system software where practical.
- Axiom OS is not a new kernel, and it is not a claim that the Linux kernel itself is
  written in Rust.
- At this stage, the repository is documentation-only. Do not create implementation,
  configuration, build, or boot files unless a task explicitly asks to move into
  implementation.
- Product goals in `docs/VISION.md` are aspirations, not claims that features already
  exist or guarantees of universal compatibility.

## Tripwires — break one and nothing goes red

Every rule in this repository fails silently. There is no build to break and no test to
turn red: a false claim, a renumbered ID or an unindexed document reads exactly like
correct work. The detail lives in the rule each line names; these are the one-liners.

- **Never write that an image boots, a feature works, or a milestone is complete** unless a
  dated report in `docs/validation/` shows it. → *Validation reports* below, and the
  **Evidence language** section of `docs/DOC_CONVENTIONS.md`.
- **Never cite a Proposed decision as settled, and never implement from one.** Read the
  record's `Status:` line before relying on it; do not assume a high ID means Proposed or a
  low one means Accepted. → *Citing a Proposed decision* below.
- **Never set `Status: Accepted` and never move a question to Resolved.** You may propose;
  only the owner accepts (DEC-032). → *What you may decide, and what you must escalate*.
- **Never rewrite an accepted record** when the decision itself changes. Add a superseding
  record instead. → *Changing an accepted record* below.
- **Never reuse, renumber or delete a DEC ID or an open-question number.** Other documents
  cite them and nothing checks those citations. → *Decision records* below, and
  `docs/OPEN_QUESTIONS.md`.
- **The README's "Current status" must never claim more than the newest validation report
  supports**, and an index must be updated in the same commit as the thing it points at.
  → **Keeping the README current** in `docs/DOC_CONVENTIONS.md`.
- **A document no index references will not be read.** Adding one means adding it to the
  README index and to the reading order below. → **Keeping the README current**.
- **Do not create an implementation, configuration, or build file** — that includes a
  linter config or a git hook, however small. → *The documentation-only gate* below.

## Reading order

`docs/DECISIONS.md` is the authoritative rule source. Where any other document disagrees
with it, DECISIONS.md wins and the other document is a defect you should report or fix.

1. **`docs/DECISIONS.md`** — the accepted decisions. These are the rules you apply.
2. **`docs/REQUIREMENTS.md`** — each decision restated as a concrete, checkable
   requirement, with how to verify it. Start here when you need to know *what to produce*.
3. **`docs/GLOSSARY.md`** — terminology. Use these words exactly as defined.
4. **`README.md`**, **`docs/PROJECT.md`**, **`docs/VISION.md`**, **`docs/SYSTEM.md`** —
   purpose, audience, scope, and the shape of the system.
5. **`docs/ROADMAP.md`** — which stage is current and what its exit conditions are.
6. **`docs/OPEN_QUESTIONS.md`** — what is deliberately still undecided.
7. **`docs/DOC_CONVENTIONS.md`** — how to write and file whatever you produce.

Then read the specification covering your task:

| Task | Specification |
|---|---|
| Setting up a build host | `docs/BUILD_HOST.md` |
| Writing or changing the treefile | `docs/TREEFILE_SPEC.md` |
| Where a file belongs | `docs/REPO_LAYOUT.md` |
| Building or booting an image | `docs/IMAGE_PIPELINE.md` |
| The update and rollback workflow | `docs/UPDATE_ROLLBACK_SPEC.md` |
| Application installation and defaults | `docs/APP_INSTALLATION.md` |
| Writing a check, or claiming a gate | `docs/TESTING.md` |
| Writing an Axiom-owned component | `docs/RUST_COMPONENTS.md` |

## How to work here

1. Follow the reading order above before proposing a direction.
2. Keep confirmed decisions, proposals, and unresolved questions distinct. Do not present
   assumptions as settled architecture.
3. Prefer upstream Linux interfaces and established standards. Avoid proposing a custom
   kernel fork or bespoke subsystem without a clear need.
4. Keep Rust's role realistic: Linux already provides the kernel and many existing
   user-space components. State where Rust is intended to be used and where existing
   components may remain appropriate.
5. When a task needs a decision that is not documented, record it as a **Proposed**
   decision with its trade-offs (see below). Do not silently invent a requirement.
6. Keep documentation concise, internally consistent, and useful to both maintainers and
   future implementation agents.
7. Do not claim that an image boots, a feature works, or a milestone is complete without
   evidence. Evidence means a dated report in `docs/validation/`.

## What you may decide, and what you must escalate

Under [DEC-032](docs/DECISIONS.md#dec-032-architecture-and-release-approval) the repository
owner approves architecture decisions and prototype releases.

**You may decide, and should simply state that you did:**

- Process and presentation: document structure, section order, wording, where a file goes
  within the documented conventions.
- Clerical corrections: a stale cross-reference, a broken link, a miscount, a status line
  that contradicts `DECISIONS.md`. Apply inline with a dated amendment note.
- Requirements that follow **necessarily** from an accepted decision. Cite the DEC ID that
  compels it. If you cannot cite one, it is not derived — it is a new choice.

**You must escalate, as a Proposed decision:**

- Any architecture, security, release, packaging, or product-scope choice not already
  compelled by an accepted decision.
- Anything that would change, narrow, or broaden an accepted decision.
- Any resolution of an entry in `docs/OPEN_QUESTIONS.md`.

**An agent may propose a decision. An agent may never accept one.** Never set
`Status: Accepted`, and never move a question to Resolved. That is the owner's action.

## Decision records

Add a new record at the end of `docs/DECISIONS.md` using the entry format at the top of
that file. Numbering is sequential and permanent: never reuse, renumber, or delete an ID,
because other documents cite them.

Status values and the only permitted transitions:

| Status | Meaning |
|---|---|
| **Proposed** | Drafted, awaiting owner approval. Not a rule yet. |
| **Accepted** | A rule. Apply it. Owner action only. |
| **Rejected** | Considered and declined. Kept for the record; never deleted. |
| **Superseded** | Was accepted, now replaced. Must name the record that replaces it. |
| **Accepted (superseded in part by X)** | Still a rule except for one named clause replaced by X. Use this instead of Superseded when the rest of the record still holds; state exactly which clause changed. |

`Proposed → Accepted | Rejected`, and `Accepted → Superseded`. Nothing returns to Proposed.

**Changing an accepted record:**

- **Clerical** (a typo, a miscount, a stale cross-reference — nothing about the decision
  changes): edit inline and append a dated `**Amended YYYY-MM-DD (clerical):**` line
  saying what changed and why.
- **Substantive** (the decision itself changes): do **not** rewrite it. Add a new record
  that supersedes it in whole or in part, and point the old record at the new one — fully
  Superseded if nothing survives, otherwise `Accepted (superseded in part by …)` naming the
  replaced clause. This needs owner approval like any new decision.

**Citing a Proposed decision:** only inside a section explicitly marked provisional, and
never as a settled requirement. **Do not implement from a Proposed decision.** If your
task depends on one, stop and say what is blocked.

**Recording a decision after the fact:** if the choice was already made and acted on before
anyone wrote it down, say so in the record rather than presenting it as contemporaneous. Open
with a short provenance note — `> **Note on provenance.** This record was written after the
decision was made …` — and mark any rationale you reconstructed rather than took from a
source with *(inference)*. A reader can then tell a recorded decision from a reasonable
guess about why something was done.

**An amendment that corrects something stated earlier in the same record:** say which part
to read first. A reader who meets the superseded figure before the correction will use the
wrong one, and the amendment note alone does not prevent that — for example, "Read the
2026-11-12 amendment before the Decision paragraph, whose counts it corrects."

## Validation reports

Never claim a build, boot, or feature works. Record what was observed instead.

File a dated report at `docs/validation/YYYY-MM-DD-<topic>.md` for every attempt,
including attempts that were blocked or failed — a blocked attempt is useful evidence.
Use the build report template in `docs/IMAGE_PIPELINE.md` for image-pipeline work. State
the result as one of: **not run / blocked / failed / passed**. Record deviations from the
documented baseline rather than redefining the baseline. Then update the README
"Current status" section so it matches the newest report.

## The documentation-only gate

Implementation has not started. Do not create treefiles, build scripts, configuration,
units, or source files. A specification may contain an illustrative fenced code block,
but it must be explicitly marked as illustrative and non-authoritative.

The gate lifts only when a task explicitly asks to move into implementation **and** the
decisions that task depends on are Accepted, not Proposed. This guide does not authorize
implementation by itself, and neither does an approved plan to write documentation.

## Scope boundary

The project is intended to include a Linux-based system image, Rust system services and
tools where appropriate, packaging, installation and update mechanisms, and a user-facing
environment. Foundational choices are recorded in `docs/DECISIONS.md`.
