# Treefile specification

**Status:** Specification. No Axiom treefile exists and none has been composed. Every
requirement below traces to a decision record; a requirement with no citation is a defect
in this document. The YAML in [Illustrative shape](#illustrative-shape) is **illustrative
and non-authoritative** — key names must be confirmed against the Fedora 45
[rpm-ostree treefile reference](https://coreos.github.io/rpm-ostree/treefile/), because
`IMAGE_PIPELINE.md` forbids inferring Fedora 45 behavior from earlier releases.

This document says what the treefile must contain, exclude and configure. It does not
cover the build procedure, which is
[`IMAGE_PIPELINE.md`](IMAGE_PIPELINE.md), or the host, which is
[`BUILD_HOST.md`](BUILD_HOST.md).

## Derivation

Per [DEC-049](DECISIONS.md#dec-049-treefile-derivation-from-fedoras-configuration):

- **Source.** Fedora's Atomic Desktop manifests are maintained in Fedora's
  [Atomic Desktops config repository](https://forge.fedoraproject.org/atomic-desktops/config).
  Vendor that repository's Kinoite manifest and whatever it includes, at a pinned revision,
  into `image/treefile/fedora/`. Confirm the manifest's filename and include structure on
  the branch matching the selected Fedora release rather than assuming it is stable across
  releases.
- **Include, do not copy.** The Axiom treefile includes Fedora's Kinoite manifest from that
  vendored copy, pinned to a specific revision, and overrides only what Axiom changes.
- **Do not restate Fedora's package list.** If a package appears in the Axiom treefile, it
  is because Axiom added or removed it. That is what makes
  [DEC-028](DECISIONS.md#dec-028-inherit-fedora-kinoite-base-components)'s
  inherit-don't-replace commitment checkable by reading one file.
- **Record the pinned revision** in build evidence
  ([DEC-041](DECISIONS.md#dec-041-prototype-reproducibility-evidence)) and in the
  provenance manifest
  ([DEC-026](DECISIONS.md#dec-026-production-build-provenance-record)).
- **A base-release migration is a revision bump plus review of the differences**, not a
  rewrite.

## Identity and versioning

| Requirement | Value | DEC |
|---|---|---|
| Ref | `axiom/<fedora-release>/<arch>/<variant>`, e.g. `axiom/45/x86_64/kinoite` | DEC-044 |
| Release version | `<fedora-release>.<serial>`, e.g. `45.1`, in commit version metadata | DEC-044 |
| Prototype build version | `<fedora-release>.0-dev.<short-source-revision>` | DEC-044 |
| `os-release` fields | `NAME="Axiom OS"`, `ID=axiom`, `ID_LIKE=fedora`, `VERSION_ID` (release version), `VARIANT`, `BUILD_ID` (short OSTree checksum), `HOME_URL`, `BUG_REPORT_URL` | DEC-045 |

`BUILD_ID` depends on the commit checksum, which is not known until the commit exists.
Whether it can be set during composition or must be applied afterward is an open
validation item; record which mechanism was used.

The identity must be legible in the session **without a terminal**, through KDE's System
Settings "About this System" page, which reads `os-release`
([DEC-045](DECISIONS.md#dec-045-build-identity-in-the-desktop-session)). No Axiom splash
screen, desktop watermark, or About application is added in the first milestone.

## Must contain

| Package / content | Why | DEC |
|---|---|---|
| `flatpak-kcm` | The supported Flatpak permission-management path is System Settings → Application Permissions → Flatpak Permissions | DEC-022 |
| KDE Plasma desktop, via the included Fedora Kinoite manifest | Initial desktop environment | DEC-006 |
| Flatpak support and KDE Discover, via the included manifest | The supported application path | DEC-019 |

Axiom adds very little. That is intended: the first release inherits Fedora's tested
desktop (DEC-028) and the pipeline is what Stage 1 validates, not a distinct package set.

## Must not contain

| Excluded | Why | DEC |
|---|---|---|
| `flatseal` | `flatpak-kcm` is the supported tool; users may install Flatseal themselves from Discover | DEC-022 |
| Brave Browser as an RPM | Delivered as the Flathub Flatpak `com.brave.Browser`; an RPM in the commit could not be removed through Discover | DEC-048, DEC-042 |
| ArtCraft as an RPM, `.deb`, AppImage or tarball | Delivered as the Flatpak `ai.storyteller.artcraft-launcher`; same removability reason | DEC-048, DEC-042 |
| Any replacement for the kernel, systemd, glibc, or the rpm-ostree/OSTree stack | Inherited, not replaced, in the first release | DEC-028 |
| Any package from a repository other than the named Fedora repositories | Only named Fedora repositories are enabled as composition inputs | DEC-031 |
| An SSH server, desktop-sharing service, telemetry agent, or log-upload agent **enabled by default** | Local-first diagnostics; remote access requires deliberate user action | DEC-023 |

DEC-023 forbids these services being *enabled by default*; it does not require removing a
package the user may deliberately enable later. Where the included Fedora manifest ships
such a service, disable the unit rather than excluding the package, and record which
approach was used.

## Must configure

| Setting | Required state | DEC |
|---|---|---|
| Unattended system updates | **Disabled**, so the user sees and approves the change first. Fedora Kinoite enables them by default, so this is an override Axiom must make. | DEC-009 |
| Flatpak remotes | Exactly two, at system scope: the full Flathub remote, and ArtCraft's OCI remote `https://getartcraft.com/artcraft-oci.flatpakrepo`. No third remote. | DEC-020, DEC-050 |
| Flatpak overrides | None that broaden access beyond an application's declared permissions | DEC-021 |
| Journal storage | Fedora's bounded journald defaults, local only | DEC-023 |
| Base update source | The `axiom` OSTree remote only; no Fedora deployment remote and no automatic fallback | DEC-015, DEC-044 |
| Deployment remote signature verification | Required; never disabled for a release remote | DEC-014, DEC-025 |

Two of these need validation rather than assumption:

- **Unattended updates.** Identify the exact unit or configuration Fedora 45 Kinoite uses,
  and confirm the override holds in the booted image rather than only in the tree. This is
  the single most load-bearing configuration requirement in the first release, because
  DEC-009's promise to explain an update before applying it depends on it.
- **Flatpak remote configuration.** ArtCraft's own instructions use `--user` scope; a
  default remote must be system scope. Confirm the supported mechanism for declaring
  system remotes in a composed tree, and that both remotes appear in a booted image.

## Repository configuration

Per [DEC-031](DECISIONS.md#dec-031-pin-and-audit-production-build-inputs): keep repository
definitions in `image/repos/`, enable only named Fedora repositories, and verify Fedora
RPM signatures during composition. No vendor repository — Brave's or ArtCraft's — is a
composition input (DEC-048).

## Input pinning

| Stage | Requirement | DEC |
|---|---|---|
| Prototype | Use the strongest snapshot and package-locking mechanism the Fedora 45 toolchain can actually enforce; record repository metadata identity and resolved RPM NEVRAs; report what could not be pinned or retained | DEC-039 |
| Production | Generate and **strictly enforce** an architecture-specific lockfile covering all resolved RPMs, in `image/lockfiles/`; review lockfile changes as source changes; pin tool versions | DEC-031 |

rpm-ostree's lockfile support has been documented as experimental, so DEC-031 requires
validating the workflow on Fedora 45 before production and **forbids publishing a
production image if the toolchain cannot enforce resolved inputs**. Prototype builds are
not held to that bar (DEC-039), and prototype evidence must not be described as
reproducible (DEC-041).

## Default application seeding

This is the part most likely to surprise an implementer. System-wide Flatpaks live under
`/var`, which is **not** part of an OSTree commit — `/var` is populated at deployment
time. So default applications cannot simply be listed in the treefile the way RPMs are.

Per [DEC-048](DECISIONS.md#dec-048-default-application-delivery-for-brave-and-artcraft):
seed the defaults at image build time if a mechanism exists. If Fedora 45 provides no
offline seeding mechanism and the defaults must install over the network at first boot,
**state the network requirement explicitly** rather than implying offline capability —
`VISION.md` sets working without a continuous internet connection as a product goal, and
first-run downloads would qualify that goal.

Determining which mechanism Fedora 45 supports is an open validation item for Stage 1.

## What this document does not decide

- The plain-language content of the update summary (DEC-009) — belongs to the
  update/rollback specification.
- Which ArtCraft component beyond the launcher, if any, is a default — DEC-048 selects the
  launcher only.
- Whether ArtCraft's OCI remote provides signature verification. Its documentation
  describes SHA-256 checksums and names no signing key, which DEC-012 makes
  security-relevant for a default-enabled remote. Must be established before release
  (requirement DEC-050c in [`REQUIREMENTS.md`](REQUIREMENTS.md)).

## Illustrative shape

**Non-authoritative.** Confirm every key against the Fedora 45 rpm-ostree treefile
reference before writing a real treefile; this sketch shows intent and the citation style,
not verified syntax.

```yaml
# image/treefile/axiom-kinoite.yaml - ILLUSTRATIVE ONLY
include: fedora/kinoite.yaml        # DEC-049: include, do not copy
ref: axiom/45/x86_64/kinoite        # DEC-044

packages:
  - flatpak-kcm                     # DEC-022: supported permission tool

exclude-packages:
  - flatseal                        # DEC-022: not installed by default

# os-release identity (DEC-045) and the unattended-update override (DEC-009)
# are applied as configuration/postprocess steps; the supported mechanism for
# each must be confirmed on Fedora 45 before this is written.
```

The shortness is the point. If this file grows a long package list, either Axiom has
diverged from DEC-028's inherit-don't-replace commitment, or the include in DEC-049 is not
being used as intended.

## Verification

Requirements here are checked as **composition checks** (against the tree, before boot)
and **boot checks** (in the baseline QEMU profile), as defined by
[DEC-047](DECISIONS.md#dec-047-verification-strategy-and-evidence-retention). The
authoritative list of what to check, and its current state, is
[`REQUIREMENTS.md`](REQUIREMENTS.md). Record results in a dated report per
[`DOC_CONVENTIONS.md`](DOC_CONVENTIONS.md).
