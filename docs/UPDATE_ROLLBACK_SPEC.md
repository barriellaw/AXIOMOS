# System update and rollback: acceptance specification

**Status:** Acceptance specification. Nothing here has been exercised; no Axiom image has
been built or booted. This document defines what a validator must do and observe, not what
was observed.

This is the first release's headline workflow.
[DEC-003](DECISIONS.md#dec-003-smallest-useful-first-release) makes it the deliverable,
[DEC-009](DECISIONS.md#dec-009-system-update-and-rollback-experience) accepts the behavior,
and [DEC-010](DECISIONS.md#dec-010-privilege-boundary-for-rollback-controls) accepts the
privilege model. What none of them specifies is the procedure and the evidence, which
[Open question 5](OPEN_QUESTIONS.md) records as outstanding. This document supplies those.

## Scope

**Specified here:** the acceptance procedure, the failure states that must be exercised,
the privilege-boundary checks, and the evidence template.

**Deliberately deferred:** screen-by-screen flow, wording, and visual design. What KDE
Discover actually exposes is an open validation item in three accepted decisions
(DEC-020, DEC-021, DEC-022), so specifying an interface against unobserved behavior would
mostly generate rework. Design the interface after a session exists to observe; the
contract below constrains it either way.

**Not specified here and still undecided:** what the plain-language update summary must
contain. Drafted as DEC-055, which is **Proposed** — do not implement from it.

## What the accepted decisions require

| Requirement | Source |
|---|---|
| rpm-ostree remains the update and rollback mechanism; no second update engine | DEC-009, DEC-028 |
| Plasma Discover is used for update staging | DEC-009 |
| Unattended system updates are disabled by default | DEC-009 |
| A plain-language summary is shown, and the user is asked before staging and reboot | DEC-009 |
| An accessible "undo last system update" action uses the previous deployment | DEC-009 |
| The boot-menu fallback is documented for a system that cannot start | DEC-009 |
| Rollback is requested over rpm-ostree's system D-Bus API, authorized by existing polkit policy | DEC-010 |
| The interface stays unprivileged: no setuid helper, no root daemon, no broadened polkit rule | DEC-010 |
| Fedora's boundary is preserved: updates for active local users; passwordless rollback only for active local `wheel` administrators | DEC-010 |
| Rollback restores the system deployment only — never user files, and not necessarily application data | DEC-009, DEC-018 |

Exact D-Bus interface and polkit action names are **not asserted here.** DEC-010 requires
them confirmed during implementation validation, and `IMAGE_PIPELINE.md` forbids inferring
release-specific behavior from another release's documentation. Record the names observed on
the target system. See the
[rpm-ostree daemon model](https://coreos.github.io/rpm-ostree/architecture-daemon/) and the
[administrator handbook](https://coreos.github.io/rpm-ostree/administrator-handbook/)
(citation checked 2026-11-12).

## Preconditions

Before running the procedure, record:

1. The baseline QEMU profile and any deviation (DEC-038, DEC-040).
2. The image identity: qcow2 digest, OSTree commit checksum, and the `os-release` fields
   required by [DEC-045](DECISIONS.md#dec-045-build-identity-in-the-desktop-session).
3. The deployment list before any change, as the starting state to compare against.
4. That unattended updates are disabled, per DEC-009. **If they are enabled, stop.** The
   rest of the procedure assumes the user sees the change first, and an unattended update
   invalidates every step that follows.
5. Two accounts: one active local administrator in `wheel`, one active local non-administrator.
   The privilege boundary cannot be checked with one account.
6. A second Axiom commit to update *to*. A rollback test needs a deployment to roll back
   from, so this procedure requires two commits from the same pipeline, not one.

## Acceptance procedure

Each step states what the validator does and what must be true. A step that cannot be run
is recorded as blocked, with the reason — a blocked step is evidence
(see [`DOC_CONVENTIONS.md`](DOC_CONVENTIONS.md)).

### A — the update is explained before anything changes

Do: as the administrator, open the update interface with a newer Axiom commit available.

Must be true: an update is offered; a plain-language summary of what will change is shown
**before** staging begins; nothing has been staged yet. The deployment list is unchanged
from the precondition state.

### B — approval is required

Do: decline or dismiss the update.

Must be true: nothing is staged, the deployment list is still unchanged, and the system
remains on the original deployment after a reboot. An update that proceeds without approval
fails DEC-009 regardless of how clearly it was explained.

### C — staging and reboot

Do: approve the update. Observe staging. Reboot when prompted.

Must be true: the new deployment is staged rather than applied in place; the user is told a
reboot is needed; the running system is unaffected until the reboot. After reboot the new
deployment is the booted one, and the previous deployment is still present in the list.

### D — the new state is identifiable

Do: inspect the build identity in the session.

Must be true: the identity shown matches the new commit, through the `os-release` mechanism
in DEC-045 and legible without a terminal. Record both the displayed identity and the
deployment list.

### E — rollback is accessible and restores the previous deployment

Do: as the administrator, invoke the "undo last system update" action. Reboot.

Must be true: the action is reachable without a terminal and without knowing the word
"deployment"; it targets the previous deployment; after reboot the system runs the previous
commit, and the build identity shown matches it. Record which D-Bus method and polkit action
were used — this is the evidence for DEC-010's privilege boundary.

### F — the privilege boundary holds

Do: repeat the update as the **non-administrator**, then attempt rollback as the same user.

Must be true: the update is permitted without a password; rollback is **not** permitted
without authentication. Per DEC-010, passwordless rollback is limited to active local `wheel`
administrators, and Axiom adds no rule broadening that. If rollback succeeds passwordlessly
for a non-administrator, the polkit policy has been broadened somewhere and the gate fails.

### G — recovery when the updated system will not start

Do: with at least two deployments present, make the default deployment fail to reach a
graphical session, then recover using only the boot menu.

Must be true: the boot menu lists the previous deployment; selecting it reaches a working
session; the documented fallback procedure matches what the validator actually did. This is
the path a newcomer has when the interface is unreachable, so the documentation is the
deliverable as much as the behavior.

Inducing the failure is a test action, not a product behavior. Record the method used so the
result is reproducible as a procedure; do not ship it.

## Failure states that must be exercised

[DEC-010](DECISIONS.md#dec-010-privilege-boundary-for-rollback-controls) requires the
interface to handle these. Each is a separate observation, not an afterthought — an
unhandled authorization denial is how an unprivileged interface turns into a confusing dead
end.

| State | How to induce | Must be true |
|---|---|---|
| Authorization denied | Attempt rollback as a non-administrator and dismiss the prompt | A plain-language explanation of what was refused and what to do; no partial state change |
| Authentication prompt | Attempt rollback as a non-administrator | The prompt appears and is attributable to the system, not to an Axiom-authored dialog |
| No authentication agent | Run the interface in a session with no polkit agent available | A clear failure, not a hang and not a silent no-op |
| Operation fails mid-transaction | Interrupt the daemon during staging | The interface reports the failure; the deployment list is left in a consistent state |
| Rollback target absent | Attempt rollback with only one deployment present | The action is unavailable or explains why, rather than failing obscurely |

## What must not be claimed

Per DEC-009 and DEC-018, the interface and its help text must state that rollback restores
the system deployment only. It does **not** restore user files, and does not necessarily
restore application versions, Flatpak permissions, application settings, or application
data. [DEC-037](DECISIONS.md#dec-037-flatpak-acceptance-gate) keeps the application contract
separate from this gate, and conflating them is the likeliest way for this workflow to
overpromise.

Record the exact wording shown, so the claim can be audited against what the mechanism does.

## Evidence template

Copy into a dated report per [`DOC_CONVENTIONS.md`](DOC_CONVENTIONS.md):

```text
Date and operator:
Result: not run / blocked / failed / passed
Image identity (qcow2 digest, OSTree checksum, os-release fields):
Second commit used as the update target:
Baseline profile and deviations:
Accounts used (administrator / non-administrator):
Unattended updates confirmed disabled:
Deployment list before:
Step A — summary shown before staging:
Step B — approval required:
Step C — staging and reboot:
Step D — identity after update:
Step E — rollback action, D-Bus method and polkit action observed:
Step F — privilege boundary as non-administrator:
Step G — boot-menu recovery, and whether documentation matched:
Failure states exercised and behavior observed:
Exact wording shown about what rollback does not restore:
Deployment list after:
Steps blocked, and why:
Screenshots:
Known limitations:
```

## Verification class

These are **interaction checks** under
[DEC-047](DECISIONS.md#dec-047-verification-strategy-and-evidence-retention): they need a
person in the target session and are not automated for the first release. Steps C, D and E
produce deployment-list and identity output that a **boot check** can assert, so automate
those parts and keep the judgment — whether the summary is legible, whether the action is
findable — with the validator.

The authoritative list of requirements and their state is
[`REQUIREMENTS.md`](REQUIREMENTS.md).
