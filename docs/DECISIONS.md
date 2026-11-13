# Architecture and project decisions

Record consequential choices here so maintainers and automated contributors can distinguish settled direction from proposals. Use one entry per decision.

## Entry format

### DEC-000: Short title

- **Status:** Proposed / Accepted / Rejected / Superseded
- **Date:** YYYY-MM-DD
- **Context:** What question needs a decision and why?
- **Decision:** What was chosen?
- **Alternatives:** What credible options were considered?
- **Consequences:** What trade-offs, follow-up work, or constraints result?

### DEC-001: Initial audience

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The project needs an initial audience before it can define a useful first release or compare system foundations against user needs.
- **Decision:** The initial audience is desktop newcomers.
- **Alternatives:** Linux-curious power users; developers; other desktop users.
- **Consequences:** Product and usability decisions should prioritize people new to desktop Linux. The audience, problem, and first-release scope are recorded separately; this choice does not imply a distribution base, interface, or feature set.

See [Open questions](OPEN_QUESTIONS.md) for the remaining decision backlog.

### DEC-002: Problem for the initial audience

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** Desktop newcomers need a first-release problem that is specific enough to guide product scope and foundation choices. The existing vision emphasizes understandable controls and recoverable system changes.
- **Decision:** Focus Axiom OS on helping desktop newcomers confidently manage and maintain their computer through clear system controls and recoverable changes, without requiring deep Linux knowledge.
- **Alternatives:** Focus first on making Linux application and game compatibility easier; focus first on installation and hardware setup; choose another concrete newcomer problem.
- **Consequences:** The first useful release should demonstrate understandable system management and recovery. This does not define particular interfaces, technologies, or guarantee that every change can be reversed.

### DEC-003: Smallest useful first release

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** A small, demonstrable release is needed to guide target selection and foundation evaluation while addressing the accepted problem for desktop newcomers.
- **Decision:** Deliver a reproducible x86-64 virtual machine desktop image with an end-to-end system update workflow that explains the update before applying it and demonstrates rollback afterward.
- **Alternatives:** Start with an installable image for physical hardware; focus the first release on installation and hardware setup; provide a desktop image without a recovery workflow.
- **Consequences:** A VM-first scope limits initial hardware variables and makes the outcome easier to demonstrate. The update summary, rollback control, and validation criteria still need implementation design.

### DEC-004: Initial VM target and first boot

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The accepted first release needs a repeatable target and a clear criterion for a successful initial boot.
- **Decision:** Use a QEMU x86-64 virtual machine as the initial target. Define first boot as starting the built image in a clean VM and reaching an interactive graphical desktop session that identifies the Axiom OS build.
- **Alternatives:** Target another x86-64 virtualization platform; define first boot as reaching a text console and defer the graphical session.
- **Consequences:** The first boot milestone can be checked without physical hardware and gives later work a concrete target. The virtual device profile, resource requirements, and exact build identity display remain open implementation details.

### DEC-005: Linux foundation and VM image path

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The initial release needs a maintained desktop foundation with a system update and rollback model, plus a practical route to a reproducible QEMU disk image. See [Foundation research](FOUNDATION_RESEARCH.md).
- **Decision:** Use a Fedora Atomic Desktop as the initial foundation and evaluate Fedora Image Builder for the QEMU disk image. Use Universal Blue as a reference for image-building practices, not as a required base-image dependency.
- **Alternatives:** Use NixOS and its generation-based system model; adopt a Universal Blue image and build ecosystem; assemble a minimal system from upstream components.
- **Consequences:** This reuses a maintained desktop and an update/rollback model aligned with the first-release workflow. Image reproducibility, project customization, and the newcomer-facing rollback experience still require validation.

### DEC-006: Initial desktop environment

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The first-release desktop needs a concrete user-facing environment. Fedora Atomic Desktop provides GNOME through Silverblue and KDE Plasma through Kinoite; their interaction models differ.
- **Decision:** Use Fedora Kinoite with KDE Plasma as the initial desktop environment.
- **Alternatives:** Fedora Silverblue with GNOME; compare both variants in QEMU before selecting.
- **Consequences:** Initial interface and usability work should target KDE Plasma on Kinoite. The choice does not rule out other desktops in later releases. Image reproducibility and the implementation details of the accepted update/rollback workflow remain open.

### DEC-007: Initial VM workflow evaluation image

- **Status:** Superseded
- **Date:** 2026-11-03
- **Context:** Fedora provides a Kinoite 45 QEMU disk image in preview, and Image Builder documents a Kinoite qcow2 type. Axiom still needs to settle a reproducible, project-customized image path before evaluating the desktop and update/rollback workflow.
- **Decision:** The proposal to use a Fedora preview image before settling Axiom's custom image pipeline was declined. Define the reproducible custom image pipeline before workflow evaluation.
- **Alternatives:** Use Fedora's Kinoite QEMU disk image as a temporary evaluation base, while leaving Axiom's first-release artifact decision open.
- **Consequences:** A reproducible custom image pipeline must be validated before VM workflow evaluation. The preview image may still inform research, but it is not the next validation artifact.
- **Status note (2026-11-12, pending owner review):** This record's status reads `Superseded`, but its decision text says the proposal "was declined" and no later record replaces it. Under the status vocabulary in [Glossary](GLOSSARY.md#status-vocabulary) that is `Rejected`. Flagged for owner relabel; not changed here, because a status change is substantive rather than clerical.

### DEC-008: Custom Kinoite image pipeline

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** Axiom needs a reproducible, project-customized Kinoite-compatible qcow2 image for the accepted QEMU target. Image Builder's Kinoite qcow2 blueprint supports machine-level customizations but does not define a custom package set; the system tree must be composed separately.
- **Decision:** Maintain a versioned Axiom treefile based on Fedora's Atomic Desktop configuration, compose an OSTree commit from Fedora inputs, and use Image Builder's OSTree workflow to create the qcow2 artifact.
- **Alternatives:** Build a custom bootable OCI image with a Containerfile and a separate image-to-disk tool; use Fedora's preview qcow2 unchanged and defer project customization.
- **Consequences:** Package selection and system configuration remain in the native OSTree composition model, while Image Builder handles the QEMU disk artifact. The end-to-end local build path, exact input-pinning mechanism, signatures, and reproducibility properties must be validated before implementation.

### DEC-009: System update and rollback experience

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The first release must let desktop newcomers understand and recover from a system update. Fedora Kinoite uses rpm-ostree deployments, and Plasma Discover supports staged system updates. Native rollback remains an administrative action or a boot-menu choice.
- **Decision:** Keep rpm-ostree as the system update and rollback mechanism, and use Plasma Discover for update staging. Axiom should present a plain-language update summary and ask before staging/rebooting, provide an accessible “undo last system update” action using the previous deployment, and document the boot-menu fallback if the updated system cannot start. Disable unattended system updates by default so users see and approve the change first.
- **Alternatives:** Rely on Discover and the boot menu/command line without an Axiom recovery control; build a separate update engine and deployment format; enable unattended system updates by default.
- **Consequences:** This reuses Fedora's deployment behavior and keeps system updates reversible without creating another update engine. Axiom still needs to design and implement an accessible control surface. Rollback restores the previous system deployment; it does not promise restoration of user files or all application data.

### DEC-010: Privilege boundary for rollback controls

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The accepted Axiom rollback control must change the system's default deployment while keeping the desktop application unprivileged. rpm-ostree already exposes a system D-Bus service integrated with polkit.
- **Decision:** Have the Axiom desktop application request rollback through rpm-ostree's system D-Bus API and rely on the existing polkit policy for authorization. Keep the UI unprivileged; add no setuid helper, custom root daemon, or broader polkit rule. Preserve Fedora's boundary: system updates for active local users; rollback without a password only for active local administrators in `wheel`.
- **Alternatives:** Add an Axiom root service or setuid helper; broaden polkit so any local user can roll back; require administrator authentication for every rollback regardless of Fedora's existing policy.
- **Consequences:** The project reuses an established authorization path and avoids another privileged component. The user interface must handle authorization denial, authentication prompts, missing desktop agents, and operation failures. The exact D-Bus method and supported Fedora policy must be confirmed during implementation validation.

### DEC-011: QEMU firmware and boot profile

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The accepted QEMU target still needs a firmware mode and boot layout. Fedora's documented Kinoite qcow2 image uses UEFI/GPT and Fedora's GRUB EFI/shim packages.
- **Decision:** Inherit Fedora Kinoite's UEFI/GPT qcow2 boot profile for the initial QEMU image and make no custom bootloader changes in the first milestone.
- **Alternatives:** Use a legacy BIOS boot profile; define a project-specific bootloader and partition layout.
- **Consequences:** This keeps Axiom close to Fedora's supported desktop image and reduces boot integration work. Validate the profile for the selected Fedora release; Secure Boot signing and key ownership remain separate open decisions.

