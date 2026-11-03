# Roadmap

This is a sequence of decision gates, not a promise of dates. Move a stage forward when its exit conditions are documented and reviewable.

## 0. Project definition — current

- Agree on the audience and the problem Rusty OS should solve.
- Choose an initial hardware target and define what “first boot” means.
- Decide the system composition and image-building approach.
- Define the security, update, and maintenance expectations.

**Exit:** Record the foundational choices and their trade-offs in `docs/DECISIONS.md`; update this roadmap if the chosen scope changes.

## 1. Minimal bootable system

- Establish a reproducible build from pinned, documented inputs.
- Produce a bootable image for the chosen target.
- Reach a useful, observable initial state and document how to reproduce it.

**Exit:** A contributor can follow documented steps to build and boot the image, and the result is verified on the agreed target.

## 2. Foundational system services

- Add only services needed by the agreed first use case.
- Define privilege boundaries, logging, configuration, and failure behavior.
- Use Rust for new project-owned components where the language policy supports it.

**Exit:** The minimal system has a documented service model and repeatable checks for its core behavior.

## 3. Installation, updates, and maintenance

- Define installation and recovery flows for the target audience.
- Implement and verify a secure update and rollback strategy.
- Document release, support, and vulnerability response policies.

**Exit:** Users can install, update, and recover using documented, tested procedures.

## 4. Broader hardware and user experience

- Expand hardware support based on user needs and upstream Linux capabilities.
- Develop the user environment that matches the chosen audience.
- Publish support expectations and compatibility information.

**Exit:** The project can explain who it serves, what hardware it supports, and how releases are maintained.
