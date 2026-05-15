# NeurWall Architecture

NeurWall is an open-source firewall operating system built on Alpine Linux. Its goal is to do more than block traffic: over time, it should observe network behavior, learn what is normal, adapt firewall rules automatically, and explain its decisions in plain English. The system is local-first, with the OS, enforcement path, backend services, and operator interface all running on the device rather than depending on a cloud control plane.

## Purpose Of This Document #

This is the top-level architecture for contributors. It describes the major system layers, the delivery phases, and the boundary between the operating system layer and the application stack that runs on top of it.

## Delivery Phases

NeurWall is built in three phases: `Foundation`, `Interface`, and `Brain`. The phases are ordered deliberately. Each one adds capability on top of the previous layer rather than replacing it.

### 1. Foundation

Foundation delivers the minimum system needed for NeurWall to function as a real firewall appliance. This is the current project focus.

Phase 1 is complete when a contributor can:

- Burn a NeurWall ISO to a USB drive
- Boot it on bare metal or in a VM
- Verify that the firewall is running and actively filtering packets through `nftables`
- Read the project documentation and understand how the system was built and why key decisions were made

Foundation includes:

- A bootable NeurWall image based on a minimal Linux distribution
- Core networking and packet-processing primitives
- Packet capture and flow telemetry
- Basic storage for local telemetry and state
- A deterministic rule engine that can apply and update firewall policy
- A reproducible build path for producing the OS image
- Core architecture and build documentation for new contributors

The outcome of this phase is a bootable, inspectable, reproducible firewall platform. Documentation is part of the deliverable, not a follow-up task.

### 2. Interface

Interface delivers the operator-facing control plane once the firewall appliance itself is stable.

It includes:

- A backend management API for system status, telemetry, and rule control
- A frontend dashboard for viewing traffic, events, and system state
- Human-readable explanations of what the system is doing
- The initial contributor and operator surface for interacting with NeurWall

The outcome of this phase is a system that can be inspected and operated clearly instead of behaving like a black box.

### 3. Brain

Brain delivers adaptive intelligence on top of the Foundation and Interface layers.

It includes:

- Traffic classification models for detecting normal and abnormal behavior
- Decision logic that can recommend or apply rule changes
- On-device language-model reasoning for explaining actions in plain English
- A path toward reinforcement-learning-driven policy adaptation

The outcome of this phase is a firewall that does not just enforce rules, but learns from traffic patterns and helps justify its decisions.

## Phase 1 Deliverables

The Foundation phase is expected to produce:

- A bootable Alpine-based NeurWall ISO
- Active packet filtering through `nftables`
- A reproducible build system
- `ARCHITECTURE.md` for the top-level system and phase map
- `docs/os/OVERVIEW.md` for the OS-layer deep dive
- `docs/os/BUILD.md` for building the ISO from source
- `CONTRIBUTING.md` for repo workflow and contribution guidance

## Component Map

```text
                         +----------------------+
                         |      Frontend        |
                         |  Web dashboard/UI    |
                         +----------+-----------+
                                    |
                                    v
                         +----------------------+
                         |       Backend        |
                         | API, orchestration,  |
                         | telemetry services,  |
                         | explanation layer    |
                         +----------+-----------+
                                    |
                +-------------------+-------------------+
                |                                       |
                v                                       v
      +----------------------+              +----------------------+
      |      Brain Layer     |              |   Enforcement Path   |
      | ML classifier, LLM,  |              | rule engine, policy  |
      | adaptive decisioning |              | compiler, nftables   |
      +----------+-----------+              +----------+-----------+
                 |                                     |
                 +-------------------+-----------------+
                                     |
                                     v
                         +----------------------+
                         |       OS Layer       |
                         | Linux base, drivers, |
                         | boot image, packet   |
                         | capture, eBPF/libpcap|
                         +----------+-----------+
                                    |
                                    v
                         +----------------------+
                         | Network Interfaces   |
                         | WAN / LAN traffic    |
                         +----------------------+
```

### Relationship Between OS, Backend, And Frontend

- The OS layer owns the appliance runtime: boot, networking primitives, packet access, and low-level enforcement hooks.
- The backend sits on top of the OS layer and turns those low-level capabilities into system services, APIs, telemetry handling, and policy orchestration.
- The frontend talks to the backend, not directly to the OS. Its job is to present state, controls, and explanations to the operator.

In short: the OS provides capability, the backend coordinates it, and the frontend exposes it.

## OS Layer: Responsibilities

The OS layer is responsible for:

- Booting and running on the target firewall hardware
- Providing the base Linux environment and packaging model
- Exposing network interfaces and low-level packet access
- Hosting firewall enforcement primitives such as nftables
- Supporting packet capture mechanisms such as libpcap and eBPF
- Providing the local runtime environment the NeurWall services depend on

## OS Layer: Non-Responsibilities

The OS layer is not responsible for:

- Defining product behavior at the API or UI level
- Implementing the dashboard or operator workflows
- Serving as the source of truth for high-level policy decisions
- Performing model reasoning by itself
- Replacing the backend control plane

Those concerns belong to the backend and Brain layers that run on top of the OS foundation.

## Design Boundary

Contributors should treat NeurWall as three cooperating parts:

- A specialized local OS for firewall execution
- An application backend for control and orchestration
- A frontend for visibility and operation

That separation matters. NeurWall is not just a web app around Linux firewall tools, and it is not just a Linux image with some scripts. The architecture only works if those layers stay distinct and each one owns a clear responsibility.