### DEC-012: Initial security threat model

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** Security defaults and update/recovery design need a clear scope for an OS aimed at desktop newcomers. The threat model should identify protected assets, likely attack paths, and limits rather than promise universal security. NIST's [data-centric threat-modeling guidance](https://csrc.nist.gov/pubs/sp/800/154/ipd) recommends starting from the data and system elements being protected.
- **Decision:** Protect user files and credentials, system integrity and availability, and the authenticity of Axiom system images and updates. Treat network content, third-party applications, and build/update inputs as untrusted; include ordinary local applications and opportunistic attackers in scope. Include data exposure if a device is stolen while powered off. Do not claim protection against an attacker with administrator credentials, a compromised kernel or firmware, or physical control of an unlocked device.
- **Alternatives:** Limit the initial scope to system-image integrity and defer user-data confidentiality; include targeted firmware/hardware attacks and privileged-account compromise in the first-release threat model.
- **Consequences:** Design work must cover least privilege, authenticated update inputs, rollback warnings for security fixes, and protection of data at rest. Signing-key ownership and the depth of supply-chain compromise mitigation still need explicit decisions. Revisit the model as the product and supported devices expand.

### DEC-013: Data-at-rest encryption direction

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The accepted threat model includes disclosure of data from a powered-off stolen device. The selected first milestone is a QEMU qcow2 image, and the current Image Builder Kinoite qcow2 profile documents unencrypted EFI, `/boot`, and root partitions without a LUKS blueprint option. Fedora's Kinoite installer is a separate Anaconda-based path and needs its own validation before it can be used for physical installation.
- **Decision:** Require full-disk LUKS2 encryption by default for any future Axiom physical-install image, with a clear opt-out and recovery guidance. Treat the initial QEMU image as a development/demo artifact, not as a secure container for real user data or credentials; do not claim it protects its guest data at rest.
- **Alternatives:** Defer an encryption requirement until physical installation enters scope; attempt to add encryption directly to the custom qcow2 composition pipeline before validating that path.
- **Consequences:** The physical-install path must prove encryption coverage, boot/recovery behavior, and usable key recovery before release. The initial VM artifact remains useful for update/rollback demonstrations but does not satisfy the theft-at-rest protection goal. Host-side qcow2 encryption is not a substitute for guest-visible full-disk encryption.

