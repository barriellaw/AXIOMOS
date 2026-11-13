# Glossary

Use these terms exactly as defined. This project's decisions turn on narrow distinctions —
between an image and a commit, between an update and a rebase, between what is Axiom's and
what is inherited — and a document that blurs them will mislead an implementer.

Where a term names an upstream concept, the authority is the upstream project, not this
file; the definition here records how Axiom uses it.

## Build pipeline

| Term | Meaning |
|---|---|
| **treefile** | The rpm-ostree input file declaring the package set and system configuration to compose. Axiom maintains its own, derived from Fedora's Atomic Desktop configuration (DEC-008). Not an Image Builder blueprint. |
| **compose** | Running `rpm-ostree compose tree` to turn a treefile plus resolved RPMs into an OSTree commit. The step where package selection happens. |
| **OSTree commit** | A content-addressed, versioned filesystem tree, identified by a checksum. The unit Axiom signs (DEC-014) and publishes. Not a disk image. |
| **ref** | A human-readable name pointing at the latest OSTree commit on a branch, e.g. a release stream. The name scheme is not yet decided. |
| **OSTree repo** | The store holding commits and refs, served to clients over HTTP. |
| **remote** | A named, client-side configuration pointing at an OSTree repo, carrying its own trusted signing key. Axiom production systems take base updates only from the Axiom remote (DEC-015). |
| **Image Builder** (osbuild) | Fedora's tool that turns an OSTree commit into a bootable disk artifact. It handles the artifact, not the package set (DEC-008). |
| **blueprint** | Image Builder's input file, covering machine-level setup (files, services, users). It cannot define a package set — hence the separate compose step. |
| **qcow2** | The QEMU disk image format. The only supported first-release artifact (DEC-030). |
| **lockfile** | A file pinning every resolved RPM to an exact version so a build can be repeated. Strictly enforced for production builds (DEC-031); best-effort for the prototype (DEC-039). |
| **NEVRA** | Name-Epoch-Version-Release-Architecture — the exact identity of one RPM. Recorded in build evidence. |
| **provenance manifest** | The machine-readable record of a production build's inputs and outputs, signed separately (DEC-026). |

## System and updates

| Term | Meaning |
|---|---|
| **rpm-ostree** | The hybrid image/package system managing deployments. Inherited from Fedora, not replaced (DEC-028). |
| **deployment** | One bootable, installed instance of an OSTree commit. Several coexist; the previous one is what rollback returns to. |
| **update** | Fetching and staging a newer commit on the **same** ref, applied on reboot. What the first release demonstrates (DEC-003). |
| **rebase** | Switching to a **different** ref — e.g. a new Fedora base release. The end-of-support migration path (DEC-018). Not an update. |
| **upgrade** | Ambiguous in common use. **Avoid it.** Write "update" or "rebase". |
| **rollback** | Making the previous deployment the default boot target again. Restores the **system tree only** — never user files, and not necessarily application data, versions, or permissions (DEC-009). |
| **package layering** | Adding RPMs on top of the base deployment with rpm-ostree. Persists across updates, available to administrators, and deliberately **outside** the supported newcomer workflow (DEC-019). |
| **first boot** | Starting the built image in a clean VM and reaching an interactive graphical session that identifies the Axiom build (DEC-004). |
| **baseline profile** | The single fixed QEMU machine configuration all validation reports use (DEC-038, DEC-040). Deviations are reported, never silently adopted. |

## Applications

| Term | Meaning |
|---|---|
| **Flatpak** | The sandboxed desktop application format. The supported newcomer app path, via Discover (DEC-019). |
| **Flathub** | The Flatpak hosting service. The full remote is the enabled default (DEC-020). |
| **Flatpak remote** | A configured Flatpak source. Only Flathub is enabled by default in the first release. Distinct from an OSTree remote. |
| **verified** (Flathub) | Confirms the publisher owns the app ID — an **identity signal only**. Never describe it as a safety review or certification (DEC-020). |
| **portal** | A mechanism giving a sandboxed app user-mediated access to a resource without broad static permission. Preferred where supported (DEC-021). |
| **override** | A local change to a Flatpak's permissions. Axiom must not silently add overrides that broaden access (DEC-021). |
| **Discover** | KDE's graphical software manager. The supported path for installing, updating, and removing applications. |
| **polkit** | The authorization framework deciding who may perform a privileged action. Axiom relies on Fedora's existing policy and adds no broader rule (DEC-010). |

## Project terms

| Term | Meaning |
|---|---|
| **Axiom-owned** | Written and maintained by this project; needs a documented purpose, owner, privilege boundary, and maintenance plan (DEC-029). |
| **inherited** | Supplied by the Fedora Kinoite base and deliberately not replaced in the first release: kernel, systemd, glibc, rpm-ostree/OSTree (DEC-028). |
| **working name** | "Axiom OS" is provisional, not confirmed public branding and not trademark clearance (DEC-027). |
| **Fedora Atomic Desktop** | Fedora's family of rpm-ostree-based desktop variants. **Kinoite** is the KDE Plasma variant and Axiom's base; **Silverblue** is the GNOME variant and was not selected (DEC-006). |

## Status vocabulary

These words have exactly one meaning each. Do not improvise alternatives.

**Decision records** (`docs/DECISIONS.md`):

| Status | Meaning |
|---|---|
| **Proposed** | Drafted, awaiting owner approval. **Not a rule.** Cite only in sections marked provisional; never implement from it. |
| **Accepted** | A rule. Apply it. Owner action only. |
| **Rejected** | Considered and declined. Retained for the record. |
| **Superseded** | Was accepted, now replaced; names its replacement. |
| **Accepted (superseded in part by X)** | Still a rule, except for one named clause that X replaces. The record states exactly which clause changed, so the rest stays citable. Used when replacing a whole record would discard decisions that still hold — see DEC-020. |

**Validation reports** (`docs/validation/`):

| Result | Meaning |
|---|---|
| **not run** | Not attempted. |
| **blocked** | Attempted; could not proceed. The blocker is the finding, and it is real evidence. |
| **failed** | Ran and did not meet the gate. |
| **passed** | Ran and met the gate, with evidence attached. |

## Words to avoid

| Avoid | Because | Write instead |
|---|---|---|
| "upgrade" | Conflates update with rebase | "update" or "rebase" |
| "reproducible" | Reserved for an independent rebuild producing matching declared outputs (DEC-026, DEC-041) | "traceable inputs", "identified inputs" |
| "supported" | Means validated and documented as such (DEC-030, DEC-034) | "intended", "experimental", "not yet validated" |
| "verified" (of an app's safety) | Flathub verification is publisher identity only | "publisher-verified", "identity confirmed" |
| "secure" (unqualified) | Implies a guarantee outside the threat model (DEC-012) | name the specific property and its limits |
| "works" / "boots" | A claim needing a dated report | "observed in \<report\>", or say it is untested |
