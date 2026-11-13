# Documentation conventions

How to write and file documents in this repository. For *who may decide what*, see
[`LLM_GUIDE.md`](../LLM_GUIDE.md) — this file covers mechanics only and does not grant
authority to settle anything.

## Where each kind of file goes

| Kind | Location | Notes |
|---|---|---|
| Decision record | `docs/DECISIONS.md` | One file, appended. Never split or reordered. |
| Requirement mapping | `docs/REQUIREMENTS.md` | Decisions restated as checkable requirements. |
| Specification | `docs/<TOPIC>_SPEC.md` | What an implementer must produce. |
| Runbook / procedure | `docs/<TOPIC>.md` | Steps an operator follows, e.g. `IMAGE_PIPELINE.md`. |
| Validation report | `docs/validation/YYYY-MM-DD-<topic>.md` | One per attempt. Never edited after filing. |
| Research note | `docs/<TOPIC>_RESEARCH.md` | Comparison and reading, with a `Reviewed:` date. |
| Agent entrypoint | `CLAUDE.md`, `AGENTS.md` | Thin pointers to `LLM_GUIDE.md`. Keep them thin. |

Filenames are `SCREAMING_SNAKE_CASE.md` at the top level of `docs/`, except validation
reports, which are date-prefixed and lowercase. Do not add directories without a reason
recorded in a decision.

## Document structure

Every document opens with a one-or-two-sentence statement of what it is for and whether
it describes something that exists. Documents describing unbuilt things say so explicitly
in a **Status:** line — see `IMAGE_PIPELINE.md` for the pattern:

```text
**Status:** Runbook draft; no Axiom image has been built or booted.
```

Then: scope, the content, and finally what remains open. Keep documents short enough to
read in full. If one exceeds roughly 200 lines, consider whether it is carrying two
subjects.

**Wrap prose at roughly 95 columns.** This keeps a diff readable: an unwrapped paragraph
changes as one enormous line, so a one-word fix and a rewritten argument look identical in
review. Tables and links may overrun where breaking them would be worse. The rule applies
to **paragraphs you add or edit** — do not reflow a document wholesale to satisfy it, because
a reflow commit buries whatever substantive change travels with it.

## Evidence language

This is the convention most easily broken and the most damaging when it is.

**Never state that something works, boots, is validated, is reproducible, or is supported
unless a dated report in `docs/validation/` demonstrates it.** When you need to describe
intended behavior, mark it as intended:

- Good: "The boot gate passes only when the image reaches an interactive session."
- Good: "Observed in `docs/validation/2026-11-07-host-preflight.md`: compose tools absent."
- Bad: "The image boots to a Plasma session." (no report exists)
- Bad: "The pipeline is reproducible." (reserved for an independent matching rebuild)

See the **Words to avoid** table in [`GLOSSARY.md`](GLOSSARY.md). Prefer naming the limit
over omitting it: a stated limitation is more useful to an implementer than a clean
sentence that overstates what is known.

## Cross-references

- Cite decisions by ID — `DEC-031` — and link to the anchor when the reference carries
  weight: `[DEC-031](DECISIONS.md#dec-031-pin-and-audit-production-build-inputs)`.
- Cite open questions by their stable number. Numbers are never reused or renumbered.
- Every requirement in a specification traces to a DEC ID. A requirement with no citation
  is either an undocumented assumption or a new choice needing approval — not a requirement.
- When citing a **Proposed** decision, mark the containing section provisional and say so
  in the text.
- Prefer upstream documentation links over paraphrase for upstream behavior, and date
  anything release-specific, because it may not hold for the release Axiom builds.
- When a pointer's target moves independently of the citing document, record when the
  citation was last checked: `(citation checked YYYY-MM-DD)`. This records **one thing — that
  the link resolved and still said what the citing sentence claims it says, on that date.**
  It is never evidence that the thing described works, is built, or is validated; only a
  dated report in `docs/validation/` carries that, and "verified" stays reserved per the
  **Words to avoid** table. A stamp older than the document it points at is a prompt to
  re-check, not a defect in itself.

## Decision records

The entry format, the status values, and the amendment rules live in
[`LLM_GUIDE.md`](../LLM_GUIDE.md#decision-records) so there is exactly one copy. In short:
IDs are sequential and permanent; clerical fixes are inline with a dated amendment note;
substantive changes require a new superseding record; only the owner accepts a record.

## Validation reports

One report per attempt, including blocked and failed attempts — a blocker is evidence and
recording it prevents the next contributor repeating it. Use the build report template in
[`IMAGE_PIPELINE.md`](IMAGE_PIPELINE.md#build-report-template) for image-pipeline work.
Open with date, operator, result (`not run` / `blocked` / `failed` / `passed`), and purpose.

Reports are immutable once filed. A later attempt gets a new report; corrections go in the
new one, which references the old.

## Corrections stay visible

When a claim in a register turns out to be wrong, the next reader must meet the correction,
not the stale claim. Deleting the row hides that anything changed and invites the same
mistake again.

Each kind of document already has a mechanism, and keeps it:

| Document | When something changes |
|---|---|
| `DECISIONS.md` | A new record supersedes the old in whole or in part; the old record is never deleted. |
| `OPEN_QUESTIONS.md` | The question moves to **Resolved** with its number intact (owner action). |
| `docs/validation/` | Reports are immutable; a later attempt files a new report naming the old. |
| `REQUIREMENTS.md` and any future register table | **Strike the row through and keep it**, with a dated note saying what replaced it. |

The last line is the one without a prior convention. A requirement row whose claim proves
wrong, or whose decision is superseded, becomes `~~struck~~` text followed by the
correction and its date — for example, "**Corrected 2026-11-12:** this row cited DEC-020's
prohibition on additional Flatpak remotes; DEC-050 permits one, so the requirement now
traces to DEC-050." Keep the row in place rather than deleting or renumbering it, because
other documents may cite its position.

## Keeping the README current

`README.md` carries the project's status and the index of documents. Both drift silently,
so treat them as part of any change:

- **Add a document** → add it to the "Start here" index, and to the reading order in
  `LLM_GUIDE.md` if agents need it. An unreferenced document will not be read.
- **File a validation report** → update "Current status" to match the newest evidence,
  naming the current stage and what blocks it.
- **Accept a decision** that changes the project's direction → check whether "Current
  status" still describes reality.

The README's status must never claim more than the newest report in `docs/validation/`
supports.

## An index is a pointer layer, never the authority

The README's index and status, the reading order in [`LLM_GUIDE.md`](../LLM_GUIDE.md), and
[`REQUIREMENTS.md`](REQUIREMENTS.md) over `DECISIONS.md` are all pointer layers over
something else. Each is a convenience, and **the thing pointed at is always the authority**:
where an index and its source disagree, the source wins and the index is the defect.

Nothing keeps a pointer layer in sync. So **a change to the source changes its index in the
same commit** — a new decision updates the README's status if it changes the project's
direction, a new document earns an index row and a place in the reading order, a superseded
decision updates every requirement row that cites it. An index that has drifted is worse
than no index, because it is read with the same confidence as the source.