### DEC-014: Authenticate Axiom system updates

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The accepted threat model treats build and update inputs as untrusted, and the selected delivery model publishes Axiom OSTree commits for rpm-ostree deployments. OSTree supports signed commits and remote-specific trusted keys; GPG commit verification is enabled by default, but an Axiom release must explicitly establish and retain its own trusted signing identity. See the [OSTree repository configuration reference](https://ostreedev.github.io/ostree/man/ostree.repo-config.html) and [commit signing documentation](https://ostreedev.github.io/ostree/man/ostree-commit.html).
- **Decision:** Require every Axiom release OSTree commit to carry a valid signature from an Axiom release key trusted by the Axiom remote. Keep Fedora RPM signature verification enabled for Fedora package inputs. Never disable signature verification for a release remote. Development builds may use clearly marked development keys and must not be presented as production Axiom releases.
- **Alternatives:** Rely only on HTTPS transport; trust Fedora's key for Axiom-produced commits; ship release commits without verification while the project is small.
- **Consequences:** Release tooling must sign commits, and the installed image must pin the corresponding public trust key and fail closed on missing or invalid signatures. Define key custody, offline protection, rotation, revocation, recovery, and reproducible-build/provenance practices before distributing production releases.

### DEC-015: Production system update source

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** Axiom's accepted treefile → OSTree commit → Image Builder pipeline produces a project-specific system tree, and DEC-014 requires Axiom-signed release commits. rpm-ostree treats the base image as a tested unit; Fedora package layering is a separate optional mechanism. See the [rpm-ostree architecture](https://coreos.github.io/rpm-ostree/architecture-core/) and [OSTree remote configuration](https://ostreedev.github.io/ostree/man/ostree.repo-config.html).
- **Decision:** Configure production Axiom systems to receive base-system deployments only from the Axiom OSTree remote and require DEC-014 signature verification. Keep Fedora repositories as build inputs, verifying Fedora RPM signatures during composition; do not configure a direct Fedora deployment remote or automatic fallback for Axiom base updates. Treat package layering as a separate, explicitly designed administrator feature, not as the base update path.
- **Alternatives:** Let production systems rebase directly to Fedora Kinoite; allow both Fedora and Axiom remotes to supply base updates; use Fedora updates as an automatic fallback when Axiom has no release.
- **Consequences:** Axiom takes responsibility for composing, testing, signing, and publishing timely system updates. Build records should identify source RPMs and repository state. Decide separately whether package layering is available, which repositories it may use, and how those changes are explained and recovered.

### DEC-016: Separate release signing from image builds

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** Production Axiom OSTree commits must be signed, while the accepted build pipeline composes commits from external Fedora inputs. If the private release key is available to build workers, compromise of a worker could permit an attacker to publish a trusted malicious system image.
- **Decision:** Keep production release signing keys unavailable to image-build workers. Sign only reviewed, identified release commits in a separate release step, under access limited to designated release maintainers. Before public releases, document key backup, revocation, and a client trust-transition procedure.
- **Alternatives:** Store the release key in the build environment for automatic signing; let each developer sign releases with a personal key; defer key custody controls until after initial distribution.
- **Consequences:** Production builds and publication require a deliberate separate signing step. The project must choose a supported signing-key format, secure key storage and backup, maintainer access rules, and a tested response if a key is lost or compromised.

### DEC-017: Support window follows Fedora base support

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** Axiom's production base updates will be built from Fedora inputs, so continuing to ship updates after the Fedora base release stops receiving maintenance would require Axiom to take over maintenance of the unsupported upstream packages. Fedora publishes a release lifecycle and identifies end-of-life releases as no longer receiving updates. See [Fedora release lifecycle](https://docs.fedoraproject.org/en-US/releases/lifecycle/) and [Fedora end-of-life support](https://fedoraproject.org/wiki/Fedora-EOL-Support).
- **Decision:** Support each Axiom production release only while its Fedora base release remains supported. Publish the support end date for every Axiom release, and provide no longer-term support promise until Axiom has demonstrated the maintainer capacity to meet it.
- **Alternatives:** Continue supporting Axiom releases beyond Fedora's support window; promise a fixed multi-year support term now; publish releases without a stated support end date.
- **Consequences:** Release planning must track Fedora's lifecycle and provide a migration path before the base reaches end of life. Axiom must stop presenting an expired release as supported and must explain the user's next supported upgrade path.

### DEC-018: End-of-support migration notice

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** DEC-017 ties Axiom support to the Fedora base lifecycle. rpm-ostree supports rebasing between vendor-provided branches and stages the new deployment for reboot; Axiom has not yet validated a cross-release upgrade or its rollback behavior. See the [rpm-ostree administrator handbook](https://coreos.github.io/rpm-ostree/administrator-handbook/).
- **Decision:** Publish a supported migration path to the next Axiom release at least 90 days before the current release reaches end of support. Notify users in the desktop at that point and again as end of support approaches. Do not force an upgrade; after the support date, identify the installed release as unsupported and direct users to the supported migration instructions.
- **Alternatives:** Rely on users to notice release announcements; force migration before Fedora end of life; stop updates without in-product notice or upgrade guidance.
- **Consequences:** Release engineering must deliver and validate the next signed deployment, upgrade instructions, and rollback behavior before the notice window starts. The notice must explain that system rollback does not reverse changes to user files or all application data, and users should have a backup before a major release transition.

### DEC-019: Application installation boundary

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** Axiom's initial audience is desktop newcomers, and production base updates are delivered as tested, signed Axiom deployments. rpm-ostree supports transactional package layering, but layered packages persist across upgrades and rebases and create a second source of system changes to explain and recover. KDE Discover has a Flatpak backend for finding and installing desktop applications. See the [rpm-ostree administrator handbook](https://coreos.github.io/rpm-ostree/administrator-handbook/) and [KDE Discover](https://apps.kde.org/discover/).
- **Decision:** In the first release, support desktop application installation through Discover using Flatpak. Do not add an Axiom GUI for rpm-ostree package layering; leave the native administrator command available but outside the supported newcomer workflow. Choose and document the default Flatpak remote separately before distribution.
- **Alternatives:** Expose RPM package layering in the Axiom newcomer interface; remove or disable native rpm-ostree layering; defer graphical application installation.
- **Consequences:** The first-release app catalog must be usable through Discover and its configured Flatpak backend. Users who layer system packages through native tools may receive upstream rpm-ostree behavior, but Axiom's beginner-facing update and recovery guidance will not cover those customizations until that path is designed.

### DEC-020: Default Flatpak application source

- **Status:** Accepted (superseded in part by [DEC-050](#dec-050-permit-the-artcraft-oci-remote-as-a-second-default-application-source))
- **Date:** 2026-11-03
- **Superseded in part (2026-11-12):** the clause "Do not enable additional Flatpak remotes by default in the first release" is replaced by DEC-050, which permits one named additional remote. Every other part of this record stands.
- **Context:** DEC-019 selects Discover and Flatpak for desktop application installs, but the source needs a clear default. Flathub is Flatpak's primary publishing and hosting service. Its full catalog offers broad availability; its verified subset limits installs to apps whose publisher has confirmed ownership of the app ID. Verification establishes publisher identity, not an Axiom security review or a guarantee that an app is safe. KDE Discover supports a Flatpak backend. Flathub documents verified-badge display on Flathub's site and GNOME Software, so Axiom should not assume that Discover exposes this status. See [Flatpak repositories](https://docs.flatpak.org/en/latest/repositories.html), [Flathub verification](https://docs.flathub.org/docs/for-users/verification), and [Flathub safety overview](https://docs.flathub.org/blog/app-safety-layered-approach-source-to-user).
- **Decision:** Enable the full Flathub remote by default in Discover to provide a broad application catalog. Do not describe Flathub verification as a safety certification. Explain the app's source and requested permissions before installation, and provide a link to the Flathub listing so users can check publisher verification where available. Do not enable additional Flatpak remotes by default in the first release.
- **Alternatives:** Enable only Flathub's verified subset by default and require an explicit step to access the full catalog; enable Fedora and Flathub remotes together; defer a default remote until app installation is implemented.
- **Consequences:** Axiom must validate that Discover presents clear source and permission information, and add a simple explanation/link if it does not. The full catalog includes community-maintained applications that are not publisher-verified; users need an accessible explanation of that distinction and how to choose.

### DEC-021: Flatpak permission baseline

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The accepted threat model treats third-party applications as untrusted, and the first-release app source is the full Flathub catalog. Flatpak applications run in sandboxes, but access to files, devices, and host services depends on granted permissions; portals can provide user-mediated access to resources without granting broad static access. See [Flatpak sandbox concepts](https://docs.flatpak.org/en/latest/basic-concepts.html) and [Flathub permission guidance](https://docs.flathub.org/docs/for-users/permissions).
- **Decision:** Keep the permissions declared by each Flatpak as its baseline; Axiom must not silently add global or per-app overrides that broaden access. Prefer portal-mediated, user-selected access to files and devices where supported. Explain static permissions and their practical effect before installation, and let users inspect or change permissions through supported desktop tools.
- **Alternatives:** Automatically grant broad access to avoid app compatibility problems; remove permissions from apps globally regardless of function; leave permissions unexplained and rely on users to discover tools themselves.
- **Consequences:** Axiom must validate which permissions and source details Discover exposes, add plain-language guidance where needed, and document the supported permission-management path. Some apps may not work fully under their declared permissions; users may need to make a deliberate permission change.

### DEC-022: Flatpak permission-management tool

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** DEC-021 requires users to inspect and change Flatpak permissions through a supported desktop tool. KDE provides a Flatpak permissions module for System Settings (`flatpak-kcm`), while Flatseal is a separate graphical application. Integrating permission controls into the existing settings app should be easier for newcomers to find and avoids requiring a separate tool. See [KDE's Flatpak KCM](https://invent.kde.org/plasma/flatpak-kcm), the [Fedora package](https://packages.fedoraproject.org/pkgs/flatpak-kcm/flatpak-kcm/), and [Flathub's permission guidance](https://docs.flathub.org/docs/for-users/permissions).
- **Decision:** Include Fedora's `flatpak-kcm` package and recommend System Settings → Application Permissions → Flatpak Permissions as Axiom's supported way to inspect and change Flatpak permissions. Do not install Flatseal by default; users may install it from Discover if they prefer its separate interface.
- **Alternatives:** Install Flatseal by default and make it the supported tool; recommend command-line `flatpak override`; provide no GUI permission-management path.
- **Consequences:** The selected Fedora release must provide a compatible `flatpak-kcm`, and the path, labels, permission descriptions, and reset behavior must be checked in the target Plasma session. If the module is absent or confusing, the release must not claim the permission-management criterion is met.

### DEC-023: Local-first diagnostics and remote access

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The initial product serves desktop newcomers and has no staffed remote-support operation. systemd-journald stores logs locally and applies configured size limits; KDE Desktop Sharing starts its server only when the user enables sharing. Remote access and log uploads therefore need explicit user action rather than being part of the default image. See [systemd journal storage](https://www.freedesktop.org/software/systemd/man/258/journald.conf.html) and [KDE Desktop Sharing](https://docs.kde.org/stable_kf6/en/krfb/krfb/using-krfb.html).
- **Decision:** Keep system logs local under Fedora's bounded journal defaults. Do not add Axiom telemetry, automatic log uploads, remote management, SSH access, or desktop-sharing services enabled by default. If Axiom later adds a support-log export, require the user to start it, review what will be included, and confirm before sharing. Preserve normal outbound network access for user applications, Discover/Flatpak, and the accepted Axiom update workflow.
- **Alternatives:** Add opt-out telemetry and automatic diagnostic uploads; enable remote support services by default; block most outbound networking except system updates.
- **Consequences:** Support instructions must work without remote access. Axiom must document how users can intentionally enable KDE Desktop Sharing or other remote services, and explain that logs can contain sensitive system details. Any support bundle needs a reviewable contents list and explicit sharing step before it is offered.

### DEC-024: Vulnerability reporting and response targets

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The repository is hosted on GitHub, has no `SECURITY.md`, and has no published vulnerability contact path. GitHub offers private vulnerability reporting for public repositories. CISA's vulnerability-reporting guide describes seven days as a standard target for a personal acknowledgment; this is a reference point, not a service-level guarantee. See [GitHub private reporting](https://docs.github.com/en/code-security/how-tos/report-and-fix-vulnerabilities/configure-vulnerability-reporting/configure-for-a-repository) and [CISA reporting guidance](https://www.cisa.gov/sites/default/files/publications/guide-vulnerability-reporting-americas-election-admins_508.pdf).
- **Decision:** Before Axiom publishes a production release, publish a `SECURITY.md` and enable GitHub private vulnerability reporting. Target acknowledgment within 7 calendar days, an initial severity/scope assessment within 14 calendar days, and status updates at least every 30 days while a report remains open. Prioritize fixes for all supported Axiom releases. Coordinate disclosure with the reporter; do not promise a fixed remediation deadline, but publish a security advisory and available mitigation when a fix is released.
- **Alternatives:** Accept reports only through public issues; promise a fixed patch deadline regardless of severity or maintainer capacity; defer a reporting policy until after the first vulnerability is reported.
- **Consequences:** Maintainers must watch and route GitHub security notifications so private reports are not missed. The security policy must explain scope, supported releases, response targets, coordinated disclosure, and how users receive fixes. Targets are goals, not a staffed 24/7 response commitment.

### DEC-025: OSTree release-signature format

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** DEC-014 requires signed Axiom commits, but does not choose a signature format. OSTree supports per-remote OpenPGP commit verification, enabled by default for GPG, and a separate Ed25519 verification engine. A per-remote keyring keeps Axiom trust scoped to Axiom's production remote. See the [OSTree remote configuration reference](https://ostreedev.github.io/ostree/man/ostree.repo-config.html) and [GPG verification documentation](https://ostreedev.github.io/ostree/man/ostree.html).
- **Decision:** Sign production Axiom OSTree commits with OpenPGP/GPG. Configure the Axiom remote to require a valid GPG signature and trust only the Axiom release key for that remote; do not add the key to global system trust. Keep Fedora RPM verification separate and enabled during composition.
- **Alternatives:** Use OSTree's Ed25519 signature engine; rely on HTTPS without signed commits.
- **Consequences:** Build and client validation must confirm the selected Fedora OSTree/rpm-ostree versions support the chosen per-remote GPG setup. Key storage, backup, rotation, revocation, and recovery still require a separate policy.

### DEC-026: Production build provenance record

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The accepted image pipeline composes Fedora package inputs into an Axiom OSTree commit and then produces a qcow2 artifact. Build-input provenance is needed to investigate compromised inputs and reproduce a release. Exact input pinning and reproducibility remain unvalidated.
- **Decision:** Publish a machine-readable provenance manifest with each production image and a detached OpenPGP signature over the manifest. Record the Axiom source/treefile commit, Fedora repository metadata identity and package NEVRAs, package-signing key fingerprints, compose and Image Builder versions/configuration, OSTree commit checksum, qcow2 SHA-256 digest, and release signing-key fingerprint. A release maintainer must review the manifest and resulting image identity before signing and publishing. Describe a release as reproducible only after an independent rebuild produces matching declared outputs.
- **Alternatives:** Provide only human-written release notes; require a full SLSA/in-toto attestation service before the first release; publish images without input records.
- **Consequences:** The build pipeline must collect input and output metadata without exposing secrets or host-specific sensitive data. The release workflow gains a review step. The manifest improves traceability but is not proof that the source or build process is benign; independent rebuilds and deeper supply-chain controls remain separate work.

### DEC-027: Working project name

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** Project documents currently use Axiom OS, but its identity is not confirmed as the final public name. Renaming during early project definition is less costly than changing a product identity after release materials and user recognition accumulate.
- **Decision:** Continue using Axiom OS as the working name for project definition and prototype development. Confirm the final public name and identity before a production release; do not treat this decision as brand or trademark clearance.
- **Alternatives:** Commit to Axiom OS as the permanent public name now; switch to a new codename for all project work.
- **Consequences:** Internal documentation and prototypes can use the current name. Public branding and release materials remain provisional until the name is reviewed and confirmed.

### DEC-028: Inherit Fedora Kinoite base components

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** Fedora Kinoite is the selected initial foundation, and the project is still validating its custom image pipeline. Replacing foundational components such as the kernel, init/service manager, libc, or native deployment stack would create compatibility and maintenance work before Axiom has demonstrated a specific need.
- **Decision:** In the first release, inherit the core components supplied by the selected Fedora Kinoite base, including its Linux kernel, systemd service manager, glibc, and rpm-ostree/OSTree deployment stack. Replace none of these in the first release. Consider a replacement only when a documented user or technical requirement justifies the maintenance and compatibility cost.
- **Alternatives:** Replace foundational components as part of establishing Axiom's distinct identity; choose replacements independently before validating the Fedora-derived image path.
- **Consequences:** The initial system stays close to Fedora's tested desktop and update behavior. Axiom-specific Rust software should integrate through established interfaces rather than fork or replace foundational components by default.

### DEC-029: Rust and upstream component ownership

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The project prefers Rust for new system software, but its initial foundation already provides mature Linux components in C and other languages. Rewriting those components would increase maintenance and compatibility costs without necessarily helping the accepted newcomer-focused release.
- **Decision:** Use Rust for new Axiom-owned system tools and services when it fits the requirement and the needed interfaces and libraries are mature enough. Prefer established upstream components for existing system responsibilities; do not rewrite or fork them solely to use Rust. Give every Axiom-owned component a documented purpose, owner, privilege boundary, and maintenance plan.
- **Alternatives:** Require all project code to be Rust regardless of fit; rewrite inherited components incrementally; avoid project-owned system software entirely.
- **Consequences:** Axiom can add Rust software where it provides a clear benefit while keeping the platform compatible with Fedora and KDE. Each proposed component still needs a user-facing need and lifecycle justification before implementation.

### DEC-030: First-release artifact and build host

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The accepted first release targets an x86-64 QEMU desktop and the accepted image pipeline ends in a Kinoite-compatible qcow2. Image Builder supports Fedora Kinoite qcow2 targets, and its documented build environment and target availability depend on the host and tool versions. See the [Image Builder Kinoite image description](https://osbuild.org/docs/user-guide/image-descriptions/fedora-44/kinoite-qcow2/), [host installation guide](https://osbuild.org/docs/developer-guide/projects/image-builder/installation/), and [target overview](https://osbuild.org/docs/user-guide/image-descriptions/).
- **Decision:** Make x86-64 qcow2 for QEMU the only supported first-release image artifact. Document an x86-64 Fedora Linux host matching the selected Fedora base release as the supported local build and validation environment. Other host operating systems and image formats remain unsupported until the complete custom pipeline is validated there.
- **Alternatives:** Support multiple disk-image formats and host operating systems in the first milestone; require every contributor to use a Fedora virtual machine as their development host; provide only a source build environment and leave artifact support open.
- **Consequences:** Build instructions and release validation can stay focused on one host/target pair. Contributors using other environments may still experiment, but Axiom will not claim those builds are supported until they are documented and validated.

### DEC-031: Pin and audit production build inputs

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The accepted treefile-to-OSTree pipeline resolves Fedora RPM dependencies, then Image Builder consumes an OSTree commit. rpm-ostree recommends keeping treefiles in Git and supports exact package requests and lockfiles; its `lockfile-repos` option is documented as experimental. Fedora CoreOS uses architecture-specific package lockfiles as part of its compose process. Fedora RPM signatures provide a check that packages came from Fedora and were not altered. See the [rpm-ostree treefile reference](https://coreos.github.io/rpm-ostree/treefile/), [Fedora CoreOS configuration](https://github.com/coreos/fedora-coreos-config), and [Fedora package verification guidance](https://fedoraproject.org/en/security/).
- **Decision:** Keep the Axiom treefile and explicit repository configuration in version control, enabling only named Fedora repositories. For production builds, generate and strictly enforce an architecture-specific lockfile covering all resolved RPMs; review lockfile changes as source changes. Verify Fedora RPM signatures. Pin the compose and Image Builder tool versions, and use a digest-pinned builder container where a container is used. Record repository metadata identity, resolved package NEVRAs and signing-key fingerprints, tool versions, OSTree checksum, and qcow2 digest in the signed DEC-026 provenance manifest. Validate the lockfile workflow on the selected Fedora base before production; do not publish a production image if the chosen toolchain cannot enforce the resolved inputs.
- **Alternatives:** Resolve against current Fedora repositories for each build and record only the resulting package list; pin only top-level packages; copy and maintain a private mirror of all Fedora RPMs before the first release.
- **Consequences:** Production package updates require an explicit lockfile refresh and review, with RPM signature checks and provenance records. The first release depends on validating rpm-ostree lockfile support in its selected version; if that path is unstable, an equally strict alternative must be established before production publishing.

### DEC-032: Architecture and release approval

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** Axiom has no formal governance or maintainer group yet. Its architecture decisions and security-sensitive release obligations need an accountable approver without assuming a larger team exists. The project already records decisions in this file, separates release signing from image builds, and requires a reviewed provenance manifest.
- **Decision:** During prototype development, the repository owner acting as primary maintainer approves architecture decisions and prototype releases; record consequential architecture decisions in this log. Before production releases, name the release maintainers and publish their responsibilities. A production release requires a designated release maintainer to review the provenance manifest and artifact identity before signing and publication.
- **Alternatives:** Require a multi-person committee for every decision from the start; let contributors merge architecture changes without an identified approver; leave release authority implicit.
- **Consequences:** The prototype stage has a clear decision owner while the project is small. Governance must be revisited when additional maintainers join, and production release responsibilities must be published before the first production image.

### DEC-033: Repository license and contribution terms

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The repository has no license or contribution policy yet. Contributors need clear permission to use and modify Axiom's original work, and maintainers need a consistent rule for accepting contributions. The operating system also includes Fedora, KDE, and other upstream components that retain their own licenses.
- **Decision:** License original Axiom repository content and project-owned code under Apache License 2.0. Require contributions to be submitted under the same license, without a separate copyright assignment. Track third-party components and notices under their respective upstream licenses; this repository license does not relicense them. Add the license text and a concise contribution guide before accepting external contributions.
- **Alternatives:** Use MIT; use GPL-3.0-or-later for all original repository content; defer a license while accepting contributions.
- **Consequences:** Apache-2.0 provides explicit copyright and patent grants while allowing reuse under stated conditions. Maintainers must distinguish original Axiom work from third-party content and include required notices. The project must add the license file before soliciting contributions.

### DEC-034: Prototype compatibility and stability claims

- **Status:** Accepted
- **Date:** 2026-11-03
- **Context:** The first useful release is a QEMU qcow2 prototype built on Fedora Kinoite, while broader hardware, application, and upgrade behavior has not yet been validated. Axiom should describe what has been tested without implying compatibility or long-term stability beyond the evidence.
- **Decision:** For the prototype, claim support only for the documented x86-64 QEMU configuration and the tested first-boot, update, and rollback workflow. Publish a compatibility note listing tested virtual hardware and known limitations. Do not promise compatibility with arbitrary physical hardware, Windows applications or games, or future Axiom releases. Treat configuration formats, command-line interfaces, and project-owned APIs as unstable until a production release policy is adopted; announce breaking changes in prototype notes.
- **Alternatives:** Promise broad PC and application compatibility from the first image; promise stable interfaces before they exist; publish no compatibility information until production.
- **Consequences:** Claims stay tied to repeatable validation and newcomers can see the prototype's limits. Physical hardware and application compatibility require separate testing before any support claim, and production must define its own compatibility and stability policy.

### DEC-035: Initial Fedora release

- **Status:** Accepted
- **Date:** 2026-11-05
- **Context:** The custom Kinoite image pipeline needs a concrete Fedora release for its first QEMU validation. Fedora 45 is the current stable release, while Fedora 44 is the previous stable release and Fedora 45's release-specific image path still needs validation.
- **Decision:** Use Fedora 45 as the base release for the initial Axiom image-pipeline validation and prototype. Do not treat this as a permanent release-tracking policy; select and document the base release for each later Axiom release based on support status and pipeline validation.
- **Alternatives:** Start with Fedora 44 to reduce immediate change; wait for a later Fedora release; use a development stream.
- **Consequences:** The first validation must confirm Fedora 45 Kinoite inputs, tree composition, lockfile behavior, and Image Builder qcow2 output on the supported build host. If a release-specific blocker prevents that path, document the evidence and return with a scoped alternative rather than silently changing the base.

### DEC-036: Secure Boot prototype scope

- **Status:** Accepted
- **Date:** 2026-11-05
- **Context:** The initial QEMU prototype inherits Fedora Kinoite's UEFI/GPT boot profile, while Secure Boot validation and Axiom signing key ownership require additional setup and policy.
- **Decision:** Defer Secure Boot validation and Axiom-owned Secure Boot signing from the initial image-pipeline prototype. Revisit Secure Boot closer to public release, before making any public support or security claim.
- **Alternatives:** Validate Secure Boot in the first QEMU boot gate using Fedora's existing trust chain; introduce Axiom-owned signing keys during prototype development.
- **Consequences:** The prototype boot gate uses UEFI without a Secure Boot support claim. Before public release, decide the supported Secure Boot configuration, signing-key custody and rotation, and validation procedure. This does not change the requirement for authenticated Axiom OSTree updates in DEC-014.

### DEC-037: Flatpak acceptance gate

- **Status:** Accepted
- **Date:** 2026-11-05
- **Context:** The first useful release includes a newcomer-facing system update and rollback workflow, while the application-installation contract contains additional Flatpak source, permission, and application-management requirements.
- **Decision:** Keep the full `APP_INSTALLATION.md` acceptance contract as a requirement for first-release readiness, but do not make it a blocker for the initial boot and system update/rollback workflow demo. Validate the application path after that focused demo and before declaring the first release ready.
- **Alternatives:** Require every Flatpak criterion before the initial system workflow demo; defer some application-installation criteria beyond the first release.
- **Consequences:** The first demo can focus on boot and system recovery, while release readiness still requires all seven application-installation criteria to pass in the target QEMU desktop. Do not describe the broader first release as complete until they pass.
- **Amended 2026-11-12 (clerical):** the criterion count, written as six, is now eight. DEC-042 added the default-app uninstall criterion and DEC-050 added the application-source criterion, both after this record was written. The count is bookkeeping; this record's decision is unchanged.

### DEC-038: Repeatable QEMU baseline profile

- **Status:** Accepted
- **Date:** 2026-11-05
- **Context:** QEMU validation needs a stable virtual hardware configuration so build and boot results can be compared. Contributors may need more resources for local experimentation.
- **Decision:** Define one fixed baseline QEMU machine profile for supported build and validation reports. Document optional higher-resource settings separately; do not treat them as the baseline or claim them as separately supported configurations without validation.
- **Alternatives:** Use a minimum profile with many freely varying devices and resource settings; document only a recommended profile without a fixed baseline.
- **Consequences:** The concrete CPU, memory, disk, display, and network values remain to be selected from the Fedora 45 image requirements and recorded in build instructions. Validation reports must identify deviations from the baseline.

### DEC-039: Prototype Fedora input retention

- **Status:** Accepted
- **Date:** 2026-11-07
- **Context:** The Fedora 45 prototype needs sufficiently stable inputs to investigate and repeat image builds, while production infrastructure and a complete repository mirror are out of scope. DEC-031 defines stricter production input controls.
- **Decision:** For prototype builds, use the strongest repository snapshot and package-locking mechanism that the selected Fedora 45 toolchain can actually enforce. Record repository metadata identity and resolved RPM NEVRAs for each build, and retain metadata and package inputs where practical. Do not require a private full mirror before pipeline validation; document inputs that could not be retained and measure the limits of the mechanism.
- **Alternatives:** Record only repository URLs and build time; establish and maintain a complete local mirror before the first build.
- **Consequences:** Early builds can proceed without mirror infrastructure while producing useful provenance. Retention is not guaranteed until validated, and this prototype policy does not satisfy the production lockfile and provenance requirements in DEC-031 and DEC-026.

### DEC-040: Prototype QEMU baseline values

- **Status:** Accepted
- **Date:** 2026-11-07
- **Context:** DEC-038 requires one fixed QEMU machine profile, but the initial profile values were open. A modest, explicit developer profile is needed to make image validation comparable.
- **Decision:** Define one fixed x86-64 developer profile with 4 vCPUs, 8 GiB RAM, a 40 GiB disk, UEFI firmware, standard virtual display hardware, and user-mode networking. Confirm the profile against Fedora 45 image requirements during pipeline validation and record exact QEMU device and firmware choices in the build instructions. Optional higher-resource settings may be documented separately and are not supported baseline profiles.
- **Alternatives:** Copy Fedora's documented profile verbatim; establish separate minimum and recommended profiles before the first image.
- **Consequences:** Reports can compare results using a concrete resource profile while preserving room for local experimentation. Exact device and firmware implementation details must be confirmed against Fedora 45 and documented before claiming the baseline validated.

### DEC-041: Prototype reproducibility evidence

- **Status:** Accepted
- **Date:** 2026-11-07
- **Context:** The production reproducibility bar in DEC-026 requires an independent matching rebuild, while the initial prototype pipeline and lockfile enforcement have not yet been validated.
- **Decision:** For the initial prototype, require identified inputs: record the source and treefile revision, repository metadata identity, resolved RPM NEVRAs, package-signing key fingerprints, compose and Image Builder versions and configuration, OSTree checksum, and qcow2 digest. Retain inputs where practical and report any inputs or tool behavior that could not be pinned or repeated. Treat this as traceable prototype evidence, not a claim of reproducible output; reserve that claim for an independent rebuild with matching declared outputs.
- **Alternatives:** Accept a repeatable written procedure without input records; require retained inputs and an independent matching rebuild before any prototype image can be evaluated.
- **Consequences:** Pipeline work can start with auditable evidence and measured limits. Production release requirements remain those in DEC-026 and DEC-031, including enforced resolved-input pinning and an independent matching rebuild for a reproducibility claim.

### DEC-042: Default applications and removability

- **Status:** Accepted
- **Date:** 2026-11-10
- **Context:** The first-release desktop needs a defined starter app set, while Axiom's newcomer-focused app contract requires users to control installed applications. Brave Browser has both Flatpak and native Linux packages; Brave's current Linux guidance says its Flatpak has limitations and recommends the native package when available. The ArtCraft site presents a launcher for its Craft apps, individual app installers, and a separate ArtCraft Studio, so the intended packaged component needs to be identified. See [Brave's Linux installation guidance](https://brave.com/linux/) and [ArtCraft](https://getartcraft.com/).
- **Decision:** Include Brave Browser and ArtCraft in the selected default-app set for the first release. Every default app must be individually uninstallable through a documented graphical path without removing unrelated apps or core system components. Record whether app settings or user data remain after removal. The specific ArtCraft component and the package sources/update mechanisms are not selected by this decision; resolve them before app packaging without changing the accepted default-source and user-control requirements.
- **Alternatives:** Ship no default third-party applications; make defaults non-removable; select package formats before confirming which apps users want.
- **Consequences:** The first-release app contract includes these defaults and an explicit uninstall acceptance criterion. Packaging must reconcile Brave's upstream guidance and ArtCraft's multiple products with the accepted Flatpak source policy and the requirement to keep routine app management out of administrator-only package layering. No app is claimed to be implemented or included yet.

### DEC-043: Repository layout for implementation artifacts

- **Status:** Accepted
- **Date:** 2026-11-12
- **Context:** DEC-008 requires a versioned Axiom treefile and DEC-031 requires the treefile and repository configuration to be kept in version control, but no location is defined for either. DEC-029 anticipates Axiom-owned Rust components with no home. The repository is documentation-only today, so an implementer beginning Stage 1 would have to invent a layout, and specifications cannot cite stable paths until one exists.
- **Decision:** Adopt this layout when implementation begins. Directories are created only as the work reaches them; this record reserves the paths so specifications can cite them.

  ```text
  docs/                 project documentation (existing)
  docs/validation/      dated validation reports (existing)
  image/treefile/       Axiom treefile and any included fragments
  image/lockfiles/      architecture-specific package lockfiles (DEC-031)
  image/repos/          named Fedora repository definitions (DEC-031)
  image/blueprints/     Image Builder blueprint(s) for the qcow2 artifact
  crates/               Axiom-owned Rust components, one directory per crate
  build/                build and validation entry points
  build/checks/         repeatable composition and boot checks
  release/              provenance manifest template and release procedure
  ```

  Keep composition inputs (`image/`), project-owned code (`crates/`), build orchestration (`build/`), and release process (`release/`) separate, because the accepted decisions already draw boundaries between them: DEC-008 separates package selection from artifact assembly, and DEC-016 separates building from release signing.
- **Alternatives:** Place the treefile at the repository root and add directories ad hoc; mirror Fedora CoreOS's configuration-repository layout exactly; split composition inputs, Rust components, and release tooling into separate repositories.
- **Consequences:** Specifications gain citable paths, and moving a path later invalidates those references. `crates/` presumes a single Cargo workspace, which DEC-046 proposes. Separate repositories remain possible later but would complicate the provenance manifest's single source revision field (DEC-026).

### DEC-044: OSTree remote, ref, and version scheme

- **Status:** Accepted
- **Date:** 2026-11-12
- **Context:** DEC-014, DEC-015 and DEC-025 require an Axiom OSTree remote whose trust is scoped to an Axiom release key, DEC-008 requires a versioned OSTree commit, and DEC-017 requires a published support end date for each release. None of them names the remote, the ref layout, or the version scheme, so neither the treefile nor the client-side remote configuration can be written, and build evidence has no release identity to record.
- **Decision:** Use the remote name `axiom` for the production remote, and `axiom-dev` for any development remote; never publish development commits under the `axiom` name (DEC-014). Use the ref scheme `axiom/<fedora-release>/<arch>/<variant>`, for example `axiom/45/x86_64/kinoite`, mirroring Fedora's own ref layout so that a base-release migration is legible as a ref change. Use the release version scheme `<fedora-release>.<serial>`, for example `45.1`, where the serial increments for each published release on that ref, recorded as the OSTree commit version metadata. Identify unpublished prototype builds as `<fedora-release>.0-dev.<short-source-revision>`.
- **Alternatives:** Date-based versions such as `45.20261112`; semantic versioning independent of the Fedora base; a single ref carrying no release in its path, with the base release tracked only in metadata.
- **Consequences:** Encoding the Fedora release in the ref makes the DEC-018 end-of-support migration an explicit rebase between named refs rather than a silent change under one ref, and it keeps two base releases publishable at once during a transition. It also means the ref path changes at every Fedora release, so client migration instructions must name the new ref. Changing this scheme after the first publication would require a client trust and ref transition, so it should be settled before any commit is published.

### DEC-045: Build identity in the desktop session

- **Status:** Accepted
- **Date:** 2026-11-12
- **Context:** DEC-004 defines first boot as reaching an interactive graphical session "that identifies the Axiom OS build" but leaves the mechanism open. The boot acceptance evidence in `IMAGE_PIPELINE.md` requires a screenshot showing the build identity, so the Stage 1 gate cannot be claimed passed until the mechanism is decided.
- **Decision:** Set standard `os-release` fields during composition: `NAME="Axiom OS"`, `ID=axiom`, `ID_LIKE=fedora`, `VERSION_ID` set to the DEC-044 release version, `VARIANT` naming the desktop variant, `BUILD_ID` set to the short OSTree commit checksum, plus project `HOME_URL` and `BUG_REPORT_URL`. Require the identity to be legible in the session without opening a terminal, through KDE's System Settings "About this System" page, which reads these fields; that page is the boot-gate screenshot. Add no Axiom-authored splash screen, desktop watermark, or "About Axiom" application in the first milestone.
- **Alternatives:** A desktop watermark or overlay showing the build; a project-authored "About Axiom" application; identifying the build only from a terminal via `rpm-ostree status`.
- **Consequences:** This uses the standard mechanism already consumed by KDE, systemd and rpm-ostree, so the gate needs no Axiom-owned code, which suits a milestone whose purpose is validating the pipeline. Setting `ID=axiom` may affect third-party scripts and installers that branch on distribution ID; `ID_LIKE=fedora` is intended to mitigate that and the effect should be noted in the compatibility note (DEC-034). Validation must confirm that Plasma's About page on Fedora 45 displays these fields as expected, and that `BUILD_ID` can be populated from the commit checksum during composition.

### DEC-046: Rust toolchain baseline and component conventions

- **Status:** Accepted
- **Date:** 2026-11-12
- **Context:** DEC-029 makes Rust the default for new Axiom-owned system software and requires each component to document its purpose, owner, privilege boundary and maintenance plan, and DEC-010 requires an unprivileged D-Bus client for rollback. No toolchain baseline, dependency policy, or interface convention exists, so components could not be built consistently or reviewed against a shared standard.
- **Decision:** Keep all Axiom-owned Rust components in a single Cargo workspace under `crates/`, one crate per component. Pin the toolchain with a checked-in `rust-toolchain.toml` naming an exact stable version, preferring the version packaged by the selected Fedora base release so components can be built with the base toolchain; treat that pinned version as the minimum supported version rather than maintaining a separate older floor. Commit `Cargo.lock` for all workspace members. Prefer dependencies already packaged in Fedora; record every new direct dependency and its justification in the component's record. Require a comment stating the upheld invariant at every use of `unsafe`. For D-Bus, call documented system interfaces through a maintained pure-Rust client, defaulting to `zbus` unless a component record justifies otherwise, and never shell out to the `rpm-ostree` command-line tool from a graphical component. Use structured error types at component boundaries, log to the journal, and never log credentials or full user file paths (DEC-023).
- **Alternatives:** Track the latest stable Rust without a pin; vendor all dependencies into the repository; permit invoking the `rpm-ostree` CLI instead of its D-Bus API; keep crates independent with no workspace.
- **Consequences:** Pinning to Fedora's packaged toolchain keeps component builds consistent with the base system and avoids introducing a second toolchain into the image pipeline, at the cost of lagging upstream Rust releases and of a toolchain bump at each base-release migration. Preferring Fedora-packaged crates constrains dependency choice and may require justifying a dependency that is not packaged. Calling D-Bus rather than the CLI is what keeps DEC-010's privilege boundary checkable, since the CLI would obscure which authorization path is used.

### DEC-047: Verification strategy and evidence retention

- **Status:** Accepted
- **Date:** 2026-11-12
- **Context:** Roadmap Stage 2's exit condition requires "repeatable checks" for core behavior, but nothing defines what a check is, where checks live, or what result counts as evidence. DEC-041 and DEC-026 define build-input evidence only, and DEC-034 requires a compatibility note listing tested virtual hardware and known limitations without saying how that testing is recorded. Without this, "validated" has no agreed meaning and gates cannot be claimed consistently.
- **Decision:** Define three classes of check, and require every requirement in `REQUIREMENTS.md` to name the class that verifies it.
  1. **Composition checks** run on the build host against the composed tree or image without booting it: a package is present or absent, a unit is enabled or disabled, a configuration value is set, no unexpected remote is configured. Scriptable and run for every build.
  2. **Boot checks** run inside the baseline QEMU profile against a booted image: deployment identity, enabled units and listening sockets, configured Flatpak remotes, `os-release` fields.
  3. **Interaction checks** require a person in the target Plasma session: whether an update explanation is legible, whether an uninstall path works, whether the permission page is reachable. Recorded with a screenshot; not automated for the first release.

  Keep composition and boot checks in `build/checks/` behind a single entry point; these are the repeatable checks Stage 2 requires. File each run as a dated report in `docs/validation/`, attach screenshots for interaction checks, and treat filed reports as immutable. Claim a gate passed only when every requirement in its scope has a recorded check result in a filed report.
- **Alternatives:** Rely on the written runbook and operator judgment without defined check classes; require full automation including the user interface before Stage 2 can exit; adopt an external continuous-integration service before the local build path is validated.
- **Consequences:** Most requirements become checkable without a person, while claims about the user interface stay bounded by what was actually observed, which matches the project's evidence rule. Interaction checks remain manual and slow, so full acceptance runs will be infrequent and should be reserved for gates. The check runner is Axiom-owned software and needs a component record under DEC-029 and the conventions in DEC-046.

### DEC-048: Default-application delivery for Brave and ArtCraft

- **Status:** Accepted
- **Date:** 2026-11-12
- **Context:** DEC-042 selects Brave Browser and ArtCraft as first-release defaults and requires each to be individually uninstallable through a documented graphical path. Settling the delivery paths required reconciling several accepted decisions:
  - DEC-031 enables only named Fedora repositories as composition inputs, so neither vendor's own RPM repository can be a build input.
  - A package composed into the OSTree commit cannot be removed through Discover; removal requires `rpm-ostree override remove`, which DEC-019 keeps outside the supported newcomer workflow. An RPM-delivered default application therefore cannot satisfy DEC-042's graphical-removability requirement.
  - Brave publishes `com.brave.Browser` on Flathub, but its [Linux guidance](https://brave.com/linux/) states that build is not yet working as well as its native packages and recommends native packages where available.
  - ArtCraft is **not** published on Flathub. It distributes Flatpaks from its own OCI registry remote at `https://getartcraft.com/artcraft-oci.flatpakrepo`, with the launcher published as `ai.storyteller.artcraft-launcher`, alongside `.deb`, `.rpm`, AppImage and tarball channels. Enabling that remote requires permitting a second default Flatpak source, which DEC-020 prohibited.
- **Decision:** Deliver Brave as the Flathub Flatpak `com.brave.Browser`, accepting Brave's stated functional caveat and recording it in the compatibility note required by DEC-034. Deliver ArtCraft as the Flatpak launcher `ai.storyteller.artcraft-launcher` from ArtCraft's own OCI remote, enabled as a second default Flatpak source under DEC-050. Use neither vendor's RPM or `.deb` channel, and compose neither application into the OSTree commit, so both remain individually removable through Discover. Seed both at image build time; if Fedora 45 provides no offline seeding mechanism, treat them as first-run installations and state the network requirement explicitly rather than implying offline capability.
- **Alternatives:** Ship Brave as a layered RPM from Brave's own repository, which would require superseding DEC-031 and relaxing DEC-042's removability requirement; ship no default third-party applications at all, superseding DEC-042; deliver ArtCraft from its `.rpm` channel, which DEC-031 forbids and which would not be graphically removable.
- **Consequences:** Both defaults are Flatpaks, so both are sandboxed, carry declared permissions, and can be removed through Discover without touching the base system. This satisfies DEC-042 without superseding DEC-031 or DEC-042; the cost is a second default application source (DEC-050) and Brave's reduced Flatpak functionality. Validation must establish three things before release: whether ArtCraft's OCI remote can be enabled at system scope rather than the `--user` scope its own instructions show; what integrity verification that remote provides, since its documentation describes only SHA-256 checksums over files streamed from GitHub Releases and names no signing key, which DEC-012 makes a security-relevant gap for a default-enabled remote; and whether the launcher's in-app installation of further Craft apps presents source and permission information consistently with DEC-021. Upstream describes most Craft applications as in development or early alpha, which is a product risk for the newcomer audience in DEC-001 and belongs in the compatibility note.

### DEC-049: Treefile derivation from Fedora's configuration

- **Status:** Accepted
- **Date:** 2026-11-12
- **Context:** DEC-008 requires a versioned Axiom treefile "based on Fedora's Atomic Desktop configuration" without saying how it derives from it. rpm-ostree treefiles support an `include` directive, and Fedora maintains its Atomic Desktop manifests in a configuration repository. The derivation mechanism sets the maintenance cost of every future base-release migration and determines whether Axiom's own changes stay reviewable, so it must be settled before the treefile can be specified.
- **Decision:** Vendor Fedora's Atomic Desktop configuration at a pinned revision, and have the Axiom treefile `include` Fedora's Kinoite manifest while overriding only what Axiom changes. Keep Axiom-owned additions, exclusions and configuration in the Axiom treefile, so that one file is the complete record of how Axiom differs from Fedora Kinoite. Record the pinned Fedora configuration revision in build evidence (DEC-041) and in the provenance manifest (DEC-026). Treat a base-release migration as a revision bump followed by review of the resulting differences.
- **Alternatives:** Copy Fedora's treefile wholesale and maintain it as Axiom's own; reference Fedora's configuration repository at a moving branch rather than a pinned revision; specify required contents only and defer derivation until after the Fedora 45 probes.
- **Consequences:** The Axiom diff stays small and auditable, which is what makes DEC-028's commitment to inherit base components checkable: everything Axiom changes is visible in one file. Upstream changes then arrive deliberately at a revision bump rather than silently. The cost is a dependency on the `include` mechanism and on Fedora's manifest structure, both of which must be confirmed for Fedora 45 during Stage 1, since `IMAGE_PIPELINE.md` forbids inferring Fedora 45 behavior from earlier releases. If the include path proves unusable on Fedora 45, record the evidence and return with a scoped alternative rather than silently vendoring a fork.

### DEC-050: Permit the ArtCraft OCI remote as a second default application source

- **Status:** Accepted
- **Date:** 2026-11-12
- **Supersedes:** [DEC-020](#dec-020-default-flatpak-application-source) in part — only its clause prohibiting additional default Flatpak remotes. The rest of DEC-020 continues to apply.
- **Context:** DEC-020 enabled the full Flathub remote by default and prohibited enabling any additional Flatpak remote in the first release. DEC-042 then selected ArtCraft as a default application, and ArtCraft is not published on Flathub; it distributes Flatpaks only from its own OCI registry remote. Including ArtCraft as a default therefore requires either a second default remote or a different delivery path, and every other path conflicts with DEC-031 or with DEC-042's graphical-removability requirement (DEC-048).
- **Decision:** Permit exactly one additional default Flatpak remote — ArtCraft's OCI remote at `https://getartcraft.com/artcraft-oci.flatpakrepo` — for the purpose of delivering the selected ArtCraft component. Enable no other additional remote by default. The rest of DEC-020 applies to both remotes: show the application's source before installation, explain declared permissions in plain language, and never present publisher verification as a safety certification. Because this remote is vendor-operated rather than a broad catalog, the interface must make clear which remote supplies an application.
- **Alternatives:** Keep DEC-020 unchanged and drop ArtCraft from the defaults; keep DEC-020 unchanged and deliver ArtCraft from its RPM channel, which DEC-031 forbids; permit additional remotes in general rather than naming this one.
- **Consequences:** Axiom carries two default application sources, so the application contract must state which remote supplies each default, and users need an accessible way to tell them apart. The ArtCraft remote's verification properties are not established — its documentation describes SHA-256 checksums over files streamed from GitHub Releases and names no signing key — so DEC-012's treatment of third-party applications as untrusted applies with particular force here, and the remote's integrity behavior must be validated before release. Permitting one named remote rather than remotes in general keeps the boundary reviewable: adding a third source requires another decision.

### DEC-051: Documentation consistency checks

- **Status:** Proposed
- **Date:** 2026-11-12
- **Context:** This repository's rules are enforced only by attention.
  [DEC-047](#dec-047-verification-strategy-and-evidence-retention)
  defines three classes of check — composition, boot and interaction — but all three verify
  the *product*, and none covers the documentation that specifies it. Meanwhile the corpus
  depends on machine-checkable invariants: every requirement cites a `DEC-nnn` ID, decisions
  are linked by `#dec-nnn-slug` anchors, open questions are cited by stable number, and the
  README's status must not outrun `docs/validation/`. A renamed heading silently breaks every
  anchor pointing at it, and a cited ID that does not exist reads exactly like one that does.
  Nothing currently detects either.
- **Decision:** Define a fourth class of check — **documentation checks** — alongside the
  three in DEC-047, and require it to run over tracked Markdown before a commit lands. The
  class comprises: every relative link resolves; **every `#dec-nnn-slug` fragment resolves to
  a heading that exists**; every cited `DEC-nnn` names a record present in `DECISIONS.md`; no
  document asserts a decision status that contradicts `DECISIONS.md`; and prose spelling.

  Implement with `lychee` for links, **configured with fragment checking enabled**, because
  the default skips fragments and anchors are this project's main link risk; `typos` for
  prose, with a project wordlist for the domain vocabulary (`rpm-ostree`, `ostree`, Kinoite,
  treefile, qcow2, polkit, zbus, Flatpak, Flathub); and a small ID-and-status cross-check for
  the two invariants no off-the-shelf tool knows about. Run them from a `pre-commit`
  configuration so they execute locally, since there is no CI.

  Unlike DEC-047's classes, a documentation check verifies **internal consistency only**. It
  never establishes that anything builds, boots or works; that still requires a dated report
  in `docs/validation/`, and a green documentation check must never be cited as evidence.
- **Alternatives:** Keep the checks manual, as the conventions already ask — which is the
  present state, and the stale cross-references found during consistency passes are the
  argument against it. Defer until implementation begins, which leaves the specification
  layer unchecked during the period it is being written and most heavily edited. Run the
  checks in GitHub Actions instead, which needs a `.github/` workflow that does not exist and
  would catch errors after a push rather than before a commit. Adopt link checking only and
  skip spelling, which would have caught neither of the repeated misspellings in the existing
  commit log.
- **Consequences:** The invariants the conventions already require become enforced rather
  than aspirational, and a contributor learns about a broken anchor before the commit instead
  of from a future reader. This introduces four files the documentation-only gate currently
  forbids — a `pre-commit` configuration, a `lychee` configuration, a `typos` configuration,
  and one check script — so **none is created unless and until this record is Accepted**; the
  gate does not lift for anything else. It also adds local tool prerequisites, so the checks
  must degrade to a clear skip rather than a failure when a tool is absent. The wordlist is a
  maintenance surface: a legitimate new term is a one-line addition, but an unmaintained list
  trains contributors to bypass the check.

### DEC-052: Text line-ending normalization

- **Status:** Proposed
- **Date:** 2026-11-12
- **Context:** The working copy lives on a drive shared with a dual-boot Windows
  installation. A Windows-side editor, or a clone made with `core.autocrlf=true`, rewrites
  text files to CRLF; from Linux the entire tree then reports as modified, and a real change
  becomes impossible to find in the diff. The repository has no `.gitattributes`, so line
  endings depend on each machine's local git configuration.
- **Decision:** Add a `.gitattributes` at the repository root declaring `* text=auto eol=lf`,
  so text files are stored and checked out with LF regardless of the client platform. Mark
  any committed binary container explicitly as `binary` when one is first added, because a
  normalizer that rewrites bytes inside an archive corrupts it while git reports an ordinary
  checkout.

  **This is prevention, not a repair.** Measured 2026-11-12: `git ls-files --eol` reports
  `i/lf w/lf` for all 15 tracked files, and no tracked file contains CRLF. This record
  changes nothing about the current tree; it fixes the behavior for the next clone or
  Windows-side edit.
- **Alternatives:** Rely on each contributor setting `core.autocrlf=input` locally, which is
  per-machine configuration that no clone inherits and that silently does not apply to a
  contributor who has not set it. Do nothing, accepting that a Windows-side edit produces a
  whole-tree diff. Add the file only after the problem occurs, which means recovering a
  polluted diff first.
- **Consequences:** A one-line file makes line endings a property of the repository rather
  than of each machine. `.gitattributes` is a configuration file, so it is **not created
  unless this record is Accepted**. Normalization is applied as files are next staged, so
  accepting this later rather than sooner is harmless given no CRLF exists today. A future
  binary file added without its `binary` attribute would be corrupted by the wildcard rule,
  which is why the attribute is required at the time such a file is first committed.

### DEC-053: Tracked agent rule and skill layout

- **Status:** Proposed
- **Date:** 2026-11-12
- **Context:** [DEC-043](#dec-043-repository-layout-for-implementation-artifacts) reserves
  paths for implementation artifacts but says nothing about where instructions to automated
  contributors live. Those instructions are currently one file, `LLM_GUIDE.md`, reached
  through `CLAUDE.md` and `AGENTS.md`. One file is adequate at its present size, and the
  documentation conventions require a recorded reason before any new directory is added, so
  the question is whether a structured layer earns its place now or later.
- **Decision:** Reserve `.claude/rules/` for path-scoped rule files and `.claude/skills/`
  for repeatable procedures, tracked in version control so a clone carries the rules binding
  it. A rule file declares the paths it governs and is loaded when a matching file is opened,
  which puts the relevant rule in front of a contributor at the moment of editing rather than
  relying on it having been read at the start. A skill is a named procedure with its steps and
  its failure modes; the two worth having first are **filing a validation report** and
  **drafting a decision record**, because both are frequent, both have an exact shape, and
  both are currently reconstructed from prose each time.

  `LLM_GUIDE.md` remains the canonical guide and the single source of each rule. A rule file
  restates the rule for one path and cites the guide; it never holds a rule the guide does
  not, because two copies of a rule become two different rules.
- **Alternatives:** Keep everything in `LLM_GUIDE.md`, which stays correct and is the reason
  this is proposed rather than assumed — the guide is currently short enough to read in full.
  Keep `.claude/` untracked and local, which means each clone and each worktree has different
  rules and none travels with a contribution. Add the directories but leave them empty until
  a need appears, which reserves the paths without demonstrating the value.
- **Consequences:** Rules arrive when they are relevant rather than only at session start,
  and two error-prone procedures gain a checkable shape. Against that: a second location for
  guidance is a drift risk, which the single-source rule above is meant to contain but does
  not enforce; and `.claude/` is harness-specific, so `AGENTS.md` must keep pointing other
  tooling at `LLM_GUIDE.md` or non-Claude contributors silently lose the layer. The
  directories and files are **not created unless this record is Accepted**. If
  [DEC-051](#dec-051-documentation-consistency-checks) is also accepted, its checks should
  cover these files, since a rule file that cites a renamed heading fails as silently as any
  other stale pointer.

### DEC-054: Branch roles and the merge gate

- **Status:** Proposed
- **Date:** 2026-11-12
- **Context:** Three branches exist and no document says what any of them is for. Measured
  2026-11-12 against `dev`:

  | Ref | Tip | Date | Relative to `dev` |
  |---|---|---|---|
  | `dev` (current) | `52f2aff` | 2026-11-10 | — |
  | `origin/dev` | `52f2aff` | 2026-11-10 | in sync |
  | `main` | `48e0be2` | 2026-11-03 | 10 behind, 0 ahead |
  | `origin/main` | `b5288d2` | 2026-11-03 | 12 behind, 0 ahead |
  | `testing` / `origin/testing` | `7ef2375` | 2026-11-09 | 4 behind, 2 ahead |

  Two facts need deciding rather than describing. The published `main` is twelve commits
  behind the working line, so a reader arriving at the repository's default branch sees a
  state the project abandoned a week ago. And `testing` holds two commits absent from `dev` —
  `7ef2375` and `539160f` — whose subjects duplicate commits already on `dev` under different
  hashes, so it reads as an abandoned parallel line rather than a purposeful branch.
- **Decision:** Treat `dev` as the integration branch where work lands, and `main` as the
  reviewed, published state, updated from `dev` when a documentation milestone is coherent —
  at minimum whenever the README's "Current status" would change for a reader. Before `dev`
  merges to `main`, require that no document contradicts `DECISIONS.md`, that no record was
  left in a status an agent set, and that the README's status matches the newest report in
  `docs/validation/`; if [DEC-051](#dec-051-documentation-consistency-checks) is accepted, its
  checks are the mechanical part of that gate.

  **`testing` is left undecided deliberately.** It carries two commits `dev` does not, so it
  cannot be called unused, and nothing in the repository records what it was for. The owner
  should merge it, retire it after confirming those two commits are redundant, or assign it a
  role here. Inventing one would be exactly the silent requirement the contribution guide
  forbids.
- **Alternatives:** Work on `main` alone and delete the others, which the twelve-commit
  divergence shows is not what is happening in practice. Adopt a three-tier model with a
  soak branch between `dev` and `main`, which gates nothing in a repository with no build
  and no deployment. Leave the model undocumented, which is the present state and the reason
  a reader cannot tell which branch to trust.
- **Consequences:** A reader learns which branch is current, and `main` stops being a stale
  default. The merge gate is manual until DEC-051 provides the mechanical half, so it depends
  on being run rather than on being enforced. Updating `main` is an explicit step someone has
  to take, so the gap will reappear if it is not taken. Recording `testing` as undecided
  leaves a visible loose end, which is the intent: it is a question for the owner, not a
  branch an agent should quietly delete.

### DEC-055: Content of the plain-language update summary

- **Status:** Proposed
- **Date:** 2026-11-12
- **Context:** [DEC-009](#dec-009-system-update-and-rollback-experience) requires Axiom to
  present "a plain-language update summary" and ask before staging, but does not say what the
  summary contains. The audience is desktop newcomers
  ([DEC-001](#dec-001-initial-audience)), so the obvious technical source — rpm-ostree's
  package diff between deployments — is unreadable as a primary summary: a base-system update
  can change hundreds of packages, and a NEVRA list answers no question a newcomer is asking.
  The acceptance procedure in
  [`UPDATE_ROLLBACK_SPEC.md`](UPDATE_ROLLBACK_SPEC.md#a--the-update-is-explained-before-anything-changes)
  cannot fully check step A until this is settled, because "a summary was shown" is checkable
  while "the summary explained the update" is not.
- **Decision:** Require the summary, before anything is staged, to state:
  1. **That nothing changes until reboot**, and that the running system is unaffected until
     then. This is the fact most likely to be misread, because every other update model the
     audience has met applies immediately.
  2. **The version it is moving from and to**, using the release version scheme in
     [DEC-044](#dec-044-ostree-remote-ref-and-version-scheme).
  3. **Whether the update contains security fixes**, when that is known from the build
     inputs. [DEC-012](#dec-012-initial-security-threat-model) requires rollback warnings for
     security fixes, which is only possible if the update's security status is surfaced here
     first.
  4. **A short description of what changed at system level**, not per package.
  5. **That it can be undone, and what undo does not restore** — the system deployment only,
     never user files, and not necessarily application data
     ([DEC-009](#dec-009-system-update-and-rollback-experience),
     [DEC-018](#dec-018-end-of-support-migration-notice)).

  Keep the full package-level difference available but **not shown by default**, so the
  advanced control `VISION.md` promises is retained without making it the newcomer's first
  encounter with an update.

  The summary must not present a raw package list as the primary explanation, must not claim
  an update is risk-free, and must not fabricate a description when none is available — see
  the consequences below.
- **Alternatives:** Show rpm-ostree's package diff directly, which is accurate, free, and
  unreadable for the audience. Show only the version numbers, which is honest but explains
  nothing and makes the approval meaningless. Defer the content until the interface is
  designed, which inverts the dependency: the interface cannot be designed against an
  unspecified summary, and step A of the acceptance procedure would stay uncheckable.
- **Consequences:** Points 2 and 5 are available from the deployment and the decisions
  already made. Points 3 and 4 are obligations on release engineering, not on the interface:
  - **Point 4 requires per-release notes to exist.** That is a new release-engineering
    deliverable, tied to the version scheme in DEC-044 and the support dates in
    [DEC-017](#dec-017-support-window-follows-fedora-base-support). If notes are absent the
    summary degrades to points 1, 2, 3 and 5, and the interface must say nothing about what
    changed rather than inventing a description.
  - **Point 3 may not be mechanically available.** Fedora's package metadata carries advisory
    and security information, but an Axiom release is delivered as an OSTree commit composed
    from those packages, and whether that metadata survives composition in a form the client
    can read **is unvalidated**. If it does not, either the pipeline must carry the security
    status forward as release metadata of its own, or the summary must omit point 3 rather
    than assert that an update contains no security fixes. Asserting the absence of security
    fixes without evidence would be the more harmful failure, because a user may reasonably
    defer an update described as routine.

  This record specifies content, not presentation. Wording, layout and visual design remain
  deferred until a target session can be observed, per
  [`UPDATE_ROLLBACK_SPEC.md`](UPDATE_ROLLBACK_SPEC.md#scope).
