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

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.

## Wicofe-wide principles

Wicofe is an independent Unix-like, POSIX-compatible operating system and software ecosystem, designed as an alternative to established and widely adopted software ecosystems and technologies, including GNU. Native components must use Wicofe interfaces and must not require Linux libc or GNU compatibility layers. Prefer permissive licenses; avoid GPL/AGPL, and consider LGPL only when no suitable permissive alternative exists. Reuse or fork upstream software when implementing it from scratch would be disproportionately difficult. See the [canonical project principles](https://github.com/Waizusora/Wicofe-Docs/blob/main/PROJECT_PRINCIPLES.md).
