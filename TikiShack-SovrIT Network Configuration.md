# TikiShack / SovrIT Network Configuration

**Document Status:** Authoritative Technical Specification  
**Last Updated:** 2026-09-28

---

## Table of Contents

[Network Entity Index](#network-entity-index)
[VLAN Index](#vlan-index)
[Network Management Devices](#network-management-devices)
   - [Router / Gateway — ER707-M2](#router--gateway--er707-m2)
   - [Router 2 — W1850-5GB](#router-2--w1850-5gb)
   - [Switch 1 — T1500G-10PS](#switch-1--t1500g-10ps)
   - [Switch 2 — TL-SG1024DE](#switch-2--tl-sg1024de)
   - [Switch 3 — Reolink RLA-PS1](#switch-3--reolink-rla-ps1)
   - [OC200](#oc200)
   - [EAP720-1](#eap720-1)
   - [EAP720-2](#eap720-2)
5. [Infrastructure Nodes](#infrastructure-nodes)
   - [BoraBora](#node-borabora)
   - [Fiji](#node-fiji)
   - [KonTiki](#node-kontiki)
   - [Home Assistant](#node-home-assistant)
   - [Moorea](#node-moorea)
   - [Tahiti](#node-tahiti)
   - [Remote Failover Node](#node-remote-failover)
6. [VLANs](#vlans)
   - [VLAN 1 — MGMT](#vlan-1--mgmt)
   - [VLAN 2 — INFRA](#vlan-2--infra)
   - [VLAN 3 — Printer](#vlan-3--printer)
   - [VLAN 22 — TikiShack](#vlan-22--tikishack)
   - [VLAN 23 — Panama](#vlan-23--panama)
   - [VLAN 33 — Guest](#vlan-33--guest)
   - [VLAN 44 — Sentry](#vlan-44--sentry)
   - [VLAN 55 — Media](#vlan-55--media)
   - [VLAN 66 — IoT](#vlan-66--iot)
   - [VLAN 99 — Dev-Sandbox](#vlan-99--dev-sandbox)
7. [Wireless Networks](#wireless-networks)
8. [Physical Connectivity](#physical-connectivity)
9. [Logical Network Architecture](#logical-network-architecture)
10. [Future Architecture](#future-architecture)
11. [Network Design Status](#network-design-status)
12. [Guiding Principles](#guiding-principles)
13. [Revision History](#revision-history)

---

## Network Entity Index

<a name="network-entity-index"></a>

The following index provides a compact map of the principal entities in the network.

### Network Management

| Entity | Type | Primary Address | VLAN | Physical Location |
|---|---|---:|---:|---|
| [`ER707-M2`](#router--gateway--er707-m2) | Router / Gateway | `10.1.1.1` | [`VLAN 1`](#vlan-1--mgmt) | TikiShack |
| [`W1850-5GB`](#router-2--w1850-5gb) | Cellular Router | `10.1.1.1`* | [`VLAN 1`](#vlan-1--mgmt) | TikiShack |
| [`T1500G-10PS`](#switch-1--t1500g-10ps) | Managed PoE Switch | `10.1.1.2` | [`VLAN 1`](#vlan-1--mgmt) | TikiShack |
| [`TL-SG1024DE`](#switch-2--tl-sg1024de) | Managed Switch | — | [`VLAN 1`](#vlan-1--mgmt) | TikiShack |
| [`RLA-PS1`](#switch-3--reolink-rla-ps1) | PoE Switch | — | [`VLAN 1`](#vlan-1--mgmt) | TikiShack |
| [`OC200`](#oc200) | Omada Controller | `10.1.1.5` | [`VLAN 1`](#vlan-1--mgmt) | TikiShack |
| [`EAP720-1`](#eap720-1) | Wireless AP | `10.1.1.8` | [`VLAN 1`](#vlan-1--mgmt) | TikiShack |
| [`EAP720-2`](#eap720-2) | Wireless AP | Reserved | [`VLAN 1`](#vlan-1--mgmt) | TikiShack |

\* The W1850-5GB entry requires verification; see its device section.

### Infrastructure Nodes

| Entity | Hardware | Operating System | Address | VLAN |
|---|---|---|---:|---:|
| [`BoraBora`](#node-borabora) | Intel NUC7i5BNH | TrueNAS SCALE | `10.1.2.2` | [`VLAN 2`](#vlan-2--infra) |
| [`Fiji`](#node-fiji) | HP t655 | Ubuntu Server | `10.1.2.3` | [`VLAN 2`](#vlan-2--infra) |
| [`KonTiki`](#node-kontiki) | MacBook Pro M1 Max | macOS | `10.1.2.4` | [`VLAN 2`](#vlan-2--infra) |
| [`Home Assistant`](#node-home-assistant) | Home Assistant Green | Home Assistant OS | — | [`VLAN 2`](#vlan-2--infra) |
| [`Moorea`](#node-moorea) | Dell Wyse 5070 | — | — | — |
| [`Tahiti`](#node-tahiti) | Lenovo laptop | Linux Mint | `10.1.1.88` wired | [`VLAN 1`](#vlan-1--mgmt) |
| [`Remote Failover Node`](#node-remote-failover) | Future | TBD | TBD | TBD |

### VLANs

| VLAN | Name | Purpose |
|---:|---|---|
| [`1`](#vlan-1--mgmt) | MGMT | Network management |
| [`2`](#vlan-2--infra) | INFRA | Servers and infrastructure |
| [`3`](#vlan-3--printer) | Printer | Printer devices |
| [`22`](#vlan-22--tikishack) | TikiShack | Primary trusted wireless network |
| [`23`](#vlan-23--panama) | Panama | Direct-WAN wireless network |
| [`33`](#vlan-33--guest) | Guest | Guest wireless |
| [`44`](#vlan-44--sentry) | Sentry | Security / observation devices |
| [`55`](#vlan-55--media) | Media | Media devices |
| [`66`](#vlan-66--iot) | IoT | IoT devices |
| [`99`](#vlan-99--dev-sandbox) | Dev-Sandbox | Development / experimentation |

---

## VLAN Index

<a name="vlan-index"></a>

| VLAN | Name | Function | Primary Security Role |
|---:|---|---|---|
| [`1`](#vlan-1--mgmt) | MGMT | Network management | Administrative |
| [`2`](#vlan-2--infra) | INFRA | Core infrastructure | Trusted infrastructure |
| [`3`](#vlan-3--printer) | Printer | Printers | Restricted peripheral |
| [`22`](#vlan-22--tikishack) | TikiShack | Trusted clients | Primary user network |
| [`23`](#vlan-23--panama) | Panama | Direct-WAN clients | Alternate client path |
| [`33`](#vlan-33--guest) | Guest | Guest access | Untrusted |
| [`44`](#vlan-44--sentry) | Sentry | Security devices | Restricted observation |
| [`55`](#vlan-55--media) | Media | Media infrastructure | Restricted service network |
| [`66`](#vlan-66--iot) | IoT | IoT devices | Restricted devices |
| [`99`](#vlan-99--dev-sandbox) | Dev-Sandbox | Development | Isolated experimentation |

---

## Network Management Devices      [↩ TOC](#table-of-contents)

<a name="network-management-devices"></a>

Network management devices provide routing, switching, wireless management, and network control.

---

## Router / Gateway — ER707-M2

<a name="router--gateway--er707-m2"></a>

[↩ TOC](#table-of-contents)

**Manufacturer:** TP-Link  
**Model:** [ER707-M2](https://www.tp-link.com/us/business-networking/omada-router/er707-m2/)  
**Role:** Primary router / gateway  
**Management VLAN:** [`VLAN 1 — MGMT`](#vlan-1--mgmt)

### Identity

| Property | Value |
|---|---|
| Hostname | `ER707-M2` |
| IP Address | `10.1.1.1` |
| MAC Address | `AC-A7-F1-D6-57-A6` |
| Firmware | `1.4.2 Build 20260509 Rel.32107` |
| Platform | TP-Link Omada |

### Network Role

The ER707-M2 is the primary Layer-3 gateway for the TikiShack/SovrIT network.

It provides:

- inter-VLAN routing;
- firewall enforcement;
- DHCP services as configured;
- WAN connectivity;
- VLAN trunk termination;
- VPN functionality as configured;
- network-level policy enforcement.

### VLANs

The gateway carries the following VLANs:

- [`VLAN 1 — MGMT`](#vlan-1--mgmt)
- [`VLAN 2 — INFRA`](#vlan-2--infra)
- [`VLAN 3 — Printer`](#vlan-3--printer)
- [`VLAN 22 — TikiShack`](#vlan-22--tikishack)
- [`VLAN 23 — Panama`](#vlan-23--panama)
- [`VLAN 33 — Guest`](#vlan-33--guest)
- [`VLAN 44 — Sentry`](#vlan-44--sentry)
- [`VLAN 55 — Media`](#vlan-55--media)
- [`VLAN 66 — IoT`](#vlan-66--iot)
- [`VLAN 99 — Dev-Sandbox`](#vlan-99--dev-sandbox)

### Design Intent

The router is the enforcement point for the network's segmentation model.

The architecture intentionally places policy at the Layer-3 boundary rather than relying on implicit trust between devices simply because they share a physical switching fabric.

---

## Router 2 — W1850-5GB

<a name="router-2--w1850-5gb"></a>

[↩ TOC](#table-of-contents)

**Manufacturer:** Cradlepoint / Ericsson  
**Model:** [W1850-5GB](https://cradlepoint.ericsson.com/products/endpoints/w1850-series/)  
**Role:** Cellular / secondary WAN capability

### Identity

| Property | Value |
|---|---|
| Hostname | `W1850-5GB` |
| IP Address | `10.1.1.1`* |
| MAC Address | `AC-A7-F1-D6-57-A6`* |
| VLAN | [`VLAN 1 — MGMT`](#vlan-1--mgmt) |

### Verification Required

The currently documented IP address, MAC address, and port configuration duplicate the [`ER707-M2`](#router--gateway--er707-m2).

This is almost certainly an inventory/configuration inconsistency rather than an intentional duplicate network identity.

**Do not treat the values above as authoritative until the physical W1850-5GB installation is verified.**

The device is intended to provide a secondary/failover WAN path.

---

## Switch 1 — T1500G-10PS

<a name="switch-1--t1500g-10ps"></a>

[↩ TOC](#table-of-contents)

**Manufacturer:** TP-Link  
**Model:** [T1500G-10PS / TL-SG2210P](https://www.tp-link.com/us/business-networking/omada-switch-smart-switch/t1500g-10ps/)  
**Role:** Primary managed PoE switch

### Identity

| Property | Value |
|---|---|
| Hostname | `Switch 1` |
| Model | `T1500G-10PS / TL-SG2210P` |
| IP Address | `10.1.1.2` |
| MAC Address | `68-FF-7B-F9-94-2D` |
| Management VLAN | [`VLAN 1`](#vlan-1--mgmt) |

### Role

Switch 1 is the principal access switch for the network.

It provides:

- PoE;
- VLAN-aware Layer-2 switching;
- the primary connection to the gateway;
- connections to core infrastructure;
- connections to wireless access points.

### Port Map

| Port | Connected Device | Mode | Native / Untagged VLAN | Tagged VLANs |
|---:|---|---|---:|---|
| 1 | [`ER707-M2`](#router--gateway--er707-m2) | Trunk | `1` | `2,3,22,23,33,44,55,66,99` |
| 2 | [`OC200`](#oc200) | Access | `1` | — |
| 3 | [`BoraBora`](#node-borabora) | Access | `2` | — |
| 4 | [`KonTiki`](#node-kontiki) | Access | `2` | — |
| 5 | [`Fiji`](#node-fiji) | Access | `2` | — |
| 6 | [`Home Assistant`](#node-home-assistant) | Access | `2` | — |
| 7 | [`EAP720-1`](#eap720-1) | Trunk | `1` | Wireless VLANs |
| 8 | [`EAP720-2`](#eap720-2) | Trunk | `1` | Wireless VLANs |

---

## Switch 2 — TL-SG1024DE

<a name="switch-2--tl-sg1024de"></a>

[↩ TOC](#table-of-contents)

**Manufacturer:** TP-Link  
**Model:** [TL-SG1024DE](https://www.tp-link.com/us/business-networking/easy-smart-switch/tl-sg1024de/)  
**Role:** Secondary managed switch

### Identity

| Property | Value |
|---|---|
| Hostname | `Switch 2` |
| Model | `TL-SG1024DE` |
| Management VLAN | [`VLAN 1`](#vlan-1--mgmt) |

### Role

Switch 2 provides additional wired Ethernet capacity.

Its configuration should follow the same VLAN design principles as [`Switch 1`](#switch-1--t1500g-10ps):

- explicit access VLANs;
- explicit trunk VLANs;
- management on [`VLAN 1`](#vlan-1--mgmt);
- no implicit cross-VLAN trust.

---

## Switch 3 — Reolink RLA-PS1

<a name="switch-3--reolink-rla-ps1"></a>

[↩ TOC](#table-of-contents)

**Manufacturer:** Reolink  
**Model:** [RLA-PS1](https://reolink.com/product/rla-ps1/)  
**Role:** PoE switch for security infrastructure

### Identity

| Property | Value |
|---|---|
| Hostname | `Switch 3` |
| Model | `RLA-PS1` |
| Management VLAN | [`VLAN 1`](#vlan-1--mgmt) |

### Role

Switch 3 is intended primarily for Reolink/security infrastructure.

Security-oriented devices should ultimately reside on [`VLAN 44 — Sentry`](#vlan-44--sentry) where practical.

---

## OC200

<a name="oc200"></a>

[↩ TOC](#table-of-contents)

**Manufacturer:** TP-Link  
**Model:** [OC200 Omada Hardware Controller](https://www.tp-link.com/us/business-networking/omada-controller-hardware/oc200/)  
**Role:** Omada Controller

### Identity

| Property | Value |
|---|---|
| Hostname | `OC200` |
| IP Address | `10.1.1.5` |
| MAC Address | `B0-BE-76-C6-3A-FB` |
| VLAN | [`VLAN 1 — MGMT`](#vlan-1--mgmt) |
| Connected To | [`Switch 1`](#switch-1--t1500g-10ps), Port 2 |

### Role

The OC200 provides centralized management for the Omada network infrastructure.

It is deliberately located on the management VLAN rather than a user or IoT VLAN.

---

## EAP720-1

<a name="eap720-1"></a>

[↩ TOC](#table-of-contents)

**Manufacturer:** TP-Link  
**Model:** EAP720  
**Role:** Wireless access point

### Identity

| Property | Value |
|---|---|
| Hostname | `EAP720-1` |
| IP Address | `10.1.1.8` |
| MAC Address | `10-5A-95-2F-66-B1` |
| Management VLAN | [`VLAN 1`](#vlan-1--mgmt) |
| Connected To | [`Switch 1`](#switch-1--t1500g-10ps), Port 7 |

### Switch Port

Port 7 is configured as a trunk:

- Native / untagged VLAN: [`VLAN 1 — MGMT`](#vlan-1--mgmt)
- Tagged VLANs: wireless client VLANs

The access point therefore uses the management VLAN for its own management traffic while carrying wireless client VLANs as tagged traffic.

---

## EAP720-2

<a name="eap720-2"></a>

[↩ TOC](#table-of-contents)

**Manufacturer:** TP-Link  
**Model:** EAP720  
**Role:** Reserved / future wireless access point

### Status

EAP720-2 is reserved for future deployment.

The intended switch configuration is equivalent to [`EAP720-1`](#eap720-1):

- Native VLAN: [`VLAN 1 — MGMT`](#vlan-1--mgmt)
- Tagged wireless VLANs
- Connected to [`Switch 1`](#switch-1--t1500g-10ps), Port 8

---

# Infrastructure Nodes

<a name="infrastructure-nodes"></a>

Infrastructure nodes are the systems that provide computing, storage, orchestration, services, development environments, and other foundational capabilities to the network.

---

## BoraBora

<a name="node-borabora"></a>

[↩ TOC](#table-of-contents)

**Hardware:** Intel NUC7i5BNH  
**Processor:** Intel Core i5-7260U  
**Memory:** 32 GB  
**Operating System:** TrueNAS SCALE 25.10  
**IP Address:** `10.1.2.2`  
**VLAN:** [`VLAN 2 — INFRA`](#vlan-2--infra)  
**Switch:** [`Switch 1`](#switch-1--t1500g-10ps), Port 3

### Identity

| Property | Value |
|---|---|
| Hostname | `BoraBora` |
| Hardware | Intel NUC7i5BNH |
| CPU | Intel Core i5-7260U |
| RAM | 32 GB |
| OS | TrueNAS SCALE 25.10 |
| IP | `10.1.2.2` |
| VLAN | `2` |
| Switch Port | Switch 1 / Port 3 |

### Role

BoraBora is the primary network storage server.

Its role includes storage and media infrastructure and may expand to host additional services where appropriate.

### Network Placement

BoraBora is intentionally placed on [`VLAN 2 — INFRA`](#vlan-2--infra).

This provides a clear separation between infrastructure services and end-user/client networks.

---

## Fiji

<a name="node-fiji"></a>

[↩ TOC](#table-of-contents)

**Hardware:** HP t655  
**Processor:** AMD Ryzen Embedded  
**Memory:** 32 GB  
**Operating System:** Ubuntu Server 22.04.5 LTS  
**IP Address:** `10.1.2.3`  
**VLAN:** [`VLAN 2 — INFRA`](#vlan-2--infra)  
**Switch:** [`Switch 1`](#switch-1--t1500g-10ps), Port 5

### Identity

| Property | Value |
|---|---|
| Hostname | `Fiji` |
| Hardware | HP t655 |
| CPU | AMD Ryzen Embedded |
| RAM | 32 GB |
| OS | Ubuntu Server 22.04.5 LTS |
| IP | `10.1.2.3` |
| VLAN | `2` |
| Switch Port | Switch 1 / Port 5 |

### Role

Fiji is an infrastructure compute node.

It provides a general-purpose Linux environment for server workloads that do not belong on the storage platform.

---

## KonTiki

<a name="node-kontiki"></a>

[↩ TOC](#table-of-contents)

**Hardware:** Apple MacBook Pro M1 Max, A2485  
**Memory:** 32 GB  
**Operating System:** macOS Sequoia 15.1.1 (24B91)  
**IP Address:** `10.1.2.4`  
**VLAN:** [`VLAN 2 — INFRA`](#vlan-2--infra)  
**Switch:** [`Switch 1`](#switch-1--t1500g-10ps), Port 4

### Identity

| Property | Value |
|---|---|
| Hostname | `KonTiki` |
| Hardware | Apple MacBook Pro M1 Max |
| Model | A2485 |
| RAM | 32 GB |
| OS | macOS Sequoia 15.1.1 |
| OS Build | `24B91` |
| IP | `10.1.2.4` |
| VLAN | `2` |
| Switch Port | Switch 1 / Port 4 |

### Role

KonTiki is the local AI and development compute node.

It is also used as a general-purpose infrastructure workstation/server.

### Network Placement

KonTiki is on [`VLAN 2 — INFRA`](#vlan-2--infra) because its server and infrastructure functions require controlled access to other infrastructure systems.

---

## Home Assistant

<a name="node-home-assistant"></a>

[↩ TOC](#table-of-contents)

**Hardware:** Home Assistant Green  
**Role:** Home automation controller  
**VLAN:** [`VLAN 2 — INFRA`](#vlan-2--infra)  
**Switch:** [`Switch 1`](#switch-1--t1500g-10ps), Port 6

### Identity

| Property | Value |
|---|---|
| Hostname | `Home Assistant` |
| Hardware | Home Assistant Green |
| VLAN | `2` |
| Switch Port | Switch 1 / Port 6 |

### Network Design

Home Assistant currently resides on [`VLAN 2 — INFRA`](#vlan-2--infra).

A secondary USB Ethernet interface is planned for future connectivity to [`VLAN 66 — IoT`](#vlan-66--iot).

This design allows Home Assistant to function as a controlled bridge between trusted infrastructure and IoT devices without placing the primary Home Assistant host directly into the IoT trust domain.

---

## Moorea

<a name="node-moorea"></a>

[↩ TOC](#table-of-contents)

**Hardware:** Dell Wyse 5070  
**Status:** Offline / dead

### Identity

| Property | Value |
|---|---|
| Hostname | `Moorea` |
| Hardware | Dell Wyse 5070 |
| Status | Offline / dead |

### Future Role

Moorea is retained in the documentation as an infrastructure node because the hardware may eventually be repaired, repurposed, or replaced.

No active network configuration should currently be assigned to it.

---

## Tahiti

<a name="node-tahiti"></a>

[↩ TOC](#table-of-contents)

**Hardware:** Lenovo laptop  
**Operating System:** Linux Mint 22

### Wired Network

| Property | Value |
|---|---|
| Connection | Ethernet |
| VLAN | [`VLAN 1 — MGMT`](#vlan-1--mgmt) |
| IP Address | `10.1.1.88` |

### Wireless Network

Tahiti also connects to the [`TikiShack`](#vlan-22--tikishack) wireless network.

| Property | Value |
|---|---|
| SSID | `TikiShack` |
| VLAN | [`VLAN 22 — TikiShack`](#vlan-22--tikishack) |

### Role

Tahiti is a mobile administration and general-purpose Linux workstation.

Its wired management connection provides a predictable administrative path, while its wireless connection allows normal client-network operation.

---

## Remote Failover Node

<a name="node-remote-failover"></a>

[↩ TOC](#table-of-contents)

**Status:** Planned

A remote failover node is planned as part of the long-term resilience architecture.

The exact hardware, location, connectivity, and service set have not yet been finalized.

The intended design is to provide an independent recovery and/or management path should the primary TikiShack infrastructure become unavailable.

---

# VLANs

<a name="vlans"></a>

VLANs are the primary logical segmentation mechanism for the TikiShack/SovrIT network.

The design favors explicit segmentation over implicit trust.

---

## VLAN 1 — MGMT

<a name="vlan-1--mgmt"></a>

[↩ TOC](#table-of-contents)

**VLAN ID:** `1`  
**Name:** `MGMT`  
**Purpose:** Network management

### Role

VLAN 1 is the administrative network for network infrastructure and management interfaces.

Devices currently associated with VLAN 1 include:

- [`ER707-M2`](#router--gateway--er707-m2)
- [`T1500G-10PS`](#switch-1--t1500g-10ps)
- [`TL-SG1024DE`](#switch-2--tl-sg1024de)
- [`RLA-PS1`](#switch-3--reolink-rla-ps1)
- [`OC200`](#oc200)
- [`EAP720-1`](#eap720-1)
- [`EAP720-2`](#eap720-2)
- wired [`Tahiti`](#node-tahiti)

### Security Intent

Management interfaces should not be exposed to ordinary client networks.

Access to VLAN 1 should be restricted through explicit firewall policy.

---

## VLAN 2 — INFRA

<a name="vlan-2--infra"></a>

[↩ TOC](#table-of-contents)

**VLAN ID:** `2`  
**Name:** `INFRA`  
**Purpose:** Servers and infrastructure

### Role

VLAN 2 contains systems that provide infrastructure services.

Primary members include:

- [`BoraBora`](#node-borabora)
- [`Fiji`](#node-fiji)
- [`KonTiki`](#node-kontiki)
- [`Home Assistant`](#node-home-assistant)

### Security Intent

Infrastructure systems are trusted relative to ordinary clients, but trust is not unlimited.

Inter-VLAN access should be explicitly authorized according to service requirements.

---

## VLAN 3 — Printer

<a name="vlan-3--printer"></a>

[↩ TOC](#table-of-contents)

**VLAN ID:** `3`  
**Name:** `Printer`  
**Purpose:** Printers

### Security Intent

Printers are treated as peripheral devices rather than trusted infrastructure.

Client access should be permitted only for required printing/discovery protocols.

Printer-initiated access to infrastructure should be restricted.

---

## VLAN 22 — TikiShack

<a name="vlan-22--tikishack"></a>

[↩ TOC](#table-of-contents)

**VLAN ID:** `22`  
**Name:** `TikiShack`  
**Purpose:** Primary trusted wireless client network

### Role

TikiShack is the primary trusted wireless network for normal household/client devices.

### VPN

The TikiShack network is associated with the Boston VPN path where configured.

### Security Intent

This is a trusted client network, but it is still distinct from the infrastructure network.

Clients should access infrastructure services through explicit firewall policy.

---

## VLAN 23 — Panama

<a name="vlan-23--panama"></a>

[↩ TOC](#table-of-contents)

**VLAN ID:** `23`  
**Name:** `Panama`  
**Purpose:** Direct-WAN wireless client network

### Role

Panama provides a wireless client path that bypasses the normal Boston VPN route and uses the direct WAN connection.

### Security Intent

Panama is a separate logical network so that VPN routing policy can be selected at the network boundary rather than individually on every client.

---

## VLAN 33 — Guest

<a name="vlan-33--guest"></a>

[↩ TOC](#table-of-contents)

**VLAN ID:** `33`  
**Name:** `Guest`  
**Purpose:** Guest access

### Security Intent

Guest devices are untrusted.

The guest network should have Internet access while being denied access to internal infrastructure and household networks except where explicitly required.

---

## VLAN 44 — Sentry

<a name="vlan-44--sentry"></a>

[↩ TOC](#table-of-contents)

**VLAN ID:** `44`  
**Name:** `Sentry`  
**Purpose:** Security and observation infrastructure

### Role

Sentry is intended for security-oriented devices such as cameras and related observation infrastructure.

### Security Intent

Sentry devices should have limited access to infrastructure.

Where practical, the preferred direction of trust is:

**Infrastructure → Sentry**

rather than:

**Sentry → Infrastructure**

This limits the consequences of a compromised security device.

---

## VLAN 55 — Media

<a name="vlan-55--media"></a>

[↩ TOC](#table-of-contents)

**VLAN ID:** `55`  
**Name:** `Media`  
**Purpose:** Media devices and services

### Role

Media devices and services are logically separated from general infrastructure.

[`BoraBora`](#node-borabora) may provide storage to systems on this VLAN where explicitly permitted.

### Security Intent

Media clients should not receive unrestricted access to management or infrastructure services.

---

## VLAN 66 — IoT

<a name="vlan-66--iot"></a>

[↩ TOC](#table-of-contents)

**VLAN ID:** `66`  
**Name:** `IoT`  
**Purpose:** Internet-of-Things devices

### Role

VLAN 66 provides an isolated network for IoT devices.

### Security Intent

IoT devices are inherently less trusted than general-purpose computers and servers.

The network therefore uses segmentation to limit their ability to initiate connections into:

- management;
- infrastructure;
- household clients;
- development systems.

[`Home Assistant`](#node-home-assistant) is intended to provide controlled access to IoT devices.

---

## VLAN 99 — Dev-Sandbox

<a name="vlan-99--dev-sandbox"></a>

[↩ TOC](#table-of-contents)

**VLAN ID:** `99`  
**Name:** `Dev-Sandbox`  
**Purpose:** Development and experimentation

### Role

VLAN 99 is intended for development, testing, experimentation, and systems that should not be treated as production infrastructure.

### Security Intent

Development systems should be assumed to have a higher probability of configuration changes, experimental software, and temporary services.

They should therefore be isolated from production infrastructure except where explicit access is required.

---

# Wireless Networks

<a name="wireless-networks"></a>

Wireless networks map SSIDs to VLANs and, where applicable, to different WAN routing policies.

---

## TikiShack

<a name="ssid-tikishack"></a>

[↩ TOC](#table-of-contents)

**SSID:** `TikiShack`  
**VLAN:** [`VLAN 22`](#vlan-22--tikishack)  
**WAN Path:** Boston VPN

### Purpose

Primary trusted wireless network.

This is the normal wireless network for household/client devices that should use the VPN egress.

---

## Panama

<a name="ssid-panama"></a>

[↩ TOC](#table-of-contents)

**SSID:** `Panama`  
**VLAN:** [`VLAN 23`](#vlan-23--panama)  
**WAN Path:** Direct WAN

### Purpose

Provides wireless clients with direct Internet access through the local WAN rather than the Boston VPN.

---

## Guest

<a name="ssid-guest"></a>

[↩ TOC](#table-of-contents)

**SSID:** `Guest`  
**VLAN:** [`VLAN 33`](#vlan-33--guest)  
**WAN Path:** Direct WAN

### Purpose

Guest access.

Guests should have Internet access without access to internal infrastructure.

---

## Sentry

<a name="ssid-sentry"></a>

[↩ TOC](#table-of-contents)

**SSID:** `Sentry`  
**VLAN:** [`VLAN 44`](#vlan-44--sentry)

### Purpose

Wireless security/observation devices where required.

---

## Media

<a name="ssid-media"></a>

[↩ TOC](#table-of-contents)

**SSID:** `Media`  
**VLAN:** [`VLAN 55`](#vlan-55--media)

### Purpose

Wireless media devices.

---

## IoT

<a name="ssid-iot"></a>

[↩ TOC](#table-of-contents)

**SSID:** `IoT`  
**VLAN:** [`VLAN 66`](#vlan-66--iot)

### Purpose

Wireless IoT devices.

---

## Dev-Sandbox

<a name="ssid-dev-sandbox"></a>

[↩ TOC](#table-of-contents)

**SSID:** `Dev-Sandbox`  
**VLAN:** [`VLAN 99`](#vlan-99--dev-sandbox)

### Purpose

Wireless development and testing.

---

# Physical Connectivity

<a name="physical-connectivity"></a>

The physical network is deliberately documented separately from the logical architecture.

Physical topology answers:

> What is physically connected to what?

Logical topology answers:

> What network does that connection carry?

Keeping those concepts separate makes the configuration easier to maintain.

---

## Primary Gateway Connection

<a name="physical-gateway"></a>

[`ER707-M2`](#router--gateway--er707-m2) connects to [`Switch 1`](#switch-1--t1500g-10ps), Port 1.

### Port 1

**Mode:** Trunk

**Native / Untagged VLAN:** [`VLAN 1 — MGMT`](#vlan-1--mgmt)

**Tagged VLANs:**

- [`VLAN 2 — INFRA`](#vlan-2--infra)
- [`VLAN 3 — Printer`](#vlan-3--printer)
- [`VLAN 22 — TikiShack`](#vlan-22--tikishack)
- [`VLAN 23 — Panama`](#vlan-23--panama)
- [`VLAN 33 — Guest`](#vlan-33--guest)
- [`VLAN 44 — Sentry`](#vlan-44--sentry)
- [`VLAN 55 — Media`](#vlan-55--media)
- [`VLAN 66 — IoT`](#vlan-66--iot)
- [`VLAN 99 — Dev-Sandbox`](#vlan-99--dev-sandbox)

---

## Switch 1 — Port 2

<a name="physical-switch1-port2"></a>

[↩ TOC](#table-of-contents)

**Connected Device:** [`OC200`](#oc200)

**Mode:** Access

**Untagged VLAN:** [`VLAN 1 — MGMT`](#vlan-1--mgmt)

The Omada controller is therefore directly attached to the management network.

---

## Switch 1 — Port 3

<a name="physical-switch1-port3"></a>

[↩ TOC](#table-of-contents)

**Connected Device:** [`BoraBora`](#node-borabora)

**Mode:** Access

**Untagged VLAN:** [`VLAN 2 — INFRA`](#vlan-2--infra)

---

## Switch 1 — Port 4

<a name="physical-switch1-port4"></a>

[↩ TOC](#table-of-contents)

**Connected Device:** [`KonTiki`](#node-kontiki)

**Mode:** Access

**Untagged VLAN:** [`VLAN 2 — INFRA`](#vlan-2--infra)

---

## Switch 1 — Port 5

<a name="physical-switch1-port5"></a>

[↩ TOC](#table-of-contents)

**Connected Device:** [`Fiji`](#node-fiji)

**Mode:** Access

**Untagged VLAN:** [`VLAN 2 — INFRA`](#vlan-2--infra)

---

## Switch 1 — Port 6

<a name="physical-switch1-port6"></a>

[↩ TOC](#table-of-contents)

**Connected Device:** [`Home Assistant`](#node-home-assistant)

**Mode:** Access

**Untagged VLAN:** [`VLAN 2 — INFRA`](#vlan-2--infra)

A future secondary USB Ethernet interface is intended to provide Home Assistant connectivity to [`VLAN 66 — IoT`](#vlan-66--iot).

---

## Switch 1 — Port 7

<a name="physical-switch1-port7"></a>

[↩ TOC](#table-of-contents)

**Connected Device:** [`EAP720-1`](#eap720-1)

**Mode:** Trunk

**Native / Untagged VLAN:** [`VLAN 1 — MGMT`](#vlan-1--mgmt)

**Tagged VLANs:** Wireless client VLANs.

The access point carries management traffic untagged while client traffic is VLAN-tagged.

---

## Switch 1 — Port 8

<a name="physical-switch1-port8"></a>

[↩ TOC](#table-of-contents)

**Connected Device:** [`EAP720-2`](#eap720-2)

**Status:** Reserved

**Mode:** Trunk

**Native / Untagged VLAN:** [`VLAN 1 — MGMT`](#vlan-1--mgmt)

**Tagged VLANs:** Wireless client VLANs.

---

## Access Point Connectivity

<a name="physical-ap-connectivity"></a>

Both EAP720 access points are designed to use the same fundamental VLAN model:

- management traffic on [`VLAN 1`](#vlan-1--mgmt);
- wireless client traffic tagged according to the SSID's VLAN;
- no requirement for wireless clients to share the management VLAN.

This keeps wireless infrastructure and wireless clients logically separated.

---

# Logical Network Architecture

<a name="logical-network-architecture"></a>

The logical architecture defines how the physical network is divided into security and service domains.

The fundamental design principles are:

- strong VLAN segmentation;
- least privilege;
- explicit routing and firewall policy;
- infrastructure-first design;
- wired-first infrastructure;
- observation over inference;
- security before convenience.

---

## Layer 2 Architecture

<a name="layer-2-architecture"></a>

The switching infrastructure provides Layer-2 VLAN transport.

The primary VLAN trunk is:

[`ER707-M2`](#router--gateway--er707-m2) → [`Switch 1`](#switch-1--t1500g-10ps)

From Switch 1, VLANs are presented to:

- infrastructure access ports;
- management devices;
- wireless access points;
- additional switches.

### Access Ports

Access ports carry a single untagged VLAN.

Current examples:

| Port | Device | VLAN |
|---:|---|---:|
| 2 | [`OC200`](#oc200) | [`1`](#vlan-1--mgmt) |
| 3 | [`BoraBora`](#node-borabora) | [`2`](#vlan-2--infra) |
| 4 | [`KonTiki`](#node-kontiki) | [`2`](#vlan-2--infra) |
| 5 | [`Fiji`](#node-fiji) | [`2`](#vlan-2--infra) |
| 6 | [`Home Assistant`](#node-home-assistant) | [`2`](#vlan-2--infra) |

### Trunk Ports

Trunk ports carry multiple VLANs.

Current examples:

| Port | Device | Native VLAN | Tagged VLANs |
|---:|---|---:|---|
| 1 | [`ER707-M2`](#router--gateway--er707-m2) | `1` | `2,3,22,23,33,44,55,66,99` |
| 7 | [`EAP720-1`](#eap720-1) | `1` | Wireless VLANs |
| 8 | [`EAP720-2`](#eap720-2) | `1` | Wireless VLANs |

---

## Access Port Design

<a name="access-port-design"></a>

Access ports should be explicitly assigned to the VLAN appropriate for the attached device.

A device should not be placed on a VLAN merely because that VLAN happens to provide connectivity.

The VLAN should represent the device's intended trust and service domain.

Examples:

- network infrastructure → [`VLAN 1`](#vlan-1--mgmt);
- servers → [`VLAN 2`](#vlan-2--infra);
- printers → [`VLAN 3`](#vlan-3--printer);
- trusted wireless clients → [`VLAN 22`](#vlan-22--tikishack);
- direct-WAN wireless clients → [`VLAN 23`](#vlan-23--panama);
- guests → [`VLAN 33`](#vlan-33--guest);
- security devices → [`VLAN 44`](#vlan-44--sentry);
- media devices → [`VLAN 55`](#vlan-55--media);
- IoT → [`VLAN 66`](#vlan-66--iot);
- development → [`VLAN 99`](#vlan-99--dev-sandbox).

---

## Trunk Port Design

<a name="trunk-port-design"></a>

Trunks should explicitly define:

1. native / untagged VLAN;
2. permitted tagged VLANs;
3. connected device;
4. intended purpose.

Avoid broad "allow all VLANs" trunk configurations where the connected device does not actually require them.

This makes the physical configuration itself an enforcement mechanism and reduces accidental exposure.

---

## Access Point Design

<a name="ap-design"></a>

Access points are treated as infrastructure devices rather than as ordinary wireless clients.

Their management interface belongs on [`VLAN 1 — MGMT`](#vlan-1--mgmt).

Client SSIDs map directly to VLANs:

| SSID | VLAN | Purpose |
|---|---:|---|
| [`TikiShack`](#ssid-tikishack) | `22` | Trusted clients |
| [`Panama`](#ssid-panama) | `23` | Direct-WAN clients |
| [`Guest`](#ssid-guest) | `33` | Guest clients |
| [`Sentry`](#ssid-sentry) | `44` | Security devices |
| [`Media`](#ssid-media) | `55` | Media devices |
| [`IoT`](#ssid-iot) | `66` | IoT devices |
| [`Dev-Sandbox`](#ssid-dev-sandbox) | `99` | Development |

This provides a clean mapping:

**SSID → VLAN → security policy → WAN policy**

---

## Layer 3 / Gateway Architecture

<a name="layer-3-gateway"></a>

The [`ER707-M2`](#router--gateway--er707-m2) is the primary Layer-3 gateway.

Inter-VLAN traffic should pass through the router where firewall policy can be applied.

The design therefore avoids relying on Layer-2 proximity as an indicator of trust.

### Routing Domains

The principal routing domains are:

- management;
- infrastructure;
- peripherals;
- trusted clients;
- direct-WAN clients;
- guests;
- security/observation;
- media;
- IoT;
- development.

### Firewall Philosophy

Firewall rules should follow least privilege.

The default question should be:

> Does this traffic need to exist?

rather than:

> How can we make this traffic work?

Where a service requires access across VLAN boundaries, the required source, destination, protocol, and port should be explicitly documented.

---

## Infrastructure Philosophy

<a name="infrastructure-philosophy"></a>

Infrastructure is the foundation of the network.

The architecture therefore gives special consideration to:

- storage;
- DNS;
- authentication;
- certificate infrastructure;
- monitoring;
- backup;
- network management;
- automation;
- AI compute;
- service orchestration.

Infrastructure systems are centralized where that improves reliability and observability, but critical dependencies should not create unnecessary single points of failure.

[`BoraBora`](#node-borabora), [`Fiji`](#node-fiji), and [`KonTiki`](#node-kontiki) form the current core infrastructure compute/storage group.

---

## Wireless Philosophy

<a name="wireless-philosophy"></a>

Wireless networks are treated as logical access domains rather than simply different names for the same network.

Different SSIDs can therefore represent different:

- trust levels;
- VLANs;
- firewall policies;
- WAN paths;
- device classes.

The network should avoid requiring per-device configuration merely to select an Internet egress policy.

Instead:

**SSID → VLAN → routing policy**

is the preferred abstraction.

---

## Security Architecture

<a name="security-architecture"></a>

The security architecture follows a layered model.

### Segmentation

VLANs establish coarse security boundaries.

### Firewall

The gateway establishes explicit Layer-3 policy.

### Identity

Authentication systems should eventually provide centralized identity and strong authentication.

### Certificates

A private PKI can provide internal TLS certificates and machine identity.

### Monitoring

Monitoring should observe the network rather than infer security solely from configuration.

### Detection

Security tooling can identify anomalous behavior and enforce additional controls.

### Zero Trust

Network location alone should not be considered sufficient authorization.

A device being on [`VLAN 2`](#vlan-2--infra), for example, does not automatically mean that it should be able to access every service on every other infrastructure device.

---

## Network Management

<a name="network-management"></a>

Network infrastructure is centralized under the Omada management plane where supported.

[`OC200`](#oc200) provides the Omada controller function.

Management devices reside on [`VLAN 1 — MGMT`](#vlan-1--mgmt).

Administrative access should be restricted to trusted management paths.

---

## Internet Connectivity

<a name="internet-connectivity"></a>

The primary Internet gateway is [`ER707-M2`](#router--gateway--er707-m2).

The network is designed to support multiple WAN paths.

### Primary WAN

The normal WAN path is provided by the primary Internet connection attached to the ER707-M2.

### Secondary / Cellular WAN

[`W1850-5GB`](#router-2--w1850-5gb) is intended to provide a secondary cellular/failover path.

Its actual network configuration remains subject to physical verification.

---

## VPN Architecture

<a name="vpn-architecture"></a>

The network supports routing selected client networks through a remote VPN endpoint.

The principal current use case is the Boston VPN path associated with [`VLAN 22 — TikiShack`](#vlan-22--tikishack).

This approach allows VPN policy to be attached to a network rather than to individual clients.

### Direct-WAN Exception

[`VLAN 23 — Panama`](#vlan-23--panama) provides a direct-WAN alternative.

This is useful for services that require local geographic egress or otherwise behave poorly through the VPN.

---

## Service Discovery

<a name="service-discovery"></a>

Service discovery should be deliberately controlled across VLAN boundaries.

mDNS, multicast, broadcast discovery, and similar mechanisms should not be allowed to cross segmentation boundaries indiscriminately.

Where cross-VLAN discovery is necessary, the architecture should provide an explicit reflector or proxy rather than weakening segmentation globally.

---

## Home Assistant Architecture

<a name="home-assistant-architecture"></a>

[`Home Assistant`](#node-home-assistant) is currently attached to [`VLAN 2 — INFRA`](#vlan-2--infra).

A future secondary Ethernet interface is intended to connect it to [`VLAN 66 — IoT`](#vlan-66--iot).

This creates a deliberate architectural separation:

**Home Assistant infrastructure interface**

→ trusted infrastructure

**Home Assistant IoT interface**

→ IoT devices

The objective is to permit Home Assistant to manage IoT devices without requiring every infrastructure host to have equivalent access to the IoT network.

---

## AI Platform

<a name="ai-platform"></a>

[`KonTiki`](#node-kontiki) is the primary local AI compute platform.

It is located on [`VLAN 2 — INFRA`](#vlan-2--infra).

The AI environment is intentionally treated as infrastructure rather than as an ordinary workstation because local models and AI services may interact with:

- network services;
- storage;
- development environments;
- automation;
- source repositories;
- other infrastructure systems.

AI workloads should therefore be subject to the same segmentation and access-control principles as other infrastructure workloads.

---

## Storage Philosophy

<a name="storage-philosophy"></a>

[`BoraBora`](#node-borabora) is the primary storage platform.

Storage should be treated as infrastructure and protected accordingly.

Access should be granted according to service requirements rather than simply because a client can reach the storage VLAN.

Important storage considerations include:

- backups;
- snapshots;
- replication;
- media;
- application data;
- configuration backups;
- disaster recovery.

---

# Future Architecture

<a name="future-architecture"></a>

Future architecture should extend the existing segmentation and infrastructure principles rather than introduce unrelated parallel systems.

---

## Firewall / ACL Expansion

<a name="future-firewall"></a>

The next major logical-network phase is to formalize inter-VLAN firewall policy.

The intended model is:

1. deny unnecessary cross-VLAN traffic;
2. explicitly permit required services;
3. document every exception;
4. periodically review exceptions.

A rule should ideally answer:

- source;
- destination;
- protocol;
- port;
- purpose;
- justification.

---

## IPv6

<a name="future-ipv6"></a>

IPv6 is intentionally deferred until the IPv4/VLAN architecture is stable.

When implemented, IPv6 should receive equivalent segmentation and firewall treatment rather than being allowed to bypass the IPv4 security architecture.

---

## High Availability

<a name="future-ha"></a>

High availability is a future objective rather than a current capability.

Potential areas include:

- gateway redundancy;
- DNS redundancy;
- storage redundancy;
- service redundancy;
- remote recovery;
- alternate Internet connectivity.

HA should be introduced where it materially improves resilience rather than simply adding complexity.

---

## Additional Infrastructure

<a name="future-infrastructure"></a>

Additional infrastructure systems may eventually include:

- centralized authentication;
- WebAuthn;
- password management;
- internal PKI;
- NTP;
- DNS filtering;
- monitoring;
- alerting;
- intrusion detection;
- backup orchestration;
- automation;
- source control;
- service orchestration.

These systems should remain subordinate to the fundamental network architecture.

---

## Remote Resilience

<a name="future-remote-resilience"></a>

A remote infrastructure/failover node is planned.

The purpose is to ensure that a failure at TikiShack does not simultaneously eliminate every recovery mechanism.

The remote node should ideally provide some combination of:

- secure remote access;
- configuration backup;
- DNS;
- authentication;
- monitoring;
- VPN endpoint;
- disaster-recovery orchestration.

---

# Network Design Status

<a name="network-design-status"></a>

The network is operational but remains under active development.

---

## Operational

The following elements are considered operational components of the current design:

- TP-Link Omada management;
- primary gateway;
- managed switching;
- VLAN segmentation;
- infrastructure VLAN;
- management VLAN;
- primary wireless network;
- direct-WAN wireless network;
- guest network;
- infrastructure hosts;
- primary storage;
- local AI compute.

---

## In Development

The following areas remain under active development:

- comprehensive firewall/ACL policy;
- IoT segmentation;
- Sentry/security segmentation;
- media segmentation;
- development sandbox;
- Home Assistant dual-interface architecture;
- cellular WAN failover;
- remote failover infrastructure;
- centralized security monitoring;
- service-level authentication;
- internal PKI;
- broader automation.

---

## Deferred

The following items are deliberately deferred:

- IPv6;
- full high availability;
- complete remote-site architecture;
- additional access points where not yet needed;
- unnecessary hardware expansion.

---

# Guiding Principles

<a name="guiding-principles"></a>

The TikiShack/SovrIT network follows these principles.

## 1. Infrastructure First

Build the network foundation before layering applications and convenience services on top of it.

## 2. Explicit Over Implicit

Configuration should explicitly state what is permitted.

Avoid relying on undocumented defaults.

## 3. Least Privilege

A device should receive only the access it needs.

## 4. Segmentation

Different trust domains should have different network boundaries.

## 5. Wired First

Core infrastructure should use wired connectivity whenever practical.

Wireless is primarily an access mechanism for clients and mobile devices.

## 6. Observation Over Inference

The network should collect sufficient information to determine what is actually happening.

Do not rely solely on assumptions based on configuration.

## 7. Security Before Convenience

Convenience should not silently weaken network boundaries.

If a convenience feature requires reduced security, the tradeoff should be explicit.

## 8. Document the Why

Configuration documentation should explain not only what a device or VLAN does, but why it exists.

This makes future changes safer.

## 9. Make the Network Self-Describing

Device names, VLAN names, port descriptions, and documentation should reinforce one another.

A person should be able to move from:

**device → port → VLAN → gateway → policy**

without reconstructing the topology from unrelated documents.

## 10. Design for AI Readability

Network documentation should use consistent names, explicit relationships, stable anchors, and semantic structure.

Avoid encoding important relationships solely in visual formatting.

---

# Revision History

<a name="revision-history"></a>

| Date | Revision | Description |
|---|---|---|
| 2026-09-28 | 2.0 | Refactored from YAML-heavy configuration into semantic Markdown |
| 2026-09-28 | 2.0 | Added canonical entity anchors and internal cross-links |
| 2026-09-28 | 2.0 | Added manufacturer/model links |
| 2026-09-28 | 2.0 | Separated physical, logical, VLAN, wireless, and infrastructure documentation |
| 2026-09-28 | 2.0 | Added Network Entity Index and VLAN Index |
| 2026-09-28 | 2.0 | Flagged W1850-5GB identity/configuration inconsistency for verification |

---

**End of Document**
