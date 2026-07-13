# TikiShack – SovrIT Network Configuration

> **Project:** SovrIT PTI (Personal/Private Technology Infrastructure)
>
> This document is the authoritative technical specification for the TikiShack network.
> It documents the physical infrastructure, logical network topology, infrastructure
> nodes, wireless configuration, and operational design decisions.
>
> This document is intended to serve as the primary source of truth for rebuilding,
> maintaining, troubleshooting, securing, and extending the network.

---

# Table of Contents

- [Network Management Devices](#network-management-devices)
  - [Router / Gateway](#router--gateway)
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
  - [Home Assistant Green](#home-assistant-green)
  - [Development Node (Moorea)](#development-node-moorea)
  - [Administration Node (Tahiti)](#administration-node-tahiti)
  - [Remote Failover Node](#remote-failover-node)
- [Network Configuration (Source of Truth)](#network-configuration-source-of-truth)
  - [Switch Port Configuration](#switch-port-configuration)
  - [Wireless Networks](#wireless-networks)
  - [VLAN Device Inventory](#vlan-device-inventory)
- [Network Design Decisions](#network-design-decisions)
- [Network Design Status](#network-design-status)

---

# Network Management Devices

> **Management VLAN:** VLAN 1 (MGMT)

All network infrastructure devices reside on the Management VLAN unless otherwise noted.

---

## Router / Gateway

```yaml
ROUTER_GATEWAY:
  Make: TP-Link
  Model: ER707-M2
  Revision: v1.3
  FirmwareVersion: 1.4.2 Build 20260509 Rel.32107

  Description: >
    Primary gateway, firewall, DHCP server,
    VLAN router and VPN endpoint.

  Network:
    VLAN: 1-MGMT
    IP: 10.1.1.1
    IP_Assignment: Static
    MAC_ID: AC-A7-F1-D6-57-A6
    Connection: 802.3 (Ethernet)
    Cable_Color: White (Flat)
    SSID: N/A

  PortMap:
    Port 1 (WAN): ISP modem/router
    Port 2 (WAN/LAN): UNUSED
    Port 3 (WAN/LAN): UNUSED
    Port 4 (WAN/LAN): Moorea (Development/Test Node)
    Port 5 (WAN/LAN): Tahiti (Administration Laptop)
    Port 6 (WAN/LAN): Switch 1
    Port 7 (WAN/LAN): UNUSED
```

---

## Switch 1

```yaml
SWITCH1:
  Make: TP-Link
  Model: T1500G-10PS (TL-SG2210P)
  Revision: v2.8
  FirmwareVersion: 2.0.6 Build 20200805 Rel.57865

  Description: >
    Primary managed PoE switch providing
    wired connectivity and power for the
    OC200 controller and EAP720 access point.

  Network:
    VLAN: 1-MGMT
    IP: 10.1.1.2
    IP_Assignment: Static
    MAC_ID: 68-FF-7B-F9-94-2D
    Connection: 802.3 (Ethernet)
    SSID: N/A

  PortMap:
    Port 1: ER707-M2 Gateway
    Port 2: OC200 Controller (PoE)
    Port 3: BoraBora
    Port 4: Reserved for KonTiki
    Port 5: Fiji
    Port 6: Home Assistant Green
    Port 7: EAP720-1 (PoE)
    Port 8: Reserved for EAP720-2 (PoE)
```

---

## Switch 2

```yaml
SWITCH2:
  Make: TP-Link
  Model: TL-SG1024DE

  Description: >
    Reserved for future deployment.

  Network:
    VLAN: 1-MGMT
    Connection: None
    SSID: N/A
```

---

## Switch 3

```yaml
SWITCH3:
  Make: Reolink
  Model: RLA-PS1

  Description: >
    Reserved for future deployment.
    Intended to provide dedicated PoE
    connectivity for security cameras
    and related surveillance equipment.

  Network:
    Connection: None
    SSID: N/A
```

---

## Omada SDN Controller

```yaml
SDN_CONTROLLER:
  Make: TP-Link
  Model: OC200
  Revision: v1.0
  FirmwareVersion: 1.40.18 Build 20260506 Rel.74003

  Description: >
    Dedicated Omada SDN Controller
    replacing the previous Docker-based
    controller.

  Network:
    VLAN: 1-MGMT
    IP: 10.1.1.5
    IP_Assignment: Static
    MAC_ID: B0-BE-76-C6-3A-FB
    Connection: 802.3 (Ethernet)
    Cable_Color: Blue
    Device_Port: Switch 1 / Port 2
    SSID: N/A
```

---

## Wireless Access Point 1

```yaml
WAP1:
  Make: TP-Link
  Model: EAP720 (US)
  Revision: v1.0
  FirmwareVersion: 1.1.4 Build 20251224 Rel.62114

  Description: >
    Primary wireless access point.

  Network:
    VLAN: 1-MGMT
    IP: 10.1.1.8
    IP_Assignment: Static
    MAC_ID: 10-5A-95-2F-66-B1
    Connection: 802.3 (Ethernet)
    Cable_Color: Yellow
    Device_Port: Switch 1 / Port 7
    SSID: N/A
```

---

## Wireless Access Point 2

```yaml
WAP2:
  Make: TP-Link
  Model: EAP720 (US)

  Description: >
    Reserved for future deployment.

  Network:
    Connection: None
    SSID: N/A
```

---

# Infrastructure Nodes

> **Infrastructure VLAN:** VLAN 2 (INFRA)

All infrastructure systems are connected via wired Ethernet and operate as
access-port devices on VLAN 2 unless explicitly documented otherwise.

## Storage Node (BoraBora)

```yaml
STORAGE_NODE:
  Hostname: BoraBora

  Role:
    - Primary NAS
    - Media Storage
    - Backup Repository
    - Infrastructure Storage
    - Shared Storage
    - Migration Support

  Hardware:
    Manufacturer: Intel
    Model: NUC7i5BNH
    CPU: Intel Core i5-7260U
    Memory: 32 GB
    Boot_Device: SSD
    Data_Storage: Multi-drive storage pool

  Operating_System:
    Name: TrueNAS SCALE
    Version: 25.10

  Network:
    VLAN: 2-INFRA
    IP: 10.1.2.2
    IP_Assignment: DHCP Reservation
    Connection: 802.3 (Ethernet)
    Device_Port: Switch 1 / Port 3

  Services:
    - SMB
    - NFS
    - Kopia Repository
    - Docker
    - Media Storage

  Notes:
    - Primary storage platform for the PTI.
    - Intended to remain an Infrastructure host.
    - Does not trunk multiple VLANs.
    - Access from other VLANs is controlled exclusively through routing and ACLs.
```

---

## Core Node (Fiji)

```yaml
CORE_NODE:
  Hostname: Fiji

  Role:
    - Primary Infrastructure Server
    - Container Host
    - Infrastructure Services
    - Future Core Platform

  Hardware:
    Manufacturer: HP
    Model: t655
    CPU: AMD Ryzen Embedded
    Memory: 32 GB

  Operating_System:
    Name: Ubuntu Server
    Version: 22.04.5 LTS

  Network:
    VLAN: 2-INFRA
    IP: 10.1.2.3
    IP_Assignment: DHCP Reservation
    Connection: 802.3 (Ethernet)
    Device_Port: Switch 1 / Port 5

  Planned_Services:
    - AdGuard Home
    - Unbound
    - CrowdSec
    - Step-CA
    - NetBird
    - Docker
    - Reverse Proxy
    - Infrastructure Services

  Notes:
    - Currently operates as an Infrastructure access-port host.
    - Single Ethernet interface.
    - No tagged VLANs.
    - Additional services will be exposed to other VLANs through router ACLs.
```

---

## Compute Node (KonTiki)

```yaml
COMPUTE_NODE:
  Hostname: KonTiki

  Role:
    - Local LLM Server
    - AI Inference
    - GPU Compute
    - Experimental AI Platform

  Hardware:
    Manufacturer: Apple
    Model: MacBook
    Status: Awaiting USB Ethernet Adapter

  Operating_System:
    Name: macOS

  Network:
    Planned_VLAN: 2-INFRA
    Planned_IP: DHCP Reservation
    Connection: Wired Ethernet
    Device_Port: Switch 1 / Port 4

  Planned_Services:
    - llama.cpp
    - Local LLMs
    - AI APIs
    - SovrIT Assistant backend

  Notes:
    - Permanent infrastructure appliance.
    - Not a personal workstation.
    - Intended to remain permanently installed.
    - Connected to AC power continuously.
    - Connected to KVM.
    - Expected to operate as an Infrastructure access-port host.
    - Multiple VLAN interfaces are not presently anticipated.
    - Client access from other VLANs will be governed through ACLs.
```

---

## Home Assistant Green

```yaml
HOME_ASSISTANT:
  Hostname: HomeAssistant

  Role:
    - Home Automation
    - Device Orchestration
    - Automation Engine

  Hardware:
    Manufacturer: Home Assistant
    Model: Home Assistant Green

  Operating_System:
    Name: Home Assistant OS

  Network:
    Current_VLAN: 2-INFRA
    Current_IP: DHCP Reservation
    Connection: Wired Ethernet
    Device_Port: Switch 1 / Port 6

  Planned_Expansion:
    Secondary_USB_Ethernet:
      VLAN: 66-IoT
      Purpose: Native communication with IoT devices

  Notes:
    - Initially deployed solely on the Infrastructure VLAN.
    - Planned dual-homed architecture:
        - Primary interface: Infrastructure management.
        - Secondary interface: Native IoT communication.
    - Intended to become the application-layer coordinator between Infrastructure and IoT.
```

---

## Development Node (Moorea)

```yaml
DEVELOPMENT_NODE:
  Hostname: Moorea

  Role:
    - Development
    - Testing
    - Temporary Infrastructure

  Hardware:
    Manufacturer: Dell Wyse
    Model: 5070

  Status:
    Current_State: Offline
    Reason: Hardware troubleshooting

  Notes:
    - Temporarily removed from service.
    - Formerly connected directly to the router.
```

---

## Administration Node (Tahiti)

```yaml
ADMINISTRATION_NODE:
  Hostname: Tahiti

  Role:
    - Primary Administration Workstation

  Hardware:
    Manufacturer: Lenovo

  Operating_System:
    Name: Linux Mint
    Version: 22

  Network:
    Wired:
      VLAN: 1-MGMT
      IP: 10.1.1.88
      Usage: Administrative tasks

    Wireless:
      Primary_SSID: TikiShack
      VLAN: 22
      Usage: Normal administration

  Notes:
    - Primary management workstation.
    - Most day-to-day network administration occurs wirelessly from VLAN 22.
    - Wired management remains available when required.
```

---

## Remote Failover Node

```yaml
REMOTE_FAILOVER_NODE:
  Status: Planned

  Purpose:
    - Disaster Recovery
    - Off-site Replication
    - Remote Infrastructure

  Notes:
    - Will eventually become an independent PTI node.
    - Detailed architecture documented separately.
```

---

# Network Configuration (Source of Truth)

This section represents the authoritative operational configuration of the
production network.

If the Omada Controller configuration and this document disagree,
this document is considered the intended configuration and should be
used when validating or rebuilding the network.

## Switch Port Configuration

```yaml
SWITCH1_PORT_CONFIGURATION:

  Port 1:
    Name: Gateway Trunk
    Connected_Device: ER707-M2 Gateway

    Port_Type: Trunk

    Native_VLAN:
      - MGMT (1)

    Tagged_VLANs:
      - INFRA (2)
      - TikiShack.Printer (3)
      - TikiShack (22)
      - TikiShack.Panama (23)
      - TikiShack.Guest (33)
      - TikiShack.Sentry (44)
      - TikiShack.Media (55)
      - TikiShack.IoT (66)
      - Dev-Sandbox (99)

    Notes:
      - Primary gateway uplink.
      - Carries every production VLAN.

  Port 2:
    Name: OC200 Controller
    Connected_Device: Omada OC200

    Port_Type: Access

    Native_VLAN:
      - MGMT (1)

    Tagged_VLANs: []

    Notes:
      - Dedicated management interface.
      - No tagged VLANs.

  Port 3:
    Name: BoraBora
    Connected_Device: Storage Node

    Port_Type: Access

    Native_VLAN:
      - INFRA (2)

    Tagged_VLANs: []

    Notes:
      - Storage server.
      - Inter-VLAN access is controlled through ACLs.

  Port 4:
    Name: KonTiki
    Connected_Device: Local AI Compute Node

    Port_Type: Access

    Native_VLAN:
      - INFRA (2)

    Tagged_VLANs: []

    Notes:
      - Reserved pending USB Ethernet adapter.
      - Intended to remain an Infrastructure host.

  Port 5:
    Name: Fiji
    Connected_Device: Core Infrastructure Node

    Port_Type: Access

    Native_VLAN:
      - INFRA (2)

    Tagged_VLANs: []

    Notes:
      - Ubuntu Server.
      - Single network interface.

  Port 6:
    Name: Home Assistant Green
    Connected_Device: Home Assistant

    Port_Type: Access

    Native_VLAN:
      - INFRA (2)

    Tagged_VLANs: []

    Notes:
      - Future secondary USB NIC will connect to VLAN 66.
      - Switch port remains an access port.

  Port 7:
    Name: AP1
    Connected_Device: TP-Link EAP720

    Port_Type: Trunk

    Native_VLAN:
      - MGMT (1)

    Tagged_VLANs:
      - TikiShack.Printer (3)
      - TikiShack (22)
      - TikiShack.Panama (23)
      - TikiShack.Guest (33)
      - TikiShack.Sentry (44)
      - TikiShack.Media (55)
      - TikiShack.IoT (66)
      - Dev-Sandbox (99)

    Notes:
      - Carries only VLANs that have wireless SSIDs.
      - Does NOT carry INFRA (2).

  Port 8:
    Name: AP2 (Reserved)
    Connected_Device: Future EAP720

    Port_Type: Trunk

    Native_VLAN:
      - MGMT (1)

    Tagged_VLANs:
      - TikiShack.Printer (3)
      - TikiShack (22)
      - TikiShack.Panama (23)
      - TikiShack.Guest (33)
      - TikiShack.Sentry (44)
      - TikiShack.Media (55)
      - TikiShack.IoT (66)
      - Dev-Sandbox (99)

    Notes:
      - Reserved for future deployment.
      - Mirrors Port 7 configuration.
```

---

## Wireless Networks

```yaml
WIRELESS_NETWORKS:

  TikiShack:

    VLAN: 22
    Purpose: Primary trusted wireless LAN

    Security:
      Authentication: WPA2/WPA3 Personal
      Guest_Network: Disabled

    Radios:
      - 2.4 GHz
      - 5 GHz

    Notes:
      - All client traffic exits through the Boston VPN.
      - Default wireless network for trusted users.

  TikiShack.Panama:

    VLAN: 23
    Purpose: Trusted wireless LAN (Direct Internet)

    Security:
      Authentication: WPA2/WPA3 Personal
      Guest_Network: Disabled

    Radios:
      - 2.4 GHz
      - 5 GHz

    Notes:
      - Traffic bypasses the VPN.
      - Used when public Internet geolocation is desired.

  TikiShack.Printer:

    VLAN: 3
    Purpose: Printer access

    Security:
      Authentication: WPA2/WPA3 Personal
      Guest_Network: Disabled

    Radios:
      - 2.4 GHz
      - 5 GHz

    Notes:
      - Connected only when printing is required.

  TikiShack.Guest:

    VLAN: 33
    Purpose: Visitor access

    Security:
      Authentication: WPA2/WPA3 Personal
      Guest_Network: Enabled

    Radios:
      - 2.4 GHz
      - 5 GHz

    Notes:
      - Internet-only guest access.
      - Wireless client isolation enabled.

  TS.Sentry:

    VLAN: 44
    Purpose: Cameras and security devices

    Security:
      Authentication: WPA2/WPA3 Personal
      Guest_Network: Enabled

    Radios:
      - 2.4 GHz
      - 5 GHz

    Notes:
      - Intended for surveillance equipment.
      - Wireless client isolation enabled.

  TS.Media:

    VLAN: 55
    Purpose: Entertainment devices

    Security:
      Authentication: WPA2/WPA3 Personal
      Guest_Network: Enabled

    Radios:
      - 2.4 GHz
      - 5 GHz

    Notes:
      - Intended for televisions, streaming devices,
        receivers and similar equipment.
      - Future AirPlay/mDNS policy will be enforced via ACLs.

  TS.IoT:

    VLAN: 66
    Purpose: Smart-home devices

    Security:
      Authentication: WPA2/WPA3 Personal
      Guest_Network: Disabled

    Radios:
      - 2.4 GHz

    Notes:
      - Optimized for IoT hardware.
      - Home Assistant will eventually have
        a dedicated interface on this VLAN.

  TS.Sandbox:

    VLAN: 99
    Purpose: Development and experimentation

    Security:
      Authentication: WPA2/WPA3 Personal
      Guest_Network: Disabled

    Radios:
      - 2.4 GHz
      - 5 GHz

    Notes:
      - Intended for temporary development devices.
      - Client isolation intentionally disabled.
```

## VLAN Device Inventory

```yaml
VLAN_DEVICE_INVENTORY:

  VLAN_1:

    Name: MGMT
    VLAN_ID: 1

    Purpose:
      - Network infrastructure management

    Devices:
      - ER707-M2 Gateway
      - T1500G Managed Switch
      - OC200 SDN Controller
      - EAP720-1
      - Tahiti (wired administration)

    Notes:
      - Reserved exclusively for management infrastructure.
      - No wireless SSIDs are assigned to this VLAN.
      - Will eventually be replaced by a dedicated management VLAN.
      - VLAN 1 will ultimately become an unused/default VLAN.

  VLAN_2:

    Name: INFRA
    VLAN_ID: 2

    Purpose:
      - Core PTI infrastructure

    Devices:
      - BoraBora
      - Fiji
      - Home Assistant Green
      - KonTiki (planned)

    Notes:
      - Infrastructure servers only.
      - No wireless clients.
      - No wireless SSID.
      - Devices use access ports only.
      - Communication from other VLANs is controlled exclusively through Gateway ACLs.

  VLAN_3:

    Name: Printer
    VLAN_ID: 3

    Purpose:
      - Network printers

    Devices:
      - Brother printer(s)
      - Future network printing devices

    Wireless:
      SSID: TikiShack.Printer

  VLAN_22:

    Name: TikiShack
    VLAN_ID: 22

    Purpose:
      - Trusted user devices
      - Primary user network

    VPN:
      Enabled
      Endpoint: Boston

    Typical_Devices:
      - Tahiti
      - iPad
      - Trusted laptops
      - Trusted mobile devices

    Wireless:
      SSID: TikiShack

  VLAN_23:

    Name: Panama
    VLAN_ID: 23

    Purpose:
      - Trusted user devices
      - Direct Internet access

    VPN:
      Disabled

    Typical_Devices:
      - Tahiti
      - iPad
      - Trusted laptops
      - Trusted mobile devices

    Wireless:
      SSID: TikiShack.Panama

    Notes:
      - Functionally identical to VLAN 22 except that traffic
        bypasses the VPN.

  VLAN_33:

    Name: Guest
    VLAN_ID: 33

    Purpose:
      - Visitor Internet access

    Wireless:
      SSID: TikiShack.Guest

    Notes:
      - Internet access only.
      - No access to trusted internal resources.

  VLAN_44:

    Name: Sentry
    VLAN_ID: 44

    Purpose:
      - Cameras
      - Surveillance
      - Physical security

    Wireless:
      SSID: TS.Sentry

    Planned_Devices:
      - Reolink cameras
      - Doorbell
      - Security appliances

  VLAN_55:

    Name: Media
    VLAN_ID: 55

    Purpose:
      - Entertainment
      - Streaming
      - Audio/Video

    Wireless:
      SSID: TS.Media

    Planned_Devices:
      - Televisions
      - Streaming devices
      - AV receivers
      - Media appliances

  VLAN_66:

    Name: IoT
    VLAN_ID: 66

    Purpose:
      - Smart-home devices

    Wireless:
      SSID: TS.IoT

    Planned_Devices:
      - Smart plugs
      - Smart switches
      - Sensors
      - ESPHome devices
      - Shelly devices
      - WLED devices

    Planned_Infrastructure:
      - Home Assistant secondary interface

  VLAN_99:

    Name: Dev-Sandbox
    VLAN_ID: 99

    Purpose:
      - Development
      - Testing
      - Temporary experimentation

    Wireless:
      SSID: TS.Sandbox

    Typical_Devices:
      - Development laptops
      - Test hardware
      - Temporary virtual machines
```

---

# Network Design Decisions

The following design decisions have been intentionally adopted and represent the
authoritative architectural direction for the SovrIT PTI network.

## Core Principles

- Strong VLAN segmentation.
- Least-privilege networking.
- Zero-trust philosophy.
- Infrastructure first.
- Wired-first infrastructure.
- Explicit routing.
- Explicit firewall policy.
- Principle of least privilege.
- Observation over inference.
- Security before convenience.

---

## Layer 2 Design

### Infrastructure Hosts

- Infrastructure hosts use **access ports** unless multiple VLAN interfaces are
  explicitly required.
- End hosts are **not trunk ports**.
- VLAN membership is configured on individual switch ports.
- Omada Port Profiles are used only for physical port characteristics
  (PoE, bandwidth, flow control, etc.), **not** VLAN membership.

---

### Trunk Ports

Only links that genuinely transport multiple VLANs are configured as trunks.

Current trunk ports:

- Switch Port 1 (Gateway)
- Switch Port 7 (Primary AP)
- Switch Port 8 (Reserved AP)

No other switch ports currently require trunking.

---

### Access Ports

Current Infrastructure access ports:

- Port 2 — OC200
- Port 3 — BoraBora
- Port 4 — KonTiki
- Port 5 — Fiji
- Port 6 — Home Assistant

Each carries exactly one production VLAN.

---

## Access Point Design

Wireless access points carry only VLANs that actually have wireless SSIDs.

Specifically:

- Management VLAN remains native.
- Infrastructure VLAN is **not** carried.
- Additional VLANs are added only when an SSID requires them.

This follows the design principle:

> A VLAN should exist on a link only when there is a legitimate operational
> reason for traffic on that VLAN to traverse that link.

---

## Infrastructure Philosophy

Infrastructure systems are intentionally separated from user networks.

Infrastructure hosts are expected to provide services to other VLANs through
routed connections rather than by directly participating in multiple VLANs.

Examples include:

- BoraBora
- Fiji
- KonTiki
- Home Assistant

Infrastructure remains physically wired whenever possible.

---

## Wireless Philosophy

Wireless exists for clients.

Infrastructure does not use Wi-Fi.

Every SSID maps to exactly one VLAN.

Every wireless client receives network access appropriate only to the role of
that SSID.

Wireless is never used as a shortcut around VLAN segmentation.

## Security Architecture

The PTI network is designed around a layered security model. VLANs provide
administrative and broadcast-domain separation, while Gateway ACLs enforce
explicit communication policy between security domains.

### Layer 2

Responsibilities:

- Physical connectivity
- VLAN separation
- Broadcast containment
- Access vs. trunk enforcement

Layer 2 is **not** relied upon for authorization decisions.

---

### Layer 3

The ER707-M2 is the authoritative policy enforcement point.

Responsibilities include:

- Inter-VLAN routing
- Gateway ACL enforcement
- Internet access
- VPN routing
- Future QoS

Inter-VLAN communication is denied unless explicitly permitted.

---

### Infrastructure

Infrastructure systems are treated as service providers rather than trusted peers.

Services are exposed intentionally.

Infrastructure hosts do not automatically trust one another simply because they
reside on the same VLAN.

Future Gateway ACLs may further restrict east-west communication within
Infrastructure where operationally appropriate.

---

### Management

Administrative access is intentionally limited.

Current administration devices:

- Tahiti
- iPad

Normal administration occurs over the trusted TikiShack wireless network
(VLAN 22).

Temporary wired administration remains available through VLAN 1 when required.

A dedicated Management VLAN will eventually replace VLAN 1.

---

### Internet Access

Every VLAN receives Internet access only as required by its purpose.

Future Gateway ACLs will further restrict Internet access where practical.

Examples:

- Cameras should communicate only with required cloud services (if any).
- Infrastructure should not require unrestricted outbound access.
- Guest devices receive Internet access only.
- Sandbox remains intentionally flexible.

---

### VPN Philosophy

Two trusted client VLANs exist.

#### VLAN 22 — TikiShack

Purpose:

- Daily-use trusted devices.

Characteristics:

- Permanent VPN connection.
- Public Internet appears to originate from Boston.
- Default trusted wireless network.

---

#### VLAN 23 — Panama

Purpose:

- Trusted devices requiring direct Internet access.

Characteristics:

- No VPN.
- Native WAN routing.
- Used when geographic location should reflect the actual Internet connection.

The two VLANs are otherwise functionally equivalent.

---

### Service Discovery

Discovery protocols are intentionally treated as privileged services.

Examples include:

- mDNS
- Bonjour
- SSDP
- Matter
- Thread Border Router discovery

These services will not be permitted between VLANs unless explicitly required.

Future mDNS reflection will be implemented only where operationally justified.

---

### Home Assistant

Home Assistant occupies a unique architectural role.

Rather than acting as a network bridge, it functions as an application-layer
orchestrator between trusted infrastructure and IoT devices.

Planned architecture:

Primary interface:

- Infrastructure VLAN (2)

Purpose:

- Administration
- Backups
- Updates
- Infrastructure services

Secondary interface:

- IoT VLAN (66)

Purpose:

- Native IoT communication
- ESPHome
- Matter
- Device discovery
- Local automation

This design minimizes firewall exceptions while maintaining strong VLAN
segmentation.

---

### Artificial Intelligence Platform

KonTiki is designed as a permanent infrastructure appliance.

Purpose:

- Local LLM inference
- AI APIs
- Future SovrIT Assistant backend

KonTiki is **not** intended to function as a personal workstation.

Current design assumes:

- Single Infrastructure interface
- Access-port configuration
- Client access governed through Gateway ACLs

No multiple VLAN interfaces are currently anticipated.

---

### Storage Philosophy

BoraBora functions as centralized storage for the PTI.

Responsibilities include:

- Media storage
- Infrastructure storage
- Backup repository
- Migration support

BoraBora remains an Infrastructure host.

Other VLANs consume storage services through routed connections rather than by
placing the storage server directly on multiple VLANs.

---

# Future Architecture

The following items are planned but not yet implemented.

## ACL Policy

Status:

- Planned

Design goals:

- Default deny between VLANs.
- Explicit allow rules.
- Least privilege.
- Minimize lateral movement.
- Document every exception.

---

## Firewall Rules

Status:

- Planned

Firewall policy will be derived directly from documented communication
requirements rather than convenience.

---

## IPv6

Status:

- Deferred

IPv6 deployment will occur only after the IPv4 architecture is considered
complete and stable.

---

## High Availability

Status:

- Planned

Future work includes:

- Remote Failover Node
- Replication
- Automated recovery
- Disaster recovery procedures

---

## Additional Infrastructure

Planned future services include, but are not limited to:

- Step-CA
- CrowdSec
- NetBird
- AdGuard Home
- Unbound
- Reverse Proxy
- Centralized authentication
- Monitoring
- Logging
- Certificate management

# Network Design Status

| Area | Status | Notes |
|:-----|:------:|:------|
| Physical Infrastructure | ✅ Complete | Router, switch, controller, and primary AP operational. |
| VLAN Architecture | ✅ Complete | Production VLAN design finalized. |
| IP Addressing | ✅ Complete | Static infrastructure addresses and DHCP reservations established. |
| Switch Port Configuration | ✅ Complete | All production ports configured and documented. |
| Wireless SSIDs | ✅ Complete | Every SSID mapped to a dedicated VLAN. |
| Access Point Trunking | ✅ Complete | Only wireless VLANs transported; INFRA intentionally excluded. |
| Infrastructure Topology | ✅ Complete | Wired-first architecture established. |
| Layer 2 Design | ✅ Complete | Considered stable. |
| Gateway Routing | ✅ Complete | Inter-VLAN routing operational. |
| ACL Policy | ⏳ Planned | Next implementation phase. |
| Firewall Rules | ⏳ Planned | Derived from documented communication requirements. |
| mDNS / Bonjour Policy | ⏳ Planned | To be implemented selectively where required. |
| VPN Policy | ⏳ Planned | Fine-grained routing policy to follow ACL implementation. |
| Home Assistant Integration | ⏳ In Progress | Initial deployment on INFRA. Dual-homed design planned. |
| IoT Deployment | ⏳ In Progress | Devices will be migrated following ACL implementation. |
| AI Platform | ⏳ In Progress | KonTiki deployment pending wired Ethernet adapter. |
| Remote Failover | 📋 Planned | Future RFN deployment. |
| High Availability | 📋 Planned | Replication and disaster recovery. |
| IPv6 | 📋 Deferred | To be implemented after IPv4 architecture stabilizes. |

---

# Guiding Principles

The following principles govern all future modifications to the TikiShack network.

## Simplicity

Prefer the simplest architecture that satisfies the operational requirements.

Complexity must always be justified.

---

## Determinism

Network behavior should be predictable.

Routing, firewall policy, VLAN membership, and infrastructure responsibilities
should never depend on undocumented behavior or assumptions.

---

## Least Privilege

Every communication path must be explicitly justified.

Access is granted only when operationally necessary.

---

## Separation of Responsibilities

Each infrastructure component has a clearly defined responsibility.

Examples include:

- Gateway
    - Routing
    - Firewall
    - VPN

- Switch
    - Layer-2 forwarding
    - VLAN transport
    - PoE

- Access Points
    - Wireless access
    - Client association
    - SSID-to-VLAN mapping

- Infrastructure Nodes
    - Application services

Responsibilities should not overlap unnecessarily.

---

## Wired-First Infrastructure

Infrastructure services should use wired Ethernet whenever practical.

Wireless networking exists primarily for client devices.

---

## Explicit Over Implicit

Configuration should always be explicit.

Examples:

- Explicit VLAN membership
- Explicit ACL rules
- Explicit routing
- Explicit documentation

Automatic or implicit behavior should not be relied upon where an explicit
configuration is available.

---

## Documentation First

The documentation is the authoritative design specification.

Configuration changes should be reflected in this document as soon as reasonably
practical.

Whenever practical:

1. Update documentation.
2. Commit documentation to Git.
3. Export the Omada configuration.
4. Apply the network change.

This ensures the documented architecture and operational network remain aligned.

---

## Change Management

Major architectural changes should:

- be documented,
- be committed to version control,
- include an Omada configuration backup,
- and be reversible.

---

## Current Baseline

This document represents the **Layer-2 baseline** for the SovrIT PTI network.

Future work will build upon this baseline through:

- Gateway ACL implementation
- Firewall policy
- Service discovery policy
- Home Assistant integration
- IoT deployment
- AI platform deployment
- Remote failover implementation

Layer-2 topology should be considered stable unless a future architectural
decision explicitly requires modification.

---

# Revision History

| Version | Date | Description |
|:--------|:-----|:------------|
| 0.x | Initial development | Early network planning and hardware inventory. |
| 1.0 | Current | Layer-2 architecture completed; documented production baseline established prior to ACL implementation. |

---

# End of Document