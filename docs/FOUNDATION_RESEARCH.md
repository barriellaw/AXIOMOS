# Linux foundation research: first pass

**Status:** Fedora 45 Kinoite and the treefile → OSTree commit → Image Builder pipeline are selected for the initial prototype; release-specific pipeline behavior and end-to-end build reproducibility remain to be validated.
**Reviewed:** 2026-11-05

This note compares three candidates named in the project charter. It is a first-pass screen, not a complete technical evaluation or endorsement. The question is which foundation best supports Axiom OS's desktop goals, user control, recoverable updates, hardware support, and maintainable Rust services.

## Candidates

| Candidate | What it offers | Fit to investigate | Main questions and costs |
|---|---|---|---|
| Fedora Atomic Desktops | Fedora desktop variants based on rpm-ostree, with distinct GNOME, KDE Plasma, and other desktop editions. Fedora describes Silverblue as atomic and focused on container-oriented workflows. [Fedora Atomic Desktops](https://fedoraproject.org/atomic-desktops/) | A maintained desktop foundation with an existing Linux desktop ecosystem and an image-based/atomic system update model. | How much base-system customization is needed? Do layering and application workflows fit the desired simple-mode experience? Which desktop variant and release cadence fit the product? |
| Universal Blue | Community-maintained images built from Fedora Atomic Desktops, with additional changes and the ability to derive custom images from a Containerfile and build actions. [Universal Blue](https://universal-blue.org/) | A concrete example and ecosystem for custom Fedora-derived images, including desktop-focused projects. Could inform how Axiom builds and distributes a tailored system. | It is an additional project and image supply chain to assess, not just an abstract base. What should Axiom own, inherit, or upstream? How would project governance, image signing, release cadence, and long-term maintenance work? |
| NixOS | A Linux distribution whose declarative configuration describes system packages, services, users, and settings. System generations can be built and rolled back. [NixOS manual](https://nixos.org/manual/nixos/stable/) and [How Nix Works](https://nixos.org/guides/how-nix-works/) | Strong alignment with reproducible system configuration and generation-based rollback; could support an OS where system state is explicitly managed. | Would the Nix language and model be teachable through Axiom's accessible UI? How would it integrate with conventional Linux software, drivers, and third-party tooling? What operational skills would maintainers need? |

## Initial reading

- Fedora Atomic Desktops appear to offer the closest route to a conventional desktop experience while changing how the base system is updated and extended. This is an inference from Fedora's description of its desktop variants and update model, and still needs hands-on validation.
- Universal Blue appears most useful to evaluate as a custom-image ecosystem and operating model built around Fedora Atomic foundations. Adopting it would also mean evaluating its specific image pipeline, project boundaries, and maintenance relationship.
- NixOS offers the strongest explicit declarative configuration and generation model among these three at this level of review. That strength may come with a larger learning and product-design burden for Axiom's intended nontechnical users; this should be tested, not assumed.
- None of these choices decides Axiom's product interface, Rust service architecture, application manager, or the degree of system reversibility users can expect. Those need separate evaluation.

## Next validation step

Validate the selected Kinoite foundation in the agreed QEMU target. Evaluate first boot, the update and rollback workflow, newcomer-facing clarity, image customization, and the maintenance work required to ship a derived product. Keep the image-build choice open until a project-customized Kinoite image can be built reproducibly.

## Fit to the accepted first release

The first release is now defined as a reproducible x86-64 VM desktop for newcomers, demonstrating a system update and rollback. That narrows the comparison:

- **Fedora Atomic Desktops** are the leading candidate for the first prototype. Fedora's current Silverblue description combines a GNOME desktop with rpm-ostree, atomic system updates, and a retained previous system version. Fedora also documents rollback after an upgrade. This is close to the chosen workflow and starts from a maintained desktop. However, the documented rollback operation is a system command; Axiom still needs to make recovery understandable and accessible to newcomers. This is a fit assessment, not evidence that the desired end-to-end UI already exists. [Fedora Silverblue](https://fedoraproject.org/atomic-desktops/silverblue/) and [Fedora rpm-ostree overview](https://fedoraproject.org/wiki/Changes/RpmOstree).
- **NixOS** has a strong match for system generations and rollback, and its rebuild tooling can produce QEMU virtual machines. It remains a viable alternative if declarative configuration is itself central to the product. Its configuration model would also become part of Axiom's user and maintainer experience, so that learning and support cost needs evaluation. [NixOS generations](https://wiki.nixos.org/wiki/NixOS) and [nixos-rebuild](https://wiki.nixos.org/wiki/Nixos-rebuild).
- **Universal Blue** demonstrates a custom image workflow using Containerfiles and automated builds, but it is an image ecosystem built on Fedora Atomic foundations rather than a separate base distribution. Taking one of its maintained images would add an upstream project relationship to Axiom's foundation. Its build patterns can be studied without adopting its finished images. [Universal Blue](https://universal-blue.org/) and [contributor build guide](https://universal-blue.org/contributing.html).

Fedora 45 is the selected base release for Axiom's initial validation. Fedora's own Kinoite qcow2 artifact is useful as a reference, but Axiom must validate its customized pipeline separately. [Fedora 45 Atomic Desktop image proposal](https://discussion.fedoraproject.org/t/f45-change-proposal-build-atomic-desktops-disk-images-with-image-builder-systemwide/179631).

There is a distinction between Fedora producing its own Kinoite qcow2 artifact and Axiom reproducing a customized Kinoite image. The Image Builder docs list a `kinoite-qcow2` target, but its supported blueprint options cover system files, services, users, and related machine setup; they do not include a package list. The Kinoite image type and Image Builder's generic OSTree input path therefore provide the artifact-conversion pieces, but a customized package set needs a separately composed OSTree commit. [Fedora Kinoite download](https://fedoraproject.org/atomic-desktops/kinoite/download/), [Kinoite qcow2 image description](https://osbuild.org/docs/user-guide/image-descriptions/fedora-44/kinoite-qcow2/), and [Image Builder OSTree workflow](https://osbuild.org/docs/developer-guide/projects/image-builder/usage/).

The Kinoite 45 preview qcow2 can be used as a reference artifact while researching the custom image pipeline. It will not be used as Axiom's next validation image; the project will settle a reproducible custom pipeline first, as recorded in [DEC-007](DECISIONS.md#dec-007-initial-vm-workflow-evaluation-image).

### Accepted custom image pipeline

Maintain an Axiom treefile based on Fedora's Atomic Desktop configuration, compose a versioned OSTree commit from that definition and pinned Fedora package inputs, and use Image Builder's OSTree input workflow to produce the x86-64 Kinoite-compatible qcow2 disk image. This keeps package selection in the system tree and uses Fedora's own documented path for turning OSTree commits into disk images. The Image Builder documentation shows both the Fedora `kinoite-qcow2` type and OSTree URL/ref inputs. Fedora's Atomic Desktop source manifests are maintained in its [config repository](https://forge.fedoraproject.org/atomic-desktops/config).

This is the accepted integration of documented building blocks, not a verified end-to-end local workflow for a customized Kinoite tree. Fedora's Fedora 45 change record says local build documentation for Atomic Desktops would follow after the build transition. The first validation should confirm that the composed tree boots as QEMU qcow2, preserves update/rollback behavior, and can be rebuilt from pinned inputs. See [DEC-008](DECISIONS.md#dec-008-custom-kinoite-image-pipeline).

### Update and rollback fit

Fedora's Kinoite change proposal says Plasma Discover supports staged system updates through rpm-ostree; updates are prepared in the background and take effect on reboot. Fedora's Kinoite update policy enables unattended system updates by default, so Axiom would need to change that default to honor its accepted promise to explain the update before applying it. rpm-ostree's native rollback switches the previous deployment back to the default boot entry; the bootloader also retains deployments for selecting a previous system at startup. This recovers the system tree, not arbitrary files in the user's home directory. [Fedora Kinoite automatic updates](https://fedoraproject.org/wiki/Changes/KDEKinoiteAutoUpdateByDefault), [rpm-ostree administration](https://github.com/coreos/rpm-ostree/blob/main/docs/administrator-handbook.md), and [Fedora Kinoite overview](https://fedoraproject.org/atomic-desktops/kinoite/).

The accepted product flow keeps rpm-ostree as the updater and adds an Axiom explanation and recovery control around it. See [DEC-009](DECISIONS.md#dec-009-system-update-and-rollback-experience).

rpm-ostree runs a privileged system D-Bus daemon and maps operations to polkit authorization actions. Fedora's Atomic Desktop policy allows active local users to update without a password, while passwordless rollback is limited to active local `wheel` administrators. This supports an unprivileged Axiom UI that requests the operation through the existing daemon and does not add a privileged helper or grant broader authorization. Authorization behavior still needs confirmation against the exact Fedora release used by the image. [rpm-ostree daemon model](https://coreos.github.io/rpm-ostree/architecture-daemon/), [Fedora Atomic Desktop polkit policy](https://fedoraproject.org/wiki/Changes/UnprivilegedUpdatesAtomicDesktops), and [polkit architecture](https://www.freedesktop.org/software/polkit/docs/master/polkit.8.html). See [DEC-010](DECISIONS.md#dec-010-privilege-boundary-for-rollback-controls).

### QEMU firmware and boot profile

The Image Builder documentation for Fedora Kinoite's qcow2 image describes a UEFI/GPT disk using Fedora's GRUB EFI and shim packages. Axiom accepted inheriting this layout and avoiding custom bootloader changes in the first milestone. Secure Boot signing and key ownership remain separate security decisions. Validate the profile against the Fedora release used for Axiom's build. [Kinoite qcow2 image description](https://osbuild.org/docs/user-guide/image-descriptions/fedora-44/kinoite-qcow2/) and [Fedora 45 Atomic Desktop build change](https://fedoraproject.org/wiki/Changes/BuildAtomicDesktopsWithImageBuilder).

### Accepted direction and validation

Use Fedora 45 Kinoite with KDE Plasma as the initial foundation. Maintain an Axiom treefile, compose a versioned OSTree commit from pinned Fedora inputs, and use Image Builder to produce the x86-64 QEMU qcow2 artifact. Prefer Fedora's own base and image tooling for the prototype; treat Universal Blue as a source of build-process examples, not as a required project dependency. Keep the update/rollback implementation aligned with Fedora's deployment model.

This direction is recorded in [DEC-005](DECISIONS.md#dec-005-linux-foundation-and-vm-image-path), [DEC-008](DECISIONS.md#dec-008-custom-kinoite-image-pipeline), and [DEC-035](DECISIONS.md#dec-035-initial-fedora-release). It does not establish that the Fedora 45 Image Builder path meets Axiom's repeatability requirements or that Fedora's rollback path meets the newcomer usability goal. Validate those before making implementation or product claims.

### Desktop variant question

Both variants share the Atomic Desktop update model; the choice primarily sets the initial desktop interaction model. Silverblue uses GNOME, which opens to the Activities overview for finding windows and applications, including search by typing. Kinoite uses KDE Plasma, whose default panel includes an application launcher, taskbar, and system tray, with extensive customization. These are observable differences, not evidence that either is easier for all newcomers. Axiom selected Kinoite with KDE Plasma for its initial desktop in [DEC-006](DECISIONS.md#dec-006-initial-desktop-environment). [GNOME overview](https://help.gnome.org/gnome-help/shell-introduction.html), [KDE Plasma panel](https://docs.kde.org/stable_kf6/en/plasma-desktop/plasma-desktop/panel.html), [Fedora Silverblue](https://fedoraproject.org/atomic-desktops/silverblue/), and [Fedora Kinoite](https://fedoraproject.org/atomic-desktops/kinoite/).

## Sources

- [Fedora Atomic Desktops](https://fedoraproject.org/atomic-desktops/) — Fedora's current overview of Atomic desktop variants.
- [Fedora Atomic Desktops project wiki](https://fedoraproject.org/wiki/SIGs/AtomicDesktops) — project description of the rpm-ostree-based variants.
- [Universal Blue](https://universal-blue.org/) — project description of its Fedora Atomic-based images and customization approach.
- [NixOS manual](https://nixos.org/manual/nixos/stable/) — official system configuration and operation documentation.
- [How Nix Works](https://nixos.org/guides/how-nix-works/) — official explanation of declarative configuration, immutable store paths, and generations.
