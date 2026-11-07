# Roadmap

This is a sequence of decision gates, not a promise of dates. Move a stage forward when its exit conditions are documented and reviewable.

## 0. Project definition — decision baseline recorded

The initial audience, first-release problem and scope, Fedora 45 Kinoite/KDE foundation, treefile → OSTree → Image Builder pipeline, QEMU x86-64 target, update model, security expectations, and maintenance direction have accepted records in `docs/DECISIONS.md`.

**Exit:** The foundational decision baseline is recorded. Further decisions are tracked in [Open questions](OPEN_QUESTIONS.md) and resolved when their implementation gate requires them.

## 1. Validate the custom image pipeline — current

Follow the [prototype image-pipeline validation runbook](IMAGE_PIPELINE.md) and attach a dated build report with the gate evidence.

- Validate Fedora 45 inputs and document the fixed baseline QEMU machine profile.
- Validate the treefile composition, resolved-input pinning, and Image Builder OSTree-to-qcow2 path on the supported Fedora build host.
- Boot the resulting image to the graphical desktop and record the exact build identity and any deviations from the target profile.
- Defer Secure Boot validation and Axiom-owned Secure Boot signing until closer to public release, as recorded in `DECISIONS.md`.
- Keep production key custody and production reproducibility claims out of scope; record the evidence and limits of prototype repeatability.

**Exit:** A contributor can follow the documented build steps on the supported host, produce a qcow2 image from identified inputs, and boot it to an interactive Plasma session in the agreed QEMU profile. The build report records pinned inputs, tool versions, failures, and known reproducibility limits. No production support or security claim is implied.

## 2. Foundational system services

- Add only services needed by the agreed first use case.
- Define privilege boundaries, logging, configuration, and failure behavior.
- Use Rust for new project-owned components where the language policy supports it.

**Exit:** The minimal system has a documented service model and repeatable checks for its core behavior.

## 3. First-release workflow and maintenance

- Implement and verify the newcomer-facing update explanation, approval, staging, rollback, and recovery workflow already scoped in `DECISIONS.md`.
- After the focused boot and system update/rollback demo, demonstrate and satisfy the full first-release application path in `docs/APP_INSTALLATION.md` before declaring release readiness.
- Define installation and recovery flows for any target beyond the QEMU prototype.
- Implement and verify the signed update and rollback strategy for production distribution.
- Document release, support, and vulnerability response policies.

**Exit:** Users can install, update, and recover using documented, tested procedures.

## 4. Broader hardware and user experience

- Expand hardware support based on user needs and upstream Linux capabilities.
- Develop the user environment that matches the chosen audience.
- Publish support expectations and compatibility information.

**Exit:** The project can explain who it serves, what hardware it supports, and how releases are maintained.
