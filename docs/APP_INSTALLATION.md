# First-release application installation contract

This contract applies to the first Axiom desktop release based on Fedora Kinoite and KDE Plasma. It records the user-visible behavior required by [DEC-019](DECISIONS.md#dec-019-application-installation-boundary), [DEC-020](DECISIONS.md#dec-020-default-flatpak-application-source), [DEC-021](DECISIONS.md#dec-021-flatpak-permission-baseline), [DEC-022](DECISIONS.md#dec-022-flatpak-permission-management-tool), [DEC-037](DECISIONS.md#dec-037-flatpak-acceptance-gate), and [DEC-042](DECISIONS.md#dec-042-default-applications-and-removability). It is a target for implementation and validation, not a claim that the behavior already exists.

Per [DEC-037](DECISIONS.md#dec-037-flatpak-acceptance-gate), this full contract is required for first-release readiness but does not block the initial boot and system update/rollback workflow demo.

## Default applications

The selected first-release default apps are:

| App | Delivery | Source | Status |
|---|---|---|---|
| Brave Browser | Flatpak `com.brave.Browser` | Flathub | Selected (DEC-048). Brave's [Linux guidance](https://brave.com/linux/) says this build is not yet working as well as its native packages; that caveat belongs in the compatibility note (DEC-034). |
| ArtCraft launcher | Flatpak `ai.storyteller.artcraft-launcher` | ArtCraft's own OCI remote (DEC-050) | Selected (DEC-048). ArtCraft is not published on Flathub, so this requires the second default remote. Uninstall behavior and the remote's integrity properties need validation. |

Users must be able to uninstall each default app individually through a documented graphical path. Removing a default app must not remove unrelated apps or core desktop/system components. Explain whether app settings or user data remain after uninstall. These are product requirements, not claims that either app is currently included.

Both defaults are Flatpaks, so both are sandboxed, carry declared permissions, and can be removed through Discover without touching the base system. Neither is composed into the OSTree commit, and neither vendor's RPM or `.deb` repository is a composition input, which keeps [DEC-031](DECISIONS.md#dec-031-pin-and-audit-production-build-inputs) intact. See [DEC-042](DECISIONS.md#dec-042-default-applications-and-removability) and [DEC-048](DECISIONS.md#dec-048-default-application-delivery-for-brave-and-artcraft).

Upstream describes most Craft applications as in development or early alpha. That is a product risk for the newcomer audience in DEC-001 and belongs in the compatibility note required by DEC-034.

## Supported path

- Use KDE Discover with its Flatpak backend as the supported graphical path for installing, updating, and removing desktop applications.
- Enable exactly two Flatpak remotes by default, at system scope: the full Flathub remote, and ArtCraft's OCI remote for the default launcher ([DEC-050](DECISIONS.md#dec-050-permit-the-artcraft-oci-remote-as-a-second-default-application-source)). Enable no third remote.
- Make clear which remote supplies each application. With two sources of differing character — a broad catalog and a single vendor's registry — a newcomer needs to be able to tell them apart.
- Keep rpm-ostree package layering out of the supported newcomer application workflow. Do not remove or disable the native administrator command.

## Information users need before installing

- Show which source will provide the application.
- Show the application's requested permissions in plain language before the user confirms installation. Explain that the Flathub verification indicator, when available, indicates publisher identity; it is not an Axiom safety certification.
- If Discover does not expose the permission details or Flathub verification status, provide a clear link to the relevant Flathub listing and explain what the user should look for.
- Do not apply global or per-application Flatpak overrides that silently grant broader access than the application's declared permissions.
- Prefer portal-mediated, user-selected file or device access where supported. Explain that some applications may request static access or may not support portals for every function.

## User control and recovery

- Let users review and change application permissions through a documented, supported desktop tool. Resetting a change must have a clear explanation of its effect.
- Make application updates and removal available through Discover. Explain that rolling back the OS deployment does not necessarily revert Flatpak application versions, permissions, settings, or user data.
- Provide a simple route to find installed applications and understand which Flatpak source supplied each one.

## First-release acceptance criteria

The application-installation path is ready for release when all of the following are demonstrated in the target QEMU desktop:

1. Discover lists Flatpak applications from the full Flathub remote, and exactly one additional remote — ArtCraft's OCI remote — is enabled by default, with no third.
2. Before installation, a newcomer can identify the source and understand the application's declared permissions; if Discover lacks this information, the provided link and explanation work.
3. Publisher verification is described as an identity signal only and is not presented as proof that an application is safe.
4. Axiom does not silently broaden Flatpak permissions. User-mediated portals are used where the application and desktop support them.
5. A user can update and remove an installed Flatpak through Discover and can inspect or change its permissions using the documented tool.
6. Help text clearly distinguishes application changes from system deployments and states what OS rollback will not restore.
7. Brave Browser and the ArtCraft launcher are available by default and can each be uninstalled individually through Discover, without removing unrelated apps or core components.
8. A user can tell which remote supplied any installed application, and the ArtCraft remote's integrity behavior has been established — its documentation describes SHA-256 checksums and names no signing key, which DEC-012 makes security-relevant for a default-enabled remote.

The ArtCraft launcher installs and updates further Craft applications from within itself. Whether that in-app path presents source and permission information consistently with DEC-021 is an open validation item; if it does not, the contract's explanation requirements apply to it as to any other installation path.

[DEC-022](DECISIONS.md#dec-022-flatpak-permission-management-tool) selects KDE's Flatpak permissions page in System Settings (`flatpak-kcm`) as the supported management tool. The module's presence, path, labels, permission descriptions, and reset behavior still require target-session validation. Any Axiom-authored permission explanation UI also remains to be designed; see [Open questions](OPEN_QUESTIONS.md).
