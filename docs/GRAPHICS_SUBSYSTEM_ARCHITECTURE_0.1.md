# Graphics and Display Subsystem Architecture 0.1

**Status:** Draft proposal. This document defines boundaries and design requirements; it does not claim that a display server, compositor, GPU driver, or graphics API is implemented.

## 1. Purpose

Define a maintainable userspace graphics stack for Wicofe OS across x86-64 and AArch64. The design separates device control, buffer management, rendering, composition, and desktop policy so that a basic framebuffer console can work before accelerated graphics is available.

## 2. Repository ownership

| Component | Responsibility |
|---|---|
| Wicofe-Kernel | Privileged memory mapping, interrupt and process primitives, device-I/O foundations, VFS, syscall implementation |
| Wicofe-Drivers | Display/GPU device drivers, hardware discovery, modesetting and device-specific command submission where assigned |
| Wicofe-ABI | Stable userspace-visible syscall and object contracts |
| Wicofe-Graphics | Reusable userspace display, buffer, rendering and backend infrastructure |
| WDE | Desktop compositor, windows, surfaces policy, shell/panel, session and user interaction |
| Wicofe-Input | Input device events and seat/session routing |
| Wicofe-Specs | Cross-repository protocol and compatibility decisions |
| Wicofe-Tests | Cross-component conformance and regression suites |

No repository may silently duplicate another repository's canonical ABI or device ownership.

## 3. Layered architecture

1. **Hardware and driver layer:** discovers display controllers and GPUs, reports capabilities, controls device-specific resources and handles interrupts.
2. **Kernel device/memory layer:** mediates privileged access, validates mappings and provides process isolation and synchronization primitives. It must not embed desktop policy.
3. **Userspace device backend:** enumerates display devices, queries capabilities, configures outputs through an explicitly versioned interface and manages device lifecycle.
4. **Buffer and rendering layer:** allocates/imports shareable buffers, tracks formats/modifiers, performs CPU rendering or submits work to an available acceleration backend.
5. **Composition layer:** combines client surfaces, handles damage and frame scheduling, and presents completed frames.
6. **WDE desktop layer:** owns windows, decorations, workspace policy, panels, launchers, session behavior and user-facing display settings.

The initial fallback path should support a simple linear framebuffer or equivalent boot-provided scanout where hardware and kernel support permit. Accelerated rendering is optional and must not be a prerequisite for boot or basic recovery UI.

## 4. Display and device model

Represent each physical or virtual display endpoint with a stable runtime identifier, connector state, supported modes, current mode, physical dimensions when known, orientation, and capability flags. Runtime identifiers must not be treated as persistent hardware identity.

Hotplug and removal are asynchronous lifecycle events. A removed device invalidates its output resources; clients must receive a defined failure/event and must not continue submitting to stale handles.

Mode changes require authorization, validation against advertised modes, and an explicit completion or failure result. A mode change must not leave userspace assuming that a requested mode became active before the driver confirms it.

## 5. Buffer contract

A buffer description must eventually define:
- dimensions, pixel format, plane count, per-plane offsets and strides;
- modifier or tiling information when applicable;
- allocation size and alignment constraints;
- ownership, access rights and lifetime;
- synchronization requirements for producer/consumer access;
- whether the buffer is CPU-mappable, scanout-capable or importable by a GPU.

Never infer a hardware layout from a pixel-format name alone. Validate dimensions and all offset/stride/size arithmetic with overflow-safe operations. A process must not gain arbitrary physical-memory access by requesting a display buffer.

Buffer sharing must use explicit kernel-mediated handles or another reviewed capability mechanism. Handle transfer, revocation, process exit and device removal need defined behavior.

## 6. Rendering and acceleration

Provide a CPU fallback renderer for essential operations where practical. Hardware acceleration is a backend, not an assumption. Backend selection must be capability-driven and expose a clear fallback when initialization, allocation, compilation or submission fails.

GPU command submission is untrusted input. The kernel/driver boundary must validate object ownership, address ranges, command-buffer bounds and synchronization. Userspace must not receive unrestricted device-register or physical-memory access.

Do not promise a particular graphics API (for example Vulkan, OpenGL, or a proprietary interface) until dependency licensing, driver availability, ABI, and maintenance costs have been evaluated.

## 7. Composition and presentation

WDE owns desktop composition policy. Wicofe-Graphics supplies reusable surface/buffer/rendering primitives and presentation backends; it does not own window management.

The composition contract should define:
- surface creation, resize, destruction and visibility;
- damage regions and buffer age semantics, if supported;
- acquire/release synchronization;
- frame callbacks and presentation timestamps;
- output selection and scaling;
- behavior for occlusion, minimized surfaces and lost outputs.

A frame must not be scanned out while its producer is still writing to it. The design must specify fences or an equivalent synchronization mechanism before asynchronous GPU paths are considered safe.

## 8. Security and isolation

- Authenticate or authorize display-control operations by session/seat policy.
- Keep device file/object permissions explicit.
- Validate all user-provided dimensions, formats, offsets and command streams.
- Isolate clients' buffers and prevent cross-process reads/writes.
- Define cleanup after client crash, compositor restart, GPU reset and hot-unplug.
- Avoid exposing firmware, kernel mappings or raw device addresses to untrusted clients.

## 9. Failure handling and observability

Define recoverable errors for unsupported mode/format, allocation failure, device removal, backend initialization failure, GPU hang/reset, invalid synchronization and permission denial. Preserve a CPU/basic-display fallback where possible. Diagnostics should record device/backend identity, error class and recovery action without leaking client buffer contents.

## 10. Testing strategy

Required test layers:
- pure unit tests for format validation, layout arithmetic, mode selection and damage calculations;
- ABI/layout and handle-permission tests;
- mock-backend tests for hotplug, mode changes, failed allocation and device loss;
- compositor tests for buffer lifetime and synchronization;
- emulator tests for basic display output;
- hardware tests for each claimed device/backend;
- fuzzing of untrusted protocol messages and buffer descriptors.

A build or emulator boot is not evidence of GPU acceleration or hardware coverage. Report support separately by device, backend and test level.

## 11. Open decisions

- Canonical display-control and buffer-object ABI.
- Whether a dedicated display service owns device enumeration or this is part of a broader system service.
- Initial compositor/client protocol and its versioning policy.
- First CPU renderer and supported pixel formats.
- GPU backend/API selection and third-party license review.
- Fence/synchronization primitive and scanout ownership model.
- Multi-seat, multi-GPU, color management, HDR and variable-refresh scope.
- Whether the initial boot framebuffer is exposed through a transitional compatibility interface.

## 12. Completion criteria

This proposal can move toward implementation only after the display-control ABI, buffer lifetime/synchronization contract, driver ownership, compositor protocol, security model and test matrix are reviewed across Wicofe-Kernel, Wicofe-Drivers, Wicofe-ABI, WDE and Wicofe-Specs. Until then, all interfaces here are conceptual and non-stable.
