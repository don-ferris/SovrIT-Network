# TikiShack-SovrIT Network Configuration

> Project: SovrIT PTI (Personal/Private Technology Infrastructure)
>
> This document is the authoritative technical specification for the TikiShack/SovrIT network. It documents the physical infrastructure, logical network topology, infrastructure nodes, wireless configuration, and operational design decisions.
>
> This document is intended to serve as the primary source of truth for rebuilding, maintaining, troubleshooting, securing, and extending the network.

---

# Table of Contents

- [Network Management Devices](#network-management-devices)
  - [Router / Gateway](#router--gateway)
  - [Router 2 (5G Backup WAN)](#router-2-5g-backup-wan)
  - [Switch 1](#switch-1)
  - [Switch 2](#switch-2)
  - [Switch 3](#switch-3)
  - [Omada SDN Controller](#omada-sdn-controller)
  - [Wireless Access Point 1](#wireless-access-point-1)
  - [Wireless Access Point 2](#wireless-access-point-2)
- [Infrastructure Nodes](#infrastructure-nodes)
  - [Storage Node (BoraBora)](#storage-node-borabora)
  - [Core Node (Fiji)](#core-node-fiji)
  - [Compute Node (KonTiki)](#compute-node-kontiki)
  - [Orchestration Node (HomeAssistant)](#orchestration-node-homeassistant)
  - [Development Node (Moorea)](#development-node-moorea)
  - [Administration Node (Tahiti)](#administration-node-tahiti)
  - [Remote Failover Node](#remote-failover-node)
- [Network Configuration](#network-configuration)
  - [Switch Port Configuration](#switch-port-configuration)
  - [Wireless Networks](#wireless-networks)
  - [VLAN Device Inventory](#vlan-device-inventory)
- [Network Design Decisions](#network-design-decisions)
- [Network Design Status](#network-design-status)
- [Guiding Principles](#guiding-principles)
- [Revision History](#revision-history)

---

# Network Management Devices   [↩ TOC](#table-of-contents)

> Management VLAN: VLAN 1 (MGMT)

All network infrastructure devices reside on the Management VLAN unless otherwise noted.

---

## Router / Gateway   [↩ TOC](#table-of-contents)

**Make:** TP-Link  
**Model:** [ER707-M2](https://www.tp-link.com/us/business-networking/omada-router/er707-m2/)  
**Revision:** v1.3  
**Firmware Version:** `1.4.2 Build 20260509 Rel.32107`

### Description

Primary gateway, firewall, DHCP server, VLAN router and VPN endpoint.

### Network

| Property | Value |
|---|---|
| VLAN | [VLAN 1 — MGMT](#vlan-1-mgmt) |
| IP | `10.1.1.1` |
| IP Assignment | Static |
| MAC ID | `AC-A7-F1-D6-57-A6` |
| Connection | 802.3 (Ethernet) |
| Cable Color | White (Flat) |
| SSID | N/A |

### Port Map

| Port | Connection |
|---|---|
| Port 1 (WAN) | ISP modem/router |
| Port 2 (WAN/LAN) | UNUSED |
| Port 3 (WAN/LAN) | UNUSED |
| Port 4 (WAN/LAN) | [Moorea](#development-node-moorea) (Development/Test Node) |
| Port 5 (WAN/LAN) | [Tahiti](#administration-node-tahiti) (Administration Laptop) |
| Port 6 (WAN/LAN) | [Switch 1](#switch-1) |
| Port 7 (WAN/LAN) | UNUSED |

---

## Router 2 (5G Backup WAN)   [↩ TOC](#table-of-contents)

**Make:** Cradlepoint  
**Model:** W1850-5GB (S5A032A W-Series 5G Wideband Adapter Router)  
**Link:** [Cradlepoint W1850 Series](https://cradlepoint.ericsson.com/products/endpoints/w1850-series/)  
**Revision:** v1.3  
**Firmware Version:** `1.4.2 Build 20260509 Rel.32107`

### Description

Primary gateway, firewall, DHCP server, VLAN router and VPN endpoint.

### Network

| Property | Value |
|---|---|
| VLAN | [VLAN 1 — MGMT](#vlan-1-mgmt) |
| IP | `10.1.1.1` |
| IP Assignment | Static |
| MAC ID | `AC-A7-F1-D6-57-A6` |
| Connection | 802.3 (Ethernet) |
| Cable Color | White (Flat) |
| SSID | N/A |

### Port Map

| Port | Connection |
|---|---|
| Port 1 (LAN1) | UNUSED |
| Port 2 (LAN2) | UNUSED |
| Port 3 (WAN/LAN) | UNUSED |
| Port 4 (WAN/LAN) | [Moorea](#development-node-moorea) (Development/Test Node) |
| Port 5 (WAN/LAN) | [Tahiti](#administration-node-tahiti) (Administration Laptop) |
| Port 6 (WAN/LAN) | [Switch 1](#switch-1) |
| Port 7 (WAN/LAN) | UNUSED |

---

## Switch 1   [↩ TOC](#table-of-contents)

**Make:** TP-Link  
**Model:** [T1500G-10PS (TL-SG2210P)](https://www.tp-link.com/us/business-networking/omada-switch-smart-switch/t1500g-10ps/)  
**Revision:** v2.8  
**Firmware Version:** `2.0.6 Build 20200805 Rel.57865`

### Description

Primary managed PoE switch providing wired connectivity and power for the OC200 controller and EAP720 access point.

### Network

| Property | Value |
|---|---|
| VLAN | [VLAN 1 — MGMT](#vlan-1-mgmt) |
| IP | `10.1.1.2` |
| IP Assignment | Static |
| MAC ID | `68-FF-7B-F9-94-2D` |
| Connection | 802.3 (Ethernet) |
| SSID | N/A |

### Port Map

| Port | Connection |
|---|---|
| Port 1 | [ER707-M2](#router--gateway) Gateway |
| Port 2 | [OC200](#omada-sdn-controller) Controller (PoE) |
| Port 3 | [BoraBora](#storage-node-borabora) |
| Port 4 | Reserved for [KonTiki](#compute-node-kontiki) |
| Port 5 | [Fiji](#core-node-fiji) |
| Port 6 | Home Assistant Green |
| Port 7 | EAP720-1 (PoE) |
| Port 8 | Reserved for EAP720-2 (PoE) |

---

## Switch 2   [↩ TOC](#table-of-contents)

**Make:** TP-Link  
**Model:** [TL-SG1024DE](https://www.tp-link.com/us/business-networking/easy-smart-switch/tl-sg1024de/)

### Description

Reserved for future deployment.

### Network

| Property | Value |
|---|---|
| VLAN | [VLAN 1 — MGMT](#vlan-1-mgmt) |
| Connection | None |
| SSID | N/A |

---

## Switch 3   [↩ TOC](#table-of-contents)

**Make:** Reolink  
**Model:** [RLA-PS1](https://reolink.com/product/rla-ps1/)

### Description

Reserved for future deployment.

Intended to provide dedicated PoE connectivity for security cameras and related surveillance equipment.

### Network

| Property | Value |
|---|---|
| Connection | None |
| SSID | N/A |

---

## Omada SDN Controller   [↩ TOC](#table-of-contents)

**Make:** TP-Link  
**Model:** [OC200](https://www.tp-link.com/us/business-networking/omada-controller-hardware/oc200/)  
**Revision:** v1.0  
**Firmware Version:** `1.40.18 Build 20260506 Rel.74003`

### Description

Dedicated Omada SDN Controller replacing the previous Docker-based controller.

### Network

| Property | Value |
|---|---|
| VLAN | [VLAN 1 — MGMT](#vlan-1-mgmt) |
| IP | `10.1.1.5` |
| IP Assignment | Static |
| MAC ID | `B0-BE-76-C6-3A-FB` |
| Connection | 802.3 (Ethernet) |
| Cable Color | Blue |
| Device Port | [Switch 1](#switch-1) / Port 2 |
| SSID | N/A |

---

## Wireless Access Point 1   [↩ TOC](#table-of-contents)

**Make:** TP-Link  
**Model:** EAP720 (US)  
**Revision:** v1.0  
**Firmware Version:** `1.1.4 Build 20251224 Rel.62114`

### Description

Primary wireless access point.

### Network

| Property | Value |
|---|---|
| VLAN | [VLAN 1 — MGMT](#vlan-1-mgmt) |
| IP | `10.1.1.8` |
| IP Assignment | Static |
| MAC ID | `10-5A-95-2F-66-B1` |
| Connection | 802.3 (Ethernet) |
| Cable Color | Yellow |
| Device Port | [Switch 1](#switch-1) / Port 7 |
| SSID | N/A |

---

## Wireless Access Point 2   [↩ TOC](#table-of-contents)

**Make:** TP-Link  
**Model:** EAP720 (US)

### Description

Reserved for future deployment.

### Network

| Property | Value |
|---|---|
| Connection | None |
| SSID | N/A |

---

# Infrastructure Nodes   [↩ TOC](#table-of-contents)

> Infrastructure VLAN: VLAN 2 (INFRA)

All infrastructure systems are connected via wired Ethernet and operate as access-port devices on VLAN 2 unless explicitly documented otherwise.

---

## Storage Node (BoraBora)   [↩ TOC](#table-of-contents)

### Machine Role

**Hostname:** [BoraBora](#storage-node-borabora)

- Primary NAS
- Media Storage
- Backup Repository
- Infrastructure Storage
- Shared Storage
- Migration Support

### Hardware

| Property | Value |
|---|---|
| Manufacturer | Intel |
| Model | [NUC7i5BNH](https://www.intel.com/content/www/us/en/products/sku/95066/intel-nuc-kit-nuc7i5bnh/specifications.html) |
| CPU | Intel Core i5-7260U |
| Memory | 32 GB |
| Boot Device | SSD |
| Data Storage | Multi-drive storage pool |

### Operating System

| Property | Value |
|---|---|
| Name | TrueNAS SCALE |
| Version | 25.10 |

### Networking

| Property | Value |
|---|---|
| VLAN | [VLAN 2 — INFRA](#vlan-2-infra) |
| IP | `10.1.2.2` |
| IP Assignment | DHCP Reservation |
| Connection | 802.3 (Ethernet) |
| Device Port | [Switch 1](#switch-1) / Port 3 |

### Services

- SMB
- NFS
- Kopia Repository
- Docker
- Media Storage

### Notes

- Primary storage platform for the PTI.
- Intended to remain an Infrastructure host.
- Does not trunk multiple VLANs.
- Access from other VLANs is controlled exclusively through routing and ACLs.

---

## Core Node (Fiji)   [↩ TOC](#table-of-contents)

### Machine Role

**Hostname:** [Fiji](#core-node-fiji)

- Primary Infrastructure Server
- Container Host
- Infrastructure Services
- Future Core Platform

### Hardware

| Property | Value |
|---|---|
| Manufacturer | HP |
| Model | [t655](https://support.hp.com/us-en/product/details/hp-t655-thin-client/2101518704) |
| CPU | AMD Ryzen Embedded |
| Memory | 32 GB |

### Operating System

| Property | Value |
|---|---|
| Name | Ubuntu Server |
| Version | 22.04.5 LTS |

### Networking

| Property | Value |
|---|---|
| VLAN | [VLAN 2 — INFRA](#vlan-2-infra) |
| IP | `10.1.2.3` |
| IP Assignment | DHCP Reservation |
| Connection | 802.3 (Ethernet) |
| Device Port | [Switch 1](#switch-1) / Port 5 |

### Planned Services

- AdGuard Home
- Unbound
- CrowdSec
- Step-CA
- NetBird
- Docker
- Reverse Proxy
- Infrastructure Services

### Notes

- Currently operates as an Infrastructure access-port host.
- Single Ethernet interface.
- No tagged VLANs.
- Additional services will be exposed to other VLANs through router ACLs.

---

## Compute Node (KonTiki)   [↩ TOC](#table-of-contents)

### Machine Role

**Hostname:** [KonTiki](#compute-node-kontiki)

- Local LLM Server
- AI Inference
- GPU Compute
- Experimental AI Platform

### Hardware

| Property | Value |
|---|---|
| Manufacturer | Apple |
| Model | MacBook Pro M1 Max (A2485) |
| CPU | Apple M1 Max (8 performance cores, 2 efficiency cores) |
| Memory | 32 GB |
| Serial Number (system) | `Y45W77NJJF` |
| Hardware UUID | `BBC391E5-5D31-5EA4-BC39-9BC64DA2F7B9` |
| Provisioning UDID | `00006001-001A31913C04401E` |

### Operating System

| Property | Value |
|---|---|
| Name | macOS Sequoia (15.1.1 24B91) |
| System Firmware Version | `11881.41.5` |
| OS Loader Version | `11881.41.5` |

### Networking

| Property | Value |
|---|---|
| VLAN | [VLAN 2 — INFRA](#vlan-2-infra) |
| IP | `10.1.2.4` |
| IP Assignment | Static IP |
| Connection | Wired Ethernet |
| Device Port | [Switch 1](#switch-1) / Port 4 |

### Planned Services

- Ollama/LibreChat
- Hermes
- Local LLMs
- AI APIs
- SovrIT Assistant backend

### Notes

- Permanent infrastructure appliance.
- Used exclusively as a headless server in clamshell mode.
- NOT a personal workstation/not used as a laptop.
- Intended to remain permanently installed.
- Connected to AC power continuously.
- Connected to KVM.
- Expected to operate as an Infrastructure access-port host.
- Multiple VLAN interfaces are not presently anticipated.
- Client access from other VLANs will be governed through ACLs.

---

## Orchestration Node (HomeAssistant)   [↩ TOC](#table-of-contents)

### Machine Role

**Hostname:** [HomeAssistant](#orchestration-node-homeassistant)

- Home Automation
- Device Orchestration
- Automation Engine

### Hardware

| Property | Value |
|---|---|
| Manufacturer | Home Assistant |
| Model | [Home Assistant Green](https://www.home-assistant.io/green/) |

### Operating System

| Property | Value |
|---|---|
| Name | Home Assistant OS |

### Networking

| Property | Value |
|---|---|
| Current VLAN | [VLAN 2 — INFRA](#vlan-2-infra) |
| Current IP | DHCP Reservation |
| Connection | Wired Ethernet |
| Device Port | [Switch 1](#switch-1) / Port 6 |

### Planned Expansion

| Interface | VLAN | Purpose |
|---|---|---|
| Secondary USB Ethernet | [VLAN 66 — IoT](#vlan-66-iot) | Native communication with IoT devices |

### Notes

- Initially deployed solely on the Infrastructure VLAN.
- Planned dual-homed architecture:
  - Primary interface: Infrastructure management.
  - Secondary interface: Native IoT communication.
- Intended to become the application-layer coordinator between Infrastructure and IoT.

---

## Development Node (Moorea)   [↩ TOC](#table-of-contents)

### Machine Role

**Hostname:** [Moorea](#development-node-moorea)

- Development
- Testing
- Temporary Infrastructure

### Hardware

| Property | Value |
|---|---|
| Manufacturer | Dell Wyse |
| Model | 5070 |

### Status

| Property | Value |
|---|---|
| Current State | Offline/DEAD |
| Reason | Hardware troubleshooting |

### Notes

- Removed from service.
- Formerly connected directly to the router.

---

## Administration Node (Tahiti)   [↩ TOC](#table-of-contents)

### Machine Role

**Hostname:** [Tahiti](#administration-node-tahiti)

- Primary Administration Workstation

### Hardware

| Property | Value |
|---|---|
| Manufacturer | Lenovo |

### Operating System

| Property | Value |
|---|---|
| Name | Linux Mint |
| Version | 22 |

### Networking

#### Wired

| Property | Value |
|---|---|
| VLAN | [VLAN 1 — MGMT](#vlan-1-mgmt) |
| IP | `10.1.1.88` |
| Usage | Administrative tasks |

#### Wireless

| Property | Value |
|---|---|
| Primary SSID | [TikiShack](#wireless-networks) |
| VLAN | [VLAN 22 — TikiShack](#vlan-22-tikishack) |
| Usage | Normal administration |

### Notes

- Primary management workstation.
- Most day-to-day network administration occurs wirelessly from VLAN 22.
- Wired management remains available when required.
- iPad (Donnie's iPad Pro 12.9" 5th Gen) serves as a second admin node.

---

## Remote Failover Node   [↩ TOC](#table-of-contents)

### Status

Planned

### Purpose

- Disaster Recovery
- Off-site Replication
- Remote Infrastructure

### Notes

- Will eventually become an independent PTI node.
- Detailed architecture documented separately.

---

# Network Configuration   [↩ TOC](#table-of-contents)

This section represents the authoritative operational configuration of the production network.

If the Omada Controller configuration and this document disagree, this document is considered the intended configuration and should be used when validating or rebuilding the network.

---

## Switch Port Configuration   [↩ TOC](#table-of-contents)

### Port 1

| Property | Value |
|---|---|
| Name | Gateway Trunk |
| Connected Device | [ER707-M2](#router--gateway) Gateway |
| Port Type | Trunk |
| Native VLAN | MGMT (1) |

**Tagged VLANs:**

- INFRA (2)
- TikiShack.Printer (3)
- TikiShack (22)
- TikiShack.Panama (23)
- TikiShack.Guest (33)
- TikiShack.Sentry (44)
- TikiShack.Media (55)
- TikiShack.IoT (66)
- Dev-Sandbox (99)

**Notes:**

- Primary gateway uplink.
- Carries every production VLAN.

### Port 2

| Property | Value |
|---|---|
| Name | OC200 Controller |
| Connected Device | Omada OC200 |
| Port Type | Access |
| Native VLAN | MGMT (1) |
| Tagged VLANs | None |

**Notes:**

- Dedicated management interface.
- No tagged VLANs.

### Port 3

| Property | Value |
|---|---|
| Name | [BoraBora](#storage-node-borabora) |
| Connected Device | Storage Node |
| Port Type | Access |
| Native VLAN | INFRA (2) |
| Tagged VLANs | None |

**Notes:**

- Storage server.
- Inter-VLAN access is controlled through ACLs.

### Port 4

| Property | Value |
|---|---|
| Name | [KonTiki](#compute-node-kontiki) |
| Connected Device | Local AI Compute Node |
| Port Type | Access |
| Native VLAN | INFRA (2) |
| Tagged VLANs | None |

**Notes:**

- Reserved pending USB Ethernet adapter.
- Intended to remain an Infrastructure host.

### Port 5

| Property | Value |
|---|---|
| Name | [Fiji](#core-node-fiji) |
| Connected Device | Core Infrastructure Node |
| Port Type | Access |
| Native VLAN | INFRA (2) |
| Tagged VLANs | None |

**Notes:**

- Ubuntu Server.
- Single network interface.

### Port 6

| Property | Value |
|---|---|
| Name | Home Assistant Green |
| Connected Device | [Home Assistant](#orchestration-node-homeassistant) |
| Port Type | Access |
| Native VLAN | INFRA (2) |
| Tagged VLANs | None |

**Notes:**

- Future secondary USB NIC will connect to VLAN 66.
- Switch port remains an access port.

### Port 7

| Property | Value |
|---|---|
| Name | AP1 |
| Connected Device | TP-Link EAP720 |
| Port Type | Trunk |
| Native VLAN | MGMT (1) |

**Tagged VLANs:**

- TikiShack.Printer (3)
- TikiShack (22)
- TikiShack.Panama (23)
- TikiShack.Guest (33)
- TikiShack.Sentry (44)
- TikiShack.Media (55)
- TikiShack.IoT (66)
- Dev-Sandbox (99)

**Notes:**

- Carries only VLANs that have wireless SSIDs.
- Does NOT carry INFRA (2).

### Port 8

| Property | Value |
|---|---|
| Name | AP2 (Reserved) |
| Connected Device | Future EAP720 |
| Port Type | Trunk |
| Native VLAN | MGMT (1) |

**Tagged VLANs:**

- TikiShack.Printer (3)
- TikiShack (22)
- TikiShack.Panama (23)
- TikiShack.Guest (33)
- TikiShack.Sentry (44)
- TikiShack.Media (55)
- TikiShack.IoT (66)
- Dev-Sandbox (99)

**Notes:**

- Reserved for future deployment.
- Mirrors Port 7 configuration.

## Wireless Networks   [↩ TOC](#table-of-contents)

Wireless networks are mapped directly to VLANs.

The SSID determines the VLAN, and the VLAN determines routing, firewall, and WAN policy.

---

### TikiShack   [↩ TOC](#table-of-contents)

**SSID:** `TikiShack`  
**VLAN:** [VLAN 22 — TikiShack](#vlan-22-tikishack)

**Purpose:**

Primary trusted wireless network.

**WAN Routing:**

Boston VPN.

**Notes:**

- Primary wireless network for trusted household devices.
- Traffic is routed through the Boston VPN.
- Infrastructure access is controlled by inter-VLAN ACLs.

---

### TikiShack.Panama   [↩ TOC](#table-of-contents)

**SSID:** `TikiShack.Panama`  
**VLAN:** [VLAN 23 — Panama](#vlan-23-panama)

**Purpose:**

Trusted wireless network for devices that require direct Panama Internet access.

**WAN Routing:**

Direct WAN.

**Notes:**

- Bypasses the Boston VPN.
- Provides a separate egress path without requiring per-device VPN configuration.

---

### TikiShack.Guest   [↩ TOC](#table-of-contents)

**SSID:** `TikiShack.Guest`  
**VLAN:** [VLAN 33 — Guest](#vlan-33-guest)

**Purpose:**

Guest wireless access.

**WAN Routing:**

Direct WAN.

**Security:**

- No access to Infrastructure VLAN.
- No access to Management VLAN.
- No access to trusted client networks.
- Internet access only unless explicitly permitted otherwise.

---

### TikiShack.Sentry   [↩ TOC](#table-of-contents)

**SSID:** `TikiShack.Sentry`  
**VLAN:** [VLAN 44 — Sentry](#vlan-44-sentry)

**Purpose:**

Security and surveillance devices.

**Notes:**

- Intended for security cameras and related devices.
- Devices should have minimal access to other network segments.
- Management and recording services should be explicitly permitted.

---

### TikiShack.Media   [↩ TOC](#table-of-contents)

**SSID:** `TikiShack.Media`  
**VLAN:** [VLAN 55 — Media](#vlan-55-media)

**Purpose:**

Media devices and services.

**Notes:**

- Intended for televisions, streaming devices, and related media equipment.
- Access to [BoraBora](#storage-node-borabora) should be explicitly controlled.

---

### TikiShack.IoT   [↩ TOC](#table-of-contents)

**SSID:** `TikiShack.IoT`  
**VLAN:** [VLAN 66 — IoT](#vlan-66-iot)

**Purpose:**

Internet-of-Things devices.

**Notes:**

- IoT devices are treated as untrusted or semi-trusted.
- Direct access to Infrastructure and Management networks is prohibited unless explicitly required.
- [Home Assistant](#orchestration-node-homeassistant) is intended to provide controlled access to IoT devices.

---

### Dev-Sandbox   [↩ TOC](#table-of-contents)

**SSID:** `Dev-Sandbox`  
**VLAN:** [VLAN 99 — Dev-Sandbox](#vlan-99-dev-sandbox)

**Purpose:**

Development and experimentation.

**Notes:**

- Intended for temporary development systems.
- Devices on this VLAN should not be assumed to be trusted.
- Production infrastructure access requires explicit ACLs.

---

# VLAN Device Inventory   [↩ TOC](#table-of-contents)

This section provides the current device-to-VLAN assignments.

---

## VLAN 1 — MGMT   [↩ TOC](#table-of-contents)

**Purpose:** Network management.

| Device | IP | Connection |
|---|---|---|
| [ER707-M2](#router--gateway) | `10.1.1.1` | Gateway |
| [Switch 1](#switch-1) | `10.1.1.2` | Management |
| [OC200](#omada-sdn-controller) | `10.1.1.5` | Management |
| EAP720-1 | `10.1.1.8` | Management |
| [Tahiti](#administration-node-tahiti) | `10.1.1.88` | Wired administration |

---

## VLAN 2 — INFRA   [↩ TOC](#table-of-contents)

**Purpose:** Infrastructure services and servers.

| Device | IP | Connection |
|---|---|---|
| [BoraBora](#storage-node-borabora) | `10.1.2.2` | Switch 1 / Port 3 |
| [Fiji](#core-node-fiji) | `10.1.2.3` | Switch 1 / Port 5 |
| [KonTiki](#compute-node-kontiki) | `10.1.2.4` | Switch 1 / Port 4 |
| [Home Assistant](#orchestration-node-homeassistant) | DHCP Reservation | Switch 1 / Port 6 |

---

## VLAN 3 — Printer   [↩ TOC](#table-of-contents)

**Purpose:** Printer devices.

No permanently assigned devices are currently documented.

---

## VLAN 22 — TikiShack   [↩ TOC](#table-of-contents)

**Purpose:** Primary trusted wireless network.

**SSID:** [`TikiShack`](#wireless-networks)

**WAN:** Boston VPN.

---

## VLAN 23 — Panama   [↩ TOC](#table-of-contents)

**Purpose:** Direct-WAN wireless network.

**SSID:** [`TikiShack.Panama`](#wireless-networks)

**WAN:** Direct WAN.

---

## VLAN 33 — Guest   [↩ TOC](#table-of-contents)

**Purpose:** Guest wireless network.

**SSID:** [`TikiShack.Guest`](#wireless-networks)

**WAN:** Direct WAN.

---

## VLAN 44 — Sentry   [↩ TOC](#table-of-contents)

**Purpose:** Security and observation devices.

**SSID:** [`TikiShack.Sentry`](#wireless-networks)

---

## VLAN 55 — Media   [↩ TOC](#table-of-contents)

**Purpose:** Media devices.

**SSID:** [`TikiShack.Media`](#wireless-networks)

---

## VLAN 66 — IoT   [↩ TOC](#table-of-contents)

**Purpose:** IoT devices.

**SSID:** [`TikiShack.IoT`](#wireless-networks)

---

## VLAN 99 — Dev-Sandbox   [↩ TOC](#table-of-contents)

**Purpose:** Development and experimentation.

**SSID:** [`Dev-Sandbox`](#wireless-networks)

---

# Network Design Decisions   [↩ TOC](#table-of-contents)

This section documents the reasoning behind the network architecture.

---

## VLAN Segmentation   [↩ TOC](#table-of-contents)

The network is intentionally divided into security and functional domains.

The primary VLANs are:

| VLAN | Name | Function |
|---:|---|---|
| 1 | MGMT | Network management |
| 2 | INFRA | Servers and infrastructure |
| 3 | Printer | Printers |
| 22 | TikiShack | Trusted clients |
| 23 | Panama | Direct-WAN trusted clients |
| 33 | Guest | Guest devices |
| 44 | Sentry | Security devices |
| 55 | Media | Media devices |
| 66 | IoT | IoT devices |
| 99 | Dev-Sandbox | Development |

The objective is to prevent a compromise or misconfiguration in one class of device from automatically providing access to unrelated systems.

---

## Infrastructure VLAN   [↩ TOC](#table-of-contents)

Infrastructure systems are concentrated on VLAN 2.

Current infrastructure systems include:

- [BoraBora](#storage-node-borabora)
- [Fiji](#core-node-fiji)
- [KonTiki](#compute-node-kontiki)
- [Home Assistant](#orchestration-node-homeassistant)

The Infrastructure VLAN is not intended to be a general-purpose client network.

---

## Management VLAN   [↩ TOC](#table-of-contents)

VLAN 1 is reserved for network management.

Network equipment should use VLAN 1 for management interfaces where supported.

Management access should be restricted to trusted administrative paths.

---

## Wired Infrastructure   [↩ TOC](#table-of-contents)

Core infrastructure is wired wherever practical.

This includes:

- storage;
- compute;
- server systems;
- network management;
- access points;
- Home Assistant;
- future infrastructure nodes.

Wireless is primarily an access mechanism for client devices.

---

## Wireless Segmentation   [↩ TOC](#table-of-contents)

Wireless SSIDs map directly to VLANs.

This allows routing and security policy to be determined by the network to which a device connects.

Examples:

- `TikiShack` → VLAN 22 → Boston VPN
- `TikiShack.Panama` → VLAN 23 → Direct WAN
- `TikiShack.Guest` → VLAN 33 → Guest
- `TikiShack.Sentry` → VLAN 44 → Sentry
- `TikiShack.Media` → VLAN 55 → Media
- `TikiShack.IoT` → VLAN 66 → IoT
- `Dev-Sandbox` → VLAN 99 → Development

---

## VPN Routing   [↩ TOC](#table-of-contents)

VPN routing is implemented at the network level wherever practical.

The Boston VPN is associated with VLAN 22 rather than being individually configured on every client.

This permits clients to use the VPN simply by joining the appropriate SSID.

A separate direct-WAN VLAN provides an intentional exception for systems that require local Internet egress.

---

## IoT Isolation   [↩ TOC](#table-of-contents)

IoT devices are isolated on VLAN 66.

IoT devices should not be given unrestricted access to infrastructure or management systems.

[Home Assistant](#orchestration-node-homeassistant) is intended to act as the controlled application-layer interface between the trusted infrastructure network and IoT devices.

The planned dual-interface Home Assistant configuration is intended to provide:

- Infrastructure connectivity through VLAN 2;
- IoT connectivity through VLAN 66.

---

## Guest Isolation   [↩ TOC](#table-of-contents)

Guest devices are placed on VLAN 33.

Guest access should provide Internet connectivity without exposing:

- Management;
- Infrastructure;
- trusted client networks;
- storage;
- development systems;
- security infrastructure.

---

## Security / Sentry Isolation   [↩ TOC](#table-of-contents)

Security cameras and related devices are placed on VLAN 44.

These devices should be treated as potentially compromised endpoints.

Access should therefore be designed around the services they require rather than granting them general access to the network.

Recording and management systems may initiate connections to Sentry devices.

Sentry devices should not generally initiate connections into the Infrastructure or Management VLANs.

---

## Media Isolation   [↩ TOC](#table-of-contents)

Media devices are placed on VLAN 55.

Media devices frequently require access to storage, streaming services, discovery protocols, and the Internet, but should not automatically have access to administrative systems.

Where media devices require access to [BoraBora](#storage-node-borabora), that access should be explicitly permitted.

---

## Development Isolation   [↩ TOC](#table-of-contents)

Development and experimental systems are placed on VLAN 99.

The purpose is to provide a place for:

- software development;
- testing;
- temporary services;
- experimental systems;
- systems that should not be treated as production infrastructure.

Development access to production infrastructure should be explicit.

---

## Service Discovery   [↩ TOC](#table-of-contents)

Cross-VLAN service discovery should be controlled rather than allowing unrestricted broadcast or multicast traffic.

Where a service requires discovery across VLAN boundaries, the appropriate mechanism should be deliberately configured.

The objective is to preserve segmentation without making normal service discovery unnecessarily difficult.

---

## Firewall / ACL Philosophy   [↩ TOC](#table-of-contents)

The router is the Layer-3 enforcement point.

Inter-VLAN access should follow least privilege.

The default policy should be restrictive, with explicit exceptions for required services.

Rules should be documented in terms of:

- source VLAN/device;
- destination VLAN/device;
- protocol;
- destination port;
- purpose.

---

## IPv6   [↩ TOC](#table-of-contents)

IPv6 is intentionally deferred.

The current design is focused on establishing a stable IPv4/VLAN architecture before introducing IPv6.

When IPv6 is eventually enabled, it must receive equivalent firewall and segmentation treatment rather than becoming an unintended bypass around the IPv4 architecture.

---

## Remote Access   [↩ TOC](#table-of-contents)

Remote administration should use secure authenticated access rather than exposing management interfaces directly to the Internet.

The preferred long-term architecture uses encrypted overlay networking and strong identity controls.

---

## Infrastructure Services   [↩ TOC](#table-of-contents)

Infrastructure services will be concentrated on the Infrastructure VLAN where practical.

Planned services include:

- DNS;
- DHCP;
- internal PKI;
- authentication;
- monitoring;
- backup;
- notification;
- reverse proxy;
- VPN/overlay networking;
- containerized services;
- AI services.

These services should be reachable from other VLANs only where required.

---

## Storage Architecture   [↩ TOC](#table-of-contents)

[BoraBora](#storage-node-borabora) is the primary storage platform.

Storage is considered infrastructure and is therefore separated from general-purpose client networks.

The storage platform is expected to provide:

- NAS services;
- media storage;
- backups;
- application storage;
- shared storage.

Access to storage should be explicitly controlled by VLAN and service requirements.

---

## AI Infrastructure   [↩ TOC](#table-of-contents)

[KonTiki](#compute-node-kontiki) provides local AI compute.

It is treated as infrastructure rather than as a normal workstation.

The AI environment may require access to:

- storage;
- source repositories;
- application services;
- automation;
- network services.

Those requirements should be implemented through explicit ACLs rather than broad network access.

---

## Home Assistant Architecture   [↩ TOC](#table-of-contents)

[Home Assistant](#orchestration-node-homeassistant) currently resides on VLAN 2.

The planned secondary Ethernet interface will connect to VLAN 66.

The intended architecture is:

```text
                 ┌─────────────────────────┐
                 │      Home Assistant     │
                 │                         │
 VLAN 2 ─────────┤ Primary Ethernet        │
 Infrastructure  │                         │
                 │ Secondary USB Ethernet  ├──────── VLAN 66
                 │                         │          IoT
                 └─────────────────────────┘
```

This permits Home Assistant to communicate directly with IoT devices while maintaining its primary infrastructure presence.

---

# Network Design Status   [↩ TOC](#table-of-contents)

The network is operational, but several portions remain under active development.

---

## Operational   [↩ TOC](#table-of-contents)

Currently operational:

- ER707-M2 gateway;
- TP-Link Omada switching;
- OC200 controller;
- EAP720 wireless access point;
- VLAN 1 Management;
- VLAN 2 Infrastructure;
- VLAN 22 TikiShack;
- VLAN 23 Panama;
- VLAN 33 Guest;
- VLAN 44 Sentry;
- VLAN 55 Media;
- VLAN 66 IoT;
- VLAN 99 Dev-Sandbox;
- [BoraBora](#storage-node-borabora);
- [Fiji](#core-node-fiji);
- [KonTiki](#compute-node-kontiki);
- [Home Assistant](#orchestration-node-homeassistant);
- [Tahiti](#administration-node-tahiti).

---

## In Development   [↩ TOC](#table-of-contents)

Currently being developed:

- complete firewall/ACL policy;
- Home Assistant dual-interface configuration;
- additional security monitoring;
- remote failover;
- additional infrastructure services;
- expanded IoT integration;
- development sandbox policy.

---

## Deferred   [↩ TOC](#table-of-contents)

Currently deferred:

- IPv6;
- full gateway high availability;
- complete remote-site infrastructure;
- additional wireless access points where not yet required.

---

# Guiding Principles   [↩ TOC](#table-of-contents)

The TikiShack/SovrIT network follows these principles.

---

## Infrastructure First   [↩ TOC](#table-of-contents)

Build and stabilize the underlying infrastructure before adding dependent services.

---

## Least Privilege   [↩ TOC](#table-of-contents)

Devices and services should receive only the access they require.

---

## Explicit Over Implicit   [↩ TOC](#table-of-contents)

Network behavior should be explicitly configured and documented rather than relying on undocumented defaults.

---

## Segmentation   [↩ TOC](#table-of-contents)

Different trust domains should have distinct network boundaries.

---

## Wired First   [↩ TOC](#table-of-contents)

Core infrastructure should use wired Ethernet whenever practical.

Wireless should primarily serve client mobility and convenience.

---

## Observation Over Inference   [↩ TOC](#table-of-contents)

Network behavior should be observable.

Configuration alone should not be assumed to prove that the network is behaving as intended.

---

## Security Before Convenience   [↩ TOC](#table-of-contents)

Convenience should not silently weaken security boundaries.

Any deliberate tradeoff should be documented.

---

## Document the Why   [↩ TOC](#table-of-contents)

Configuration documentation should explain not only what exists, but why it exists.

This is particularly important for VLANs, ACLs, routing decisions, and unusual topology choices.

---

## Self-Describing Infrastructure   [↩ TOC](#table-of-contents)

Hostnames, port descriptions, VLAN names, SSIDs, and documentation should reinforce one another.

A future administrator should be able to trace:

```text
Device
  ↓
Switch Port
  ↓
VLAN
  ↓
Gateway
  ↓
Firewall / ACL
  ↓
Service
```

without reconstructing the design from unrelated sources.

---

# Revision History   [↩ TOC](#table-of-contents)

| Date | Revision | Description |
|---|---|---|
| 2026-09-28 | 1.0 | Initial authoritative TikiShack/SovrIT network configuration |
| 2026-09-28 | 1.1 | Updated infrastructure node assignments and network topology |
| 2026-09-28 | 1.2 | Added VLAN and wireless architecture |
| 2026-09-28 | 1.3 | Added future architecture and network design principles |
| 2026-09-29 | 2.0 | Converted YAML-based configuration to semantic Markdown while preserving original document hierarchy and section structure |


