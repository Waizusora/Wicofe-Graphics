# Wicofe-Graphics

Userspace graphics and display stack components for Wicofe OS.

## Scope

- Display discovery and configuration interfaces.
- Rendering and graphics API integration.
- Compositor-facing graphics support.
- Hardware acceleration userspace integration where supported.
- Display diagnostics, compatibility data, and tests.

## Boundaries

Kernel display drivers and low-level device interfaces belong in Wicofe-Kernel or Wicofe-Drivers. WDE owns desktop composition, window management, panel, launcher, and session behavior. This repository provides reusable graphics infrastructure and interfaces.

## Status

Initial repository baseline; graphics API and backend selection remain open design work.

## License

Original project code targets MPL-2.0. Graphics stacks, protocol implementations, firmware, and driver dependencies require individual license and provenance review.
