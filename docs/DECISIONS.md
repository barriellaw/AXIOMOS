# Architecture and project decisions

Record consequential choices here so maintainers and automated contributors can distinguish settled direction from proposals. Use one entry per decision.

## Entry format

### DEC-000: Short title

- **Status:** Proposed / Accepted / Superseded
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

- **Status:** Accepted
- **Date:** 2026-11-03
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
