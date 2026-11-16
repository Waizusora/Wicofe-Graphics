# Graphics Documentation Index

This directory documents reusable userspace graphics and display-stack infrastructure for Wicofe.

## Architecture and design

- [Graphics and display subsystem architecture 0.1](GRAPHICS_SUBSYSTEM_ARCHITECTURE_0.1.md) — draft boundaries, display discovery, buffer ownership, rendering, composition, synchronization, security and validation requirements.

## Scope
- Display discovery/configuration interfaces.
- Rendering APIs and backend integration.
- Compositor-facing graphics infrastructure.
- Hardware-acceleration userspace integration.
- Display diagnostics, compatibility data, and tests.

## Ownership
Kernel display drivers and low-level device interfaces belong to [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel) and [Wicofe-Drivers](https://github.com/Waizusora/Wicofe-Drivers). [WDE](https://github.com/Waizusora/WDE) owns desktop composition, windows, panel, launcher, and session behavior. This repository provides reusable graphics infrastructure.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
