# Axiom OS — agent instructions

Axiom OS is a **documentation-only** repository defining a Linux-based desktop OS
(Fedora Kinoite / KDE Plasma base, rpm-ostree deployments, Rust for new project-owned
system software). No implementation exists yet.

Three rules override anything else you might infer from the documents:

1. **Do not create implementation files.** No treefiles, build scripts, configuration,
   systemd units, or source files until a task explicitly moves into implementation.
2. **You may propose a decision; you may never accept one.** `docs/DECISIONS.md` is the
   authoritative rule source, and the repository owner approves changes to it (DEC-032).
3. **No claim without evidence.** Never state that an image boots, a feature works, or a
   milestone is complete unless a dated report in `docs/validation/` shows it.

Start with `docs/DECISIONS.md`, then `docs/REQUIREMENTS.md`. The full guide follows.

@LLM_GUIDE.md
