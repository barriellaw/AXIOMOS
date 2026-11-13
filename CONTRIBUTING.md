# Contributing

Contributions are welcome as the project moves from definition toward implementation. Start with an issue or discussion for substantial changes, especially changes to product scope, architecture, security, or release policy. Record accepted foundational choices in `docs/DECISIONS.md` and update related project documents when needed.

Keep changes focused and explain their purpose. Documentation should distinguish current behavior from goals and proposals. Do not claim that a feature, image, or workflow works unless it has been validated and the evidence is described.

## Commit messages

Write the subject as `type(scope): what changed`, lower case, no trailing period, with the
body saying why. Scope names the document or area touched.

```text
docs(decisions): propose DEC-049, documentation consistency checks
docs(validation): file 2026-11-07 host preflight — result blocked
docs(conventions): wrap rule, citation stamps, correction discipline
chore(repo): normalize text line endings
```

Types in use: `docs` for documentation, `chore` for repository mechanics. A commit that
files a validation report names the result in the subject, because the result is the point
of the report. A commit that adds a decision record names its DEC ID, so the log can be
searched for the change that introduced a rule.

For the branch model — what `dev`, `main` and `testing` are each for, and what must be true
before `dev` merges to `main` — see [DEC-054](docs/DECISIONS.md#dec-054-branch-roles-and-the-merge-gate).
That record is still Proposed, so treat the model as unsettled rather than inferring a
workflow from the current branch state.

By intentionally submitting a contribution for inclusion, you agree to license that contribution under Apache-2.0, without a separate copyright assignment. You retain copyright in your contribution. There is no contributor license agreement. Do not submit material you do not have the right to license this way. Identify third-party material and its license; do not assume this repository's license changes upstream terms.

See [LICENSE](LICENSE) for the full license terms. Project-owned content is under Apache-2.0 unless a file says otherwise.
