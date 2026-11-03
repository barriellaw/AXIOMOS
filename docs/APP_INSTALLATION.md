# First-release application installation contract

This contract applies to the first Axiom desktop release based on Fedora Kinoite and KDE Plasma. It records the user-visible behavior required by [DEC-019](DECISIONS.md#dec-019-application-installation-boundary), [DEC-020](DECISIONS.md#dec-020-default-flatpak-application-source), and [DEC-021](DECISIONS.md#dec-021-flatpak-permission-baseline). It is a target for implementation and validation, not a claim that the behavior already exists.

## Supported path

- Use KDE Discover with its Flatpak backend as the supported graphical path for installing, updating, and removing desktop applications.
- Enable the full Flathub remote by default. Do not enable other Flatpak remotes by default in the first release.
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

1. Discover lists Flatpak applications from the full Flathub remote, and no additional Flatpak remote is enabled by default.
2. Before installation, a newcomer can identify the source and understand the application's declared permissions; if Discover lacks this information, the provided link and explanation work.
3. Publisher verification is described as an identity signal only and is not presented as proof that an application is safe.
4. Axiom does not silently broaden Flatpak permissions. User-mediated portals are used where the application and desktop support them.
5. A user can update and remove an installed Flatpak through Discover and can inspect or change its permissions using the documented tool.
6. Help text clearly distinguishes application changes from system deployments and states what OS rollback will not restore.

DEC-022 proposes KDE's Flatpak permissions page in System Settings as the supported management tool; it remains pending acceptance and target-session validation. Any Axiom-authored permission explanation UI also remains to be designed; see [Open questions](OPEN_QUESTIONS.md).
