# Requirements traceability

Every accepted decision in [`DECISIONS.md`](DECISIONS.md), restated as concrete
requirements with a way to check each one. This is the working checklist for anyone
building Axiom OS: start here to learn *what to produce*, then read the specification
that owns it.

**Status:** mapping document. No requirement below has been demonstrated; nothing here
is a claim that any behavior exists. Verification methods are what a validator must do,
not records of having done it.

## How to use this

- Requirement IDs extend the decision ID (`DEC-009a`, `DEC-009b`) so there is one
  namespace and every requirement names the rule that compels it.
- A requirement with no DEC citation is not a requirement. It is an undocumented
  assumption or a new choice needing owner approval — see [New choices required](#new-choices-required).
- **State** values:

| State | Meaning |
|---|---|
| **Satisfied** | Already true in the repository, with the evidence named. |
| **Open** | Derived from an accepted decision; not yet produced. Build it. |
| **Blocked** | Needs a Proposed decision approved first. Do not guess. |
| **Deferred** | Deliberately out of scope until production or a later stage. |

## Owner documents

The **Owner doc** column uses short names. They resolve as follows; a name with no file yet
is a document this project still owes.

| Short name | File |
|---|---|
| Repo layout | [`REPO_LAYOUT.md`](REPO_LAYOUT.md) |
| Build host | [`BUILD_HOST.md`](BUILD_HOST.md) |
| Treefile spec | [`TREEFILE_SPEC.md`](TREEFILE_SPEC.md) |
| Image pipeline | [`IMAGE_PIPELINE.md`](IMAGE_PIPELINE.md) |
| Update/rollback spec | [`UPDATE_ROLLBACK_SPEC.md`](UPDATE_ROLLBACK_SPEC.md) |
| Rust components | [`RUST_COMPONENTS.md`](RUST_COMPONENTS.md) |
| Testing spec | [`TESTING.md`](TESTING.md) |
| App installation | [`APP_INSTALLATION.md`](APP_INSTALLATION.md) |
| Doc conventions | [`DOC_CONVENTIONS.md`](DOC_CONVENTIONS.md) |
| LLM guide | [`LLM_GUIDE.md`](../LLM_GUIDE.md) |
| Vision | [`VISION.md`](VISION.md) |
| Roadmap | [`ROADMAP.md`](ROADMAP.md) |
| Contributing | [`CONTRIBUTING.md`](../CONTRIBUTING.md) |
| This file | this document |
| Build spec | **not yet written** — build procedure detail beyond `IMAGE_PIPELINE.md` |
| Compatibility note | **not yet written** — required by DEC-034 |
| Production stub | **not yet written** — production-deferred items |
| Per-component specs, All specs, All user-facing docs | review obligations spanning documents, not one file |

## Build pipeline and inputs

| ID | Requirement | DEC | Owner doc | Verify | State |
|---|---|---|---|---|---|
| DEC-008a | Package selection and system configuration live in an Axiom treefile derived from Fedora's Atomic Desktop configuration — not in an Image Builder blueprint | DEC-008 | Treefile spec | Blueprint declares no package set; treefile does | Open |
| DEC-008b | The treefile is versioned at `image/treefile/` | DEC-008, DEC-031, DEC-043 | Repo layout | File tracked in git | Open |
| DEC-008c | The qcow2 artifact is produced from the composed OSTree commit via Image Builder's OSTree input workflow | DEC-008 | Image pipeline | Build log shows commit consumed as input | Open |
| DEC-005a | No Universal Blue image is a required base dependency; it informs practice only | DEC-005 | Treefile spec | Inputs reference Fedora only | Open |
| DEC-031a | Only named Fedora repositories are enabled; repository configuration is in version control | DEC-031 | Treefile spec | Inspect repo config; no unnamed or third-party repo | Open |
| DEC-031b | Production builds generate and strictly enforce an architecture-specific lockfile covering all resolved RPMs | DEC-031 | Build spec | Build fails if an input is unlocked | Deferred (production) |
| DEC-031c | Lockfile changes are reviewed as source changes | DEC-031 | Doc conventions | Lockfile diff appears in review | Deferred (production) |
| DEC-031d | Fedora RPM signatures are verified during composition | DEC-031, DEC-014 | Build spec | Compose log shows verification enabled | Open |
| DEC-031e | Compose and Image Builder tool versions are pinned; a builder container, if used, is digest-pinned | DEC-031 | Build spec | Recorded versions match pins | Deferred (production) |
| DEC-031f | The lockfile workflow is validated on Fedora 45 before production; no production image is published if the toolchain cannot enforce resolved inputs | DEC-031 | Image pipeline | Stage 1 report records lockfile behavior | Open |
| DEC-039a | Prototype builds use the strongest snapshot/locking mechanism the Fedora 45 toolchain can actually enforce, and record what it could not | DEC-039 | Image pipeline | Report names mechanism and its limits | Open |
| DEC-039b | Each prototype build records repository metadata identity and resolved RPM NEVRAs | DEC-039, DEC-041 | Image pipeline | Report fields populated | Open |
| DEC-035a | Fedora 45 is the base release for initial validation; a release-specific blocker is documented rather than silently substituted | DEC-035 | Image pipeline | Report names the Fedora release | Open |
| DEC-030a | x86-64 qcow2 for QEMU is the only supported first-release artifact | DEC-030 | Image pipeline | No other artifact type claimed supported | Open |
| DEC-030b | The supported build and validation environment is an x86-64 Fedora host matching the base release | DEC-030 | Build host | Report records host distribution and release | Open |

## Boot and artifact

| ID | Requirement | DEC | Owner doc | Verify | State |
|---|---|---|---|---|---|
| DEC-004a | First boot means a clean VM reaching an interactive graphical desktop session | DEC-004 | Image pipeline | Screenshot of interactive session from fresh state | Open |
| DEC-004b | The session identifies the Axiom build through `os-release` fields, legible in System Settings without a terminal | DEC-004, DEC-045 | Treefile spec | Screenshot of the About page showing `NAME`, `VERSION_ID`, `BUILD_ID` | Open |
| DEC-011a | The image inherits Fedora Kinoite's UEFI/GPT boot profile, with no custom bootloader changes in the first milestone | DEC-011 | Image pipeline | Partition layout and boot components match Fedora's | Open |
| DEC-038a | One fixed baseline QEMU profile is used for all supported validation reports | DEC-038 | Image pipeline | Report uses the baseline table | Satisfied (`IMAGE_PIPELINE.md` baseline table) |
| DEC-040a | The baseline is 4 vCPUs, 8 GiB RAM, 40 GiB disk, UEFI, standard virtual display, user-mode networking | DEC-040 | Image pipeline | Launch command matches | Satisfied (`IMAGE_PIPELINE.md`) |
| DEC-040b | Exact device and firmware choices are confirmed against Fedora 45 before the baseline is called validated | DEC-040 | Image pipeline | Report records firmware identity and QEMU version | Open |
| DEC-038b | Deviations from the baseline are reported, never silently adopted; higher-resource runs are reported separately | DEC-038 | Image pipeline | Report has a deviations field | Satisfied (template field present) |
| DEC-036a | Secure Boot validation and Axiom Secure Boot signing are out of scope for the prototype, and no Secure Boot support claim is made | DEC-036 | Image pipeline | Gate text disclaims Secure Boot | Satisfied (`IMAGE_PIPELINE.md` scope) |

## System update and rollback

| ID | Requirement | DEC | Owner doc | Verify | State |
|---|---|---|---|---|---|
| DEC-009a | rpm-ostree remains the update and rollback mechanism; no separate update engine is built | DEC-009, DEC-028 | Update/rollback spec | No Axiom update engine exists | Open |
| DEC-009b | Plasma Discover is used for update staging | DEC-009 | Update/rollback spec | Staging path observed in target session | Open |
| DEC-009c | **Unattended system updates are disabled by default**, so the user sees and approves the change first | DEC-009 | Treefile spec | Inspect the auto-update unit/config state in the booted image | Open |
| DEC-009d | A plain-language update summary is shown, and the user is asked before staging and reboot | DEC-009 | Update/rollback spec | Observed in target session | Open |
| DEC-009e | An accessible "undo last system update" action uses the previous deployment | DEC-009 | Update/rollback spec | Action present and returns to prior deployment | Open |
| DEC-009f | The boot-menu fallback is documented for when the updated system cannot start | DEC-009 | Update/rollback spec | Documented and exercised | Open |
| DEC-010a | Rollback is requested through rpm-ostree's system D-Bus API; authorization relies on existing polkit policy | DEC-010 | Rust components | No direct privileged call from the UI | Open |
| DEC-010b | The UI stays unprivileged: no setuid helper, no custom root daemon, no broadened polkit rule | DEC-010 | Rust components | Inspect shipped polkit rules and binaries | Open |
| DEC-010c | Fedora's boundary is preserved: updates for active local users; passwordless rollback only for active local `wheel` administrators | DEC-010 | Update/rollback spec | Test as non-admin and as admin | Open |
| DEC-010d | The UI handles authorization denial, authentication prompts, missing desktop agents, and operation failures | DEC-010 | Update/rollback spec | Each failure state exercised | Open |
| DEC-055a | **Proposed requirement.** The update summary states that nothing changes until reboot, the versions moved between, whether security fixes are included, what changed at system level, and what undo does not restore | DEC-055 (**Proposed**) | Update/rollback spec | Read the summary in the target session against the five required points | Blocked |
| DEC-009g | Rollback is described as restoring the system deployment only — never user files, and not necessarily application data | DEC-009, DEC-018 | Update/rollback spec | Help text states the limit | Open |

## Applications

| ID | Requirement | DEC | Owner doc | Verify | State |
|---|---|---|---|---|---|
| DEC-019a | Discover with its Flatpak backend is the supported graphical path to install, update and remove applications | DEC-019 | App installation | Observed in target session | Open |
| DEC-019b | No Axiom GUI exposes rpm-ostree package layering; the native administrator command stays available | DEC-019 | App installation | No layering UI present; native command still available | Open |
| DEC-020a | The full Flathub remote is enabled by default; exactly one additional remote (ArtCraft's OCI remote) is permitted, and no third | DEC-020, DEC-050 | Treefile spec | `flatpak remotes` in booted image lists exactly these two | Open |
| DEC-020b | The application's source is shown before installation | DEC-020 | App installation | Observed in target session | Open |
| DEC-020c | Flathub verification is presented as publisher identity only, never as a safety certification | DEC-020 | App installation | Review all user-facing wording | Open |
| DEC-020d | A link to the Flathub listing is provided where Discover does not expose verification status | DEC-020 | App installation | Link present and correct | Open |
| DEC-021a | Each Flatpak's declared permissions are its baseline; Axiom adds no global or per-app override that broadens access | DEC-021 | Treefile spec | Inspect shipped Flatpak overrides — expect none broadening | Open |
| DEC-021b | Requested permissions are explained in plain language before the user confirms installation | DEC-021 | App installation | Observed in target session | Open |
| DEC-021c | Portal-mediated, user-selected access is preferred where supported | DEC-021 | App installation | Review per-app behavior | Open |
| DEC-022a | `flatpak-kcm` is present in the package set | DEC-022 | Treefile spec | `rpm -q flatpak-kcm` in booted image | Open |
| DEC-022b | System Settings → Application Permissions → Flatpak Permissions is the supported management path, and its labels and reset behavior are checked in the target session | DEC-022 | App installation | Navigate the path in target session | Open |
| DEC-022c | Flatseal is not installed by default | DEC-022 | Treefile spec | Absent from package set | Open |
| DEC-042a | Brave (`com.brave.Browser`, Flathub) and the ArtCraft launcher (`ai.storyteller.artcraft-launcher`, ArtCraft OCI remote) are available by default | DEC-042, DEC-048 | App installation | Both present in booted image | Open |
| DEC-042b | Each default app is individually uninstallable through Discover, without removing unrelated apps or core components | DEC-042, DEC-048 | App installation | Uninstall each; confirm nothing else is removed | Open |
| DEC-042c | Whether app settings or user data remain after removal is recorded | DEC-042 | App installation | Inspect after uninstall; document | Open |
| DEC-037a | All eight `APP_INSTALLATION.md` criteria pass before first-release readiness is claimed, though they do not block the initial boot and update/rollback demo | DEC-037 | App installation | Criteria checklist complete | Open |

## Security, privacy and keys

| ID | Requirement | DEC | Owner doc | Verify | State |
|---|---|---|---|---|---|
| DEC-012a | Design work covers least privilege, authenticated update inputs, rollback warnings for security fixes, and protection of data at rest | DEC-012 | Per-component specs | Review each component against the threat model | Open |
| DEC-012b | No protection is claimed against an attacker with administrator credentials, a compromised kernel or firmware, or physical control of an unlocked device | DEC-012 | All user-facing docs | Review claims | Open |
| DEC-013a | The prototype QEMU image is described as a development/demo artifact and makes no guest data-at-rest protection claim | DEC-013 | Image pipeline | Review wording | Satisfied (`IMAGE_PIPELINE.md`, DEC-013 text) |
| DEC-013b | Any future physical-install image requires full-disk LUKS2 by default, with clear opt-out and recovery guidance | DEC-013 | Production stub | Installer validation | Deferred (production) |
| DEC-023a | No telemetry, automatic log upload, remote management, SSH, or desktop sharing is enabled by default | DEC-023 | Treefile spec | Enumerate enabled units and listening sockets in booted image | Open |
| DEC-023b | Logs stay local under Fedora's bounded journal defaults | DEC-023 | Treefile spec | Inspect journald configuration | Open |
| DEC-023c | Normal outbound access is preserved for user applications, Discover/Flatpak, and the update workflow | DEC-023 | Treefile spec | No default egress blocking | Open |
| DEC-023d | Any future support-log export is user-initiated, with reviewable contents and explicit confirmation | DEC-023 | Production stub | Review when designed | Deferred |
| DEC-014a | Every release OSTree commit carries a valid signature from an Axiom release key trusted by the Axiom remote | DEC-014, DEC-025 | Production stub | Client rejects unsigned/invalid commits | Deferred (production) |
| DEC-014b | Signature verification is never disabled for a release remote; the installed image pins the public trust key and fails closed | DEC-014 | Production stub | Tamper test | Deferred (production) |
| DEC-014c | Development builds use clearly marked development keys and are not presented as production releases | DEC-014 | Production stub | Key identity visible in build records | Deferred |
| DEC-025a | Production commits are signed with OpenPGP/GPG; the Axiom remote trusts only the Axiom release key, and the key is not added to global system trust | DEC-025 | Production stub | Inspect remote keyring scope | Deferred (production) |
| DEC-015a | Production systems take base deployments only from the Axiom OSTree remote, with no Fedora deployment remote or automatic fallback | DEC-015 | Production stub | Inspect configured remotes | Deferred (production) |
| DEC-015b | Fedora repositories are build inputs only | DEC-015 | Treefile spec | No Fedora deployment remote in the image | Open |
| DEC-016a | Production release signing keys are unavailable to image-build workers; signing is a separate step on reviewed commits by designated maintainers | DEC-016 | Production stub | Review release workflow | Deferred (production) |
| DEC-024a | `SECURITY.md` is published and GitHub private vulnerability reporting is enabled before the first production release | DEC-024 | Production stub | File exists; setting enabled | Deferred (production) |
| DEC-024b | Response targets: acknowledgment 7 days, assessment 14 days, status updates every 30 days | DEC-024 | Production stub | Stated in `SECURITY.md` | Deferred (production) |
| DEC-026a | Each production image ships a machine-readable provenance manifest with a detached OpenPGP signature, covering the listed input and output fields | DEC-026 | Production stub | Manifest present, signed, complete | Deferred (production) |
| DEC-026b | A release maintainer reviews the manifest and artifact identity before signing and publishing | DEC-026, DEC-032 | Production stub | Review recorded | Deferred (production) |

## Release, support and claims

| ID | Requirement | DEC | Owner doc | Verify | State |
|---|---|---|---|---|---|
| DEC-017a | Each release is supported only while its Fedora base release is supported, and its support end date is published | DEC-017 | Production stub | Date published per release | Deferred (production) |
| DEC-018a | A supported migration path is published at least 90 days before end of support, with in-desktop notice and no forced upgrade | DEC-018 | Production stub | Notice timing verified | Deferred (production) |
| DEC-018b | After the support date the installed release is identified as unsupported, with migration instructions | DEC-018 | Production stub | Observed in session | Deferred (production) |
| DEC-034a | Support is claimed only for the documented x86-64 QEMU configuration and the tested workflows | DEC-034 | Compatibility note | Review all claims | Open |
| DEC-034b | A compatibility note lists tested virtual hardware and known limitations | DEC-034 | **Compatibility note — not yet written** | Document exists and matches reports | Open |
| DEC-034c | Configuration formats, command-line interfaces, and project-owned APIs are treated as unstable until a production policy exists; breaking changes are announced | DEC-034 | Doc conventions | Review release notes | Open |
| DEC-003a | The first release is a reproducible x86-64 VM desktop image demonstrating an update that is explained before it is applied, and rollback afterward | DEC-003 | Roadmap, update/rollback spec | Stage 1 and Stage 3 gates | Open |
| DEC-041a | Prototype evidence records source/treefile revision, repository metadata identity, resolved NEVRAs, signing-key fingerprints, tool versions and configuration, OSTree checksum, and qcow2 digest | DEC-041 | Image pipeline | Report fields populated | Satisfied (template) |
| DEC-041b | Prototype evidence is described as traceable inputs, never as reproducible output; that claim awaits an independent matching rebuild | DEC-041, DEC-026 | Image pipeline | Review wording | Satisfied (`IMAGE_PIPELINE.md`) |

## Project, governance and language

| ID | Requirement | DEC | Owner doc | Verify | State |
|---|---|---|---|---|---|
| DEC-001a | Product and usability decisions prioritize desktop newcomers | DEC-001, DEC-002 | Vision | Review | Open |
| DEC-006a | The initial desktop is KDE Plasma on Fedora Kinoite | DEC-006 | Treefile spec | Session is Plasma | Open |
| DEC-028a | The kernel, systemd, glibc, and the rpm-ostree/OSTree stack are inherited and not replaced in the first release | DEC-028 | Treefile spec | No replacement packages in the tree | Open |
| DEC-029a | Rust is used for new Axiom-owned system tools where it fits; upstream components are not rewritten or forked solely to use Rust | DEC-029 | Rust components | Review each proposed component | Open |
| DEC-029b | Every Axiom-owned component has a documented purpose, owner, privilege boundary, and maintenance plan | DEC-029 | Rust components | Component record exists per component | Open |
| DEC-032a | The repository owner approves architecture decisions and prototype releases during prototype development | DEC-032 | LLM guide | Decision records show owner acceptance | Satisfied (`LLM_GUIDE.md`) |
| DEC-032b | Release maintainers and their responsibilities are named and published before production releases | DEC-032 | Production stub | Published | Deferred (production) |
| DEC-027a | "Axiom OS" is used as a working name; the public identity is confirmed before a production release | DEC-027 | Production stub | Review before release | Deferred |
| DEC-033a | Original content is Apache-2.0; contributions are under the same license with no CLA; third-party components retain their licenses | DEC-033 | Contributing | `LICENSE` and `CONTRIBUTING.md` present and consistent | Satisfied (`LICENSE`, `CONTRIBUTING.md`) |
| DEC-012c | Build and update inputs, network content, and third-party applications are treated as untrusted throughout | DEC-012 | All specs | Review each input path | Open |

## Repository, toolchain and verification

| ID | Requirement | DEC | Owner doc | Verify | State |
|---|---|---|---|---|---|
| DEC-043a | Composition inputs, project-owned code, build orchestration and release tooling occupy the reserved paths; directories are created as work reaches them | DEC-043 | Repo layout | Paths match the reserved layout | Open |
| DEC-044a | The production remote is named `axiom`; a development remote, if any, is `axiom-dev` and never publishes under `axiom` | DEC-044 | Treefile spec | Inspect configured remote names | Open |
| DEC-044b | Commits are published to `axiom/<fedora-release>/<arch>/<variant>` | DEC-044 | Treefile spec | Ref matches the scheme | Open |
| DEC-044c | Releases are versioned `<fedora-release>.<serial>`; prototype builds are `<fedora-release>.0-dev.<short-rev>` | DEC-044 | Treefile spec | Commit version metadata matches | Open |
| DEC-045a | `os-release` carries `NAME`, `ID=axiom`, `ID_LIKE=fedora`, `VERSION_ID`, `VARIANT`, `BUILD_ID` (short OSTree checksum), `HOME_URL`, `BUG_REPORT_URL` | DEC-045 | Treefile spec | Read `/etc/os-release` in booted image | Open |
| DEC-045b | No Axiom splash screen, watermark, or About application is added in the first milestone | DEC-045 | Treefile spec | None present | Open |
| DEC-046a | All Axiom crates live in one Cargo workspace under `crates/`, with a committed `rust-toolchain.toml` and `Cargo.lock` | DEC-046 | Rust components | Files present; build uses the pinned toolchain | Open |
| DEC-046b | Every `unsafe` use carries a comment stating the invariant it upholds | DEC-046 | Rust components | Review; lint | Open |
| DEC-046c | Graphical components call documented D-Bus interfaces and never shell out to the `rpm-ostree` CLI | DEC-046 | Rust components | Inspect for process invocation | Open |
| DEC-046d | Logs go to the journal and never contain credentials or full user file paths | DEC-046, DEC-023 | Rust components | Review log output | Open |
| DEC-047a | Every requirement in this file names the check class that verifies it: composition, boot, or interaction | DEC-047 | This file | Each row's Verify column is classifiable | Open |
| DEC-047b | Composition and boot checks live in `build/checks/` behind one entry point | DEC-047, DEC-043 | Testing spec | Entry point runs them | Open |
| DEC-047c | A gate is claimed passed only when every requirement in its scope has a recorded result in a filed report | DEC-047 | Doc conventions | Audit the gate claim against reports | Open |
| DEC-049a | The Axiom treefile `include`s Fedora's Kinoite manifest from a vendored, pinned revision and overrides only Axiom's changes | DEC-049, DEC-008 | Treefile spec | Treefile contains the include; Axiom diff is confined to one file | Open |
| DEC-049b | The pinned Fedora configuration revision is recorded in build evidence and the provenance manifest | DEC-049, DEC-041, DEC-026 | Image pipeline | Revision present in report | Open |
| DEC-050a | Exactly two default Flatpak remotes are enabled: Flathub and ArtCraft's OCI remote | DEC-050 | Treefile spec | `flatpak remotes` lists exactly two | Open |
| DEC-050b | The interface makes clear which remote supplies each application | DEC-050, DEC-020 | App installation | Observed in target session | Open |
| DEC-050c | **The ArtCraft remote's integrity behavior is established before release** — its documentation describes only SHA-256 checksums and names no signing key | DEC-050, DEC-012 | App installation | Inspect the `.flatpakrepo` for a GPG key; test tampered-input behavior | Open |
| DEC-048a | Neither default application is composed into the OSTree commit | DEC-048, DEC-042 | Treefile spec | Absent from the commit; present as Flatpaks | Open |
| DEC-048b | Defaults are seeded at build time; if offline seeding is unavailable on Fedora 45, the network requirement is stated explicitly rather than implying offline capability | DEC-048 | Image pipeline | Boot with networking disabled; observe and document | Open |

## New choices required

The choices discovered by this mapping were drafted as proposals and accepted on
2026-11-12 as DEC-043 through DEC-050. What remains open:

| Needed for | What must be decided | Where |
|---|---|---|
| DEC-009d | What the plain-language update summary must contain | DEC-055 (**Proposed**) — drafted 2026-11-12, awaiting owner approval |
| — | DEC-007's status reads `Superseded` but its text says the proposal was declined; under the status vocabulary that is `Rejected` | status relabel, owner action |

## Coverage

All 55 decision records (DEC-001 … DEC-055) are accounted for above, except DEC-007,
which is superseded and carries no active requirement — it appears in
[New choices required](#new-choices-required) for its status relabel. DEC-020 is
superseded in part by DEC-050; both appear, and the rows cite whichever now governs.
DEC-051 through DEC-055 are **Proposed**, so they carry no binding requirement yet: DEC-055
appears as a Blocked row above, and DEC-051 through DEC-054 govern documentation process and
repository mechanics rather than the product.
Decisions that are purely directional (DEC-001, DEC-002, DEC-005, DEC-012, DEC-027)
appear as review obligations rather than testable artifacts, which is the honest mapping:
they constrain judgment rather than producing a checkable output.

When a decision is added, add its requirements here in the same change. A decision with no
row is a decision no one will implement.
