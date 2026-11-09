# Product vision

## Working identity

**Axiom OS** is the working name for an open-source, Linux-based desktop operating system. The name is not yet confirmed as the final public identity.

## Vision

Give people meaningful control over their computers without requiring them to understand every underlying technical detail. Axiom OS should pair approachable defaults and explanations with advanced controls in the same system, so users do not have to choose between ease of use and flexibility.

The initial audience is desktop newcomers, and the first-release problem is approachable system management with recoverable changes. The first useful release is a reproducible x86-64 VM desktop demonstrating system update and rollback. ARM64 remains a possible later target.

## Product goals

- Make application, update, driver, and system settings understandable and manageable from consistent interfaces.
- Make important system changes recoverable where the underlying components allow it, and clearly explain when recovery is partial or unavailable.
- Support the established Linux application ecosystem and investigate practical Windows application and game compatibility through existing projects.
- Offer meaningful customization, strong security and privacy defaults, and continued access to advanced system controls.
- Work without requiring an AI assistant or a continuous internet connection.

These are product goals, not claims of current functionality or promises of universal compatibility. Compatibility, reversibility, and hardware support must be verified and described with their limitations.

## Design commitments

- Build on Linux rather than beginning with a replacement kernel.
- Prefer Rust for newly written project system software when it is a sound technical choice; preserve mature software and established interfaces where they serve users better.
- Research existing open-source components before proposing replacements.
- Keep users informed and in control of privileged, security-sensitive, or difficult-to-reverse changes.
- Treat recovery, security, maintainability, and evidence-based compatibility claims as architectural concerns.

## Technology candidates

The source specification names technologies such as Btrfs, Flatpak, Wine, Proton, and Wayland as preferences to evaluate. KDE Plasma on Fedora Kinoite is selected as the initial desktop environment in [Decision records](DECISIONS.md). Compare other candidates against the chosen audience and target before adopting them.
