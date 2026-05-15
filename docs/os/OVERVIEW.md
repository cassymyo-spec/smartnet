# OS Layer Overview

This document explains the NeurWall OS layer in Phase 1. It defines why Alpine Linux is the base, what the OS layer is responsible for, what it must not absorb from higher layers, and how the OS build system should be organized as the project moves from concept to a bootable image.

## Why Alpine Linux

Alpine Linux was chosen because Phase 1 needs a small, predictable, reproducible base for a firewall appliance rather than a general-purpose server distribution.

Alpine fits that goal for four reasons:

- It is lightweight, which matters for low-cost x86 appliances and future ARM targets.
- It is simple to reason about, which reduces friction for contributors building and debugging a custom image.
- Its package model is small and explicit, which helps keep the OS footprint tight.
- It is well suited to building minimal bootable systems where networking and firewall behavior matter more than bundled userland features.

For NeurWall, Alpine is not a branding choice. It is an engineering choice thus small base image, fast boot, fewer moving parts, and a cleaner path to a reproducible firewall OS.

## What The OS Layer Does

In Phase 1, the OS layer is responsible for turning NeurWall into a real appliance that can boot and enforce network policy.

Its responsibilities are:

- Boot the system on bare metal and in a VM
- Provide the base Linux runtime and package set
- Configure networking primitives needed for a firewall device
- Run `nftables` as the packet-filtering mechanism
- Host the low-level services and configuration needed for future backend integration
- Expose stable local endpoints or service boundaries that a future backend can attach to

In practical terms, the OS layer owns the machine lifecycle:

- Build an ISO
- Boot the ISO
- Bring up the network interfaces
- Load and apply firewall rules
- Keep the device in a known, inspectable state

For Phase 1, that is enough. The system does not need intelligence yet. It needs to be real, bootable, and reproducible.

## What The OS Layer Does Not Do

The OS layer is deliberately narrow. It is not the product surface and it is not where higher-level decision-making belongs.

The OS layer does not:

- Implement a UI or dashboard
- Run AI or ML models
- Contain business logic or product workflows
- Decide high-level policy from user intent
- Explain decisions in natural language
- Replace the backend control plane

That separation is important. If the OS layer starts absorbing UI, AI, or product logic, the architecture becomes harder to maintain and Phase 1 scope becomes unclear.

## OS Build System Structure

The OS build system should be organized so contributors can answer three questions quickly:

- What files define the image
- What files configure boot and networking
- What files install and enable firewall behavior

Phase 1 should use a structure close to this:

```text
os/
  build/
    Makefile
    profile/
      packages.txt
      world
      repositories
    scripts/
      build-iso.sh
      prepare-rootfs.sh
      validate-image.sh
  rootfs/
    etc/
      nftables/
        ruleset.nft
      network/
      init.d/
      local.d/
  config/
    boot/
    network/
    services/
  docs/
    decisions/
```

### What Each Part Is For

- `os/build/` contains the reproducible build entrypoints and image assembly logic.
- `os/build/profile/` defines the package set and build inputs for the NeurWall image.
- `os/build/scripts/` contains the scripts that assemble, validate, and eventually publish build artifacts.
- `os/rootfs/` contains files copied into the final image, including `nftables` rules and service configuration.
- `os/config/` contains higher-level configuration inputs for boot behavior, networking, and enabled services.
- `os/docs/decisions/` stores OS-specific architecture notes that explain why particular low-level choices were made.

The exact filenames may change, but the separation should no build logic, root filesystem contents, and configuration inputs should remain distinct.

## Endpoints For Future Backend Integration

Phase 1 does not need a full backend, but the OS layer should avoid blocking one.

That means the OS should be prepared to expose or support:

- Local service sockets or HTTP endpoints for status and control
- Readable system state for firewall status, interfaces, and loaded rules
- Stable locations for logs, metrics, and runtime files
- A clean mechanism for a backend service to inspect or update firewall policy

In Phase 1, these can be minimal or even stubbed, but the OS should be built with that integration path in mind.

## Definition Of Done For Phase 1

Phase 1 is done when all of the following are true:

- A contributor can build a NeurWall ISO from source using a documented process
- The ISO can be written to removable media or attached to a VM
- The system boots successfully on target hardware or a virtual machine
- `nftables` is installed, enabled, and actively filtering packets
- The OS layout and build steps are documented clearly enough for a new contributor to follow without guesswork
- The separation between OS responsibilities and future backend or AI responsibilities is documented and preserved

If any of those are missing, Phase 1 is not complete.

## Contributor Guidance

When working in the OS layer, optimize for:

- Small and understandable changes
- Reproducible builds
- Explicit configuration
- Clear boot and firewall behavior
- Documentation that explains not just what changed, but why

The OS layer is the foundation. If it is unclear or unstable, every later phase inherits that problem.
