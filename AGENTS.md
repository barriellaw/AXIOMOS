# Agent instructions

Axiom OS is a **documentation-only** repository defining a Linux-based desktop OS
(Fedora Kinoite / KDE Plasma base, rpm-ostree deployments, Rust for new project-owned
system software). No implementation exists yet.

**Read [`LLM_GUIDE.md`](LLM_GUIDE.md) before doing anything else.** It is the canonical
guide for automated contributors: reading order, what you may decide versus what you must
escalate, decision-record conventions, validation reporting, and the documentation-only
gate. This file exists only to point you there.

Three rules override anything else you might infer from the documents:

1. **Do not create implementation files** until a task explicitly moves into implementation.
2. **You may propose a decision; you may never accept one.** `docs/DECISIONS.md` is the
   authoritative rule source; the repository owner approves changes to it (DEC-032).
3. **No claim without evidence** — a dated report in `docs/validation/`.
