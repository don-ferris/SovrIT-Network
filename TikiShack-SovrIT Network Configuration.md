TikiShack-SovrIT Network Configuration
# ==============================================================================
#                  NETWORK  MANAGEMENT  DEVICES  (MGMT  VLAN  1)
# ==============================================================================

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~    ROUTER / GATEWAY    ~~~~~~~~~~~~~~~~~~~~~~~~~~~
ROUTER_GATEWAY:
    Make: TP-Link
    Model: ER707-M2
    Revision: v1.3
    FirmwareVersion: 1.4.2 Build 20260509 Rel.32107
    Network:
        VLAN: 1-MGMT
        IP: 10.1.1.1
        IP_Assignment: static
        MAC_ID: AC-A7-F1-D6-57-A6
        Connection: 802.3 (Ethernet/wired)
        Cable Color: White (Flat)
        PortMap:
            Port 1: ISP modem
            Port 2: UNUSED
            Port 3: UNUSED
            Port 4: Moorea (Wyse 5070 - Dev/Test Node)
            Port 5: Tahiti (Admin laptop) - 10.1.1.88 (static)
            Port 6: Switch 1 (T1500G)
            Port 7: UNUSED
        SSID: N/A

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~    SWITCH 1    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SWITCH1:
    Make: TP-Link
    Model: T1500G-10PS (TL-SG2210P)
    Revision: v2.8
    FirmwareVersion: 2.0.6 Build 20200805 Rel.57865
    Notes: "PoE - powers EAP-720 WAP and OC200 controller"
    Network:
        VLAN: 1-MGMT
        IP: 10.1.1.2
        IP_Assignment: static
        MAC_ID: 68-FF-7B-F9-94-2D
        Connection: 802.3 (Ethernet/wired)
        PortMap:
            Port 1: Router, Port 6
            Port 2: OC200 SDN Controller
            Port 3: BoraBora (NUC7i5BNH)
            Port 4: UNUSED - Reserved for KonTiki (MacBook)
            Port 5: Fiji (HP t655)
            Port 6: Home Assistant
            Port 7: EAP720-1 (10.1.1.8 - static)
            Port 8: UNUSED - Reserved for EAP720-2
        SSID: N/A

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~    SWITCH 2    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SWITCH2:
    Make: TP-Link
    Model: TL-SG1024DE
    Notes: "For future installation"
    Network:
        VLAN: 1-MGMT
        Connection: none
        SSID: N/A

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~    SWITCH  3    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SWITCH3:
    Make: Reolink
    Model: RLA-PS1
    Notes: "For future installation - for Reolink PoE security cameras & doorbell"
    Network:
        Connection: none
        SSID: N/A

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~    SDN  CONTROLLER    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SDN_CONTROLLER:
    Make: TP-Link
    Model: OC200
    Revision: v1.0
    FirmwareVersion: 1.40.18 Build 20260506 Rel.74003
    Notes: "replaces Docker-based app on BoraBora"
    Network:
        VLAN: 1-MGMT
        IP: 10.1.1.5
        IP_Assignment: static
        MAC_ID: B0-BE-76-C6-3A-FB
        Connection: 802.3 (Ethernet/wired)
        Cable Color: Blue
        Device/Port: Switch 1, port 2
        SSID: N/A

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~    WAP  1    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
WAP1:
    Make: TP-Link
    Model: EAP720(US)
    Revision: v1.0
    FirmwareVersion: 1.1.4 Build 20251224 Rel. 62114
    Network:
        VLAN: 1-MGMT
        IP: 10.1.1.8
        IP_Assignment: static
        MAC_ID: 10-5A-95-2F-66-B1
        Cable Color: Yellow
        Device/Port: Switch 1, port 7
        SSID: N/A

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~    WAP  2    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
WAP2:
    Make: TP-Link
    Model: EAP720(US)
    Notes: "for future installation"
    Network:
        SSID: N/A



# ==============================================================================
#                           SERVERS  (INFRA  VLAN  2)
# ==============================================================================
#    Use 'lshw' or 'dmidecode' to get CPU/RAM info and 'lsblk -o NAME,MODEL,SERIAL,SIZE' to get disk info

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~    STORAGE  NODE    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
STORAGE_NODE:
    Hostname: BoraBora
    Descriptors:
        Make: Intel
        Model: NUC7i5BNH
        Version: J31144-304
        Serial Number: GEBN72100WLF
    Specs:
        CPU: Intel(R) Core(TM) i5-7260U CPU @ 2.20GHz
        RAM:
            Bank 0: 8 GB SODIMM, Samsung M471A1K43EB1-CWE - DDR4-3200 (Configured for 2133)
            Bank 2: 8 GB SODIMM, Samsung M471A1K43EB1-CWE - DDR4-3200 (Configured for 2133)
        Storage:
            sda: Model=CT240BX500SSD1; Serial=2522E9C21E61; Size=223.6G
            sdb: Model=HUH721212ALE601; Serial=8HKL52EH; Size=10.9T
            sdc: Model=OOS12000G; Serial=000DGE3L; Size=10.9T
            sdd: Model=OOS12000G; Serial=000EXT6K; Size=10.9T
            sde: Model=HUH721212ALE601; Serial=AAGAX3YH; Size=10.9T
            sdf: Model=OOS12000G; Serial=000C721D; Size=10.9T
            sdg: Model=HUH721212ALE601; Serial=AAGHB49H; Size=10.9T
            nvme0n1: Model=KINGSTON SNV2S250G; Serial=50026B768602649A; Size=232.9G
        OS: TrueNAS Scale/Community Edition - 25.10.3.1 - Goldeye
        BIOS: "Version: BNKBL357.86A.0066.2018.0724.1832 ; Revision: 5.6 ; Release Date: 07/24/2018"
    Network:
        VLAN: 2-INFRA
        IP: 10.1.2.2
        IP_Assignment: static
        MAC_ID: 94:C6:91:10:B8:A5
        Connection: 802.3 (Ethernet/wired)
        Cable Color: White+Red
        Device/Port: Switch 1, port 3
        SSID: N/A
    Notes: |
        8-bay disk array connected via USB3 
        (8 x 12TB drives, 6 drives in RAIDZ2 array, 2 hot spare drives)
        Runs Jellyfin in a (standalone/non-TrueNAS) Docker container

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~     CORE  NODE    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
CORE_NODE:
    Hostname: Fiji
    Make: HP
    Model: t655
    Version: 
    Serial Number: MXL4233KJ4
    Specs:
        CPU: AMD Ryzen Embedded R2314 with Radeon Graphics @3500 MHz
        RAM:
            Total: 12 GB
            Bank 0: 
            Bank 2: 
        Storage:
            sda: Model=; Serial=; Size=
            nvme0n1: Model=; Serial=; Size=
        OS: 
        BIOS: 
    Network:
        VLAN: 2-INFRA
        IP: 10.1.2.3
        IP_Assignment: static
        MAC_ID: 7C:57:58:FC:2F:CA
        Connection: 802.3 (Ethernet/wired)
        Cable Color: Blue+Red
        Device/Port: Switch 1, port 5
        SSID: N/A
    Notes: |
        SovrIT PTI Core Node

# ~~~~~~~~~~~~~~~~~~~~~~~~~~    COMPUTE  (AI)  NODE    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
COMPUTE_NODE:
    Hostname: KonTiki
    Make: Apple
    Model: MacBook Pro (A2442)
    Version: EMC 3650
    Serial Number: W6HGTQ66QX
    Specs:
        CPU: Apple M1 Pro (8-Core CPU + 14-Core GPU)
        RAM:
            Total: 16 GB unified memory
            Bank 0: 
            Bank 2: 
        Storage:
            sda: Model=; Serial=; Size=
            nvme0n1: Model=; Serial=; Size=
        OS: 
        BIOS: 
    Network:
        VLAN: 2-INFRA
        IP: 10.1.2.4
        IP_Assignment: static
        MAC_ID: 
        Connection: 802.3 (Ethernet/wired)
        Cable Color: Blue+White
        Device/Port: Switch 1, port 4
        SSID: N/A

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~     ORCHESTRATION  NODE    ~~~~~~~~~~~~~~~~~~~~~~~~~~~
ORCHESTRATION_NODE:
    Hostname: HomeAssistant
    Make: Nabu Casa
    Model: NC-GREEN-1175 (Home Assistant Green)
    Version: 
    Serial Number: 113110024250903452
    Specs: 
        CPU: Rockchip RK3566 (Quad-core ARM Cortex-A55 @ 1.8 GHz) 
        RAM: 4 GB LPDDR4X
        Storage: 32 GB eMMC
        OS: Home Assistant OS
    Network:
        VLAN: 2-INFRA
        IP: 10.1.2.22
        IP_Assignment: static
        MAC_ID: 
        Connection: 802.3 (Ethernet/wired)
        Cable Color: Blue+Black
        Device/Port: Switch 1, port 6
        SSID: N/A
    Notes: |
        Provides a variety of smarthome and other orchestration services
        
# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~    DEV  NODE    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
DEV_NODE:
    Hostname: Moorea
    Make: Dell
    Model: Wyse 5070 Extended
    Version: 
    Serial Number: 15118026590
    Specs: 
        CPU: Apple M1 Pro (8-Core CPU + 14-Core GPU)
        RAM:
            Total:  GB
            Bank 0: 
            Bank 2: 
        Storage:
            sda: Model=; Serial=; Size=
            nvme0n1: Model=; Serial=; Size=
        OS: 
        BIOS: 
    Network:
        VLAN: 2-INFRA
        IP: 10.1.2.x
        IP_Assignment: static
        Device/Port: Router, port 4

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~    ADMIN  NODE    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ADMIN NODE:
    Hostname: Tahiti
    Make: Dell
    Model: Latitude 7290
    Version:
    Serial Number:
        Storage:
            sda: Model=; Serial=; Size=
            nvme0n1: Model=; Serial=; Size=
        OS: 
        BIOS: 
    Network:
        VLAN: 1-MGMT
        IP: 10.1.1.88
        IP_Assignment: static
        Device/Port: Router, port 5

# ~~~~~~~~~~~~~~~~~~~~~~~~    REMOTE  FAILOVER  NODE    ~~~~~~~~~~~~~~~~~~~~~~~~
REMOTE_FAILOVER_NODE:
    Hostname: RemoteFailover
    Notes: Placeholder for future configuration

# ~~~~~~~~~~~~~~~~~~~~~~~~~~    REMOTE  TEST  NODE    ~~~~~~~~~~~~~~~~~~~~~~~~~~
# REMOTE TEST NODE
TestNode:
    Hostname: RemoteTest
    Notes: Placeholder for future configuration


# ==============================================================================
#                    NETWORK  CONFIGURATION  (SOURCE  OF  TRUTH)
# ==============================================================================


# ~~~~~~~~~~~~~~~~~~~~~    SWITCH  PORT  CONFIGURATION    ~~~~~~~~~~~~~~~~~~~~~~~~~~

SWITCH1_PORT_CONFIGURATION:

    Port 1:
        Name: Gateway Trunk
        Type: Trunk
        Connected Device: ER707-M2 Gateway
        Native VLAN: MGMT (1)
        Tagged VLANs:
            INFRA (2)
            TikiShack.Printer (3)
            TikiShack (22)
            TikiShack.Panama (23)
            TikiShack.Guest (33)
            TikiShack.Sentry (44)
            TikiShack.Media (55)
            TikiShack.IoT (66)
            Dev-Sandbox (99)

    Port 2:
        Name: OC200 Controller
        Type: Access
        Connected Device: OC200
        Native VLAN: MGMT (1)
        Tagged VLANs:
            None

    Port 3:
        Name: BoraBora
        Type: Access
        Connected Device: BoraBora Storage Node
        Native VLAN: INFRA (2)
        Tagged VLANs:
            None

    Port 4:
        Name: KonTiki
        Type: Access
        Connected Device: KonTiki (future)
        Native VLAN: INFRA (2)
        Tagged VLANs:
            None

    Port 5:
        Name: Fiji
        Type: Access
        Connected Device: Fiji Core Node
        Native VLAN: INFRA (2)
        Tagged VLANs:
            None

    Port 6:
        Name: Home Assistant
        Type: Access
        Connected Device: Home Assistant Green
        Native VLAN: INFRA (2)
        Tagged VLANs:
            None

    Port 7:
        Name: AP1
        Type: Trunk
        Connected Device: EAP720-1
        Native VLAN: MGMT (1)
        Tagged VLANs:
            TikiShack.Printer (3)
            TikiShack (22)
            TikiShack.Panama (23)
            TikiShack.Guest (33)
            TikiShack.Sentry (44)
            TikiShack.Media (55)
            TikiShack.IoT (66)
            Dev-Sandbox (99)

    Port 8:
        Name: AP2 (Reserved)
        Type: Trunk
        Connected Device: Reserved
        Native VLAN: MGMT (1)
        Tagged VLANs:
            TikiShack.Printer (3)
            TikiShack (22)
            TikiShack.Panama (23)
            TikiShack.Guest (33)
            TikiShack.Sentry (44)
            TikiShack.Media (55)
            TikiShack.IoT (66)
            Dev-Sandbox (99)


# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~    SSID  MAPPINGS    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

WIRELESS_NETWORKS:

    TikiShack:
        VLAN: 22
        Guest Network: Disabled
        Bands: 2.4 GHz, 5 GHz
        Purpose: Primary WLAN (VPN-protected)

    TikiShack.Panama:
        VLAN: 23
        Guest Network: Disabled
        Bands: 2.4 GHz, 5 GHz
        Purpose: Primary WLAN (direct Internet / no VPN)

    TikiShack.Printer:
        VLAN: 3
        Guest Network: Disabled
        Bands: 2.4 GHz, 5 GHz
        Purpose: Temporary access to printers

    TikiShack.Guest:
        VLAN: 33
        Guest Network: Enabled
        Bands: 2.4 GHz, 5 GHz
        Purpose: Internet-only guest access

    TS.Sentry:
        VLAN: 44
        Guest Network: Enabled
        Bands: 2.4 GHz, 5 GHz
        Purpose: Security cameras and surveillance devices

    TS.Media:
        VLAN: 55
        Guest Network: Enabled
        Bands: 2.4 GHz, 5 GHz
        Purpose: Streaming and entertainment devices

    TS.IoT:
        VLAN: 66
        Guest Network: Disabled
        Bands: 2.4 GHz only
        Purpose: Smart-home and IoT devices

    TS.Sandbox:
        VLAN: 99
        Guest Network: Disabled
        Bands: 2.4 GHz, 5 GHz
        Purpose: Development and experimentation


# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~    VLAN  DEVICE  INVENTORY    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~

VLAN_DEVICE_INVENTORY:

    VLAN 1 (MGMT):
        ER707-M2 Gateway
        T1500G Switch
        OC200 Controller
        EAP720-1
        Tahiti (wired administration)

    VLAN 2 (INFRA):
        BoraBora
        Fiji
        Home Assistant Green
        KonTiki (future)

    VLAN 3 (Printer):
        Network printers

    VLAN 22 (TikiShack):
        Trusted user devices
        iPad
        Tahiti (wireless)

    VLAN 23 (Panama):
        Trusted user devices requiring direct Internet access

    VLAN 33 (Guest):
        Visitors

    VLAN 44 (Sentry):
        Cameras
        Security devices

    VLAN 55 (Media):
        Televisions
        Streaming devices
        Audio equipment

    VLAN 66 (IoT):
        Smart-home devices

    VLAN 99 (Dev-Sandbox):
        Development systems
        Experimental devices


# ==============================================================================
#                             NETWORK  DESIGN  DECISIONS
# ==============================================================================

NETWORK_DESIGN_DECISIONS:

    Layer 2:
        • Infrastructure hosts use access ports unless multiple VLAN interfaces are actually required.
        • Trunk ports are limited to links that must transport multiple VLANs.
        • VLAN membership is configured on individual switch ports (not Port Profiles).

    Port 1:
        • Gateway uplink carries all production VLANs.

    Port 2:
        • OC200 remains a pure MGMT access port.
        • No tagged VLANs.

    Ports 3-6:
        • All infrastructure servers operate as access ports on VLAN 2.
        • Inter-VLAN communication is provided through routing and ACLs, not by trunking hosts.

    Port 7:
        • Carries only VLANs that have wireless SSIDs.
        • Does NOT carry INFRA (VLAN 2).
        • Native VLAN remains MGMT (1) for AP management.

    Wireless:
        • Every SSID maps to exactly one VLAN.
        • IoT uses 2.4 GHz only.
        • Primary management over Wi-Fi occurs from Donnie's iPad Pro (10.1.22.62) or Tahiti (Admin laptop - 10.1.22.88) on VLAN 22.

    Home Assistant:
        • Initially deployed as an INFRA host.
        • Planned future dual-homing:
            - Primary interface: VLAN 2 (INFRA)
            - Secondary interface: VLAN 66 (IoT)

    Security:
        • Layer-2 design completed before ACL implementation.
        • Inter-VLAN communication will be controlled exclusively through router ACLs.
        • IoT device isolation will be implemented using ACLs rather than Omada Guest Network behavior.

    Future:
        • Dedicated MGMT VLAN will eventually replace VLAN 1.
        • VLAN 1 will ultimately become an unused/default VLAN.


# ==============================================================================
#                             NETWORK  DESIGN  STATUS
# ==============================================================================

NETWORK_DESIGN_STATUS:

    Layer 1 (Physical):
        COMPLETE

    Layer 2 (VLANs):
        COMPLETE

    Layer 3 (Routing):
        COMPLETE

    ACL Policy:
        NOT STARTED

    VPN Policy:
        NOT STARTED

    Firewall Rules:
        NOT STARTED

    mDNS / Service Discovery:
        NOT STARTED

    IPv6:
        DEFERRED

    High Availability:
        PLANNED








# ==============================================================================
#                             DISREGARD BELOW HERE
# ==============================================================================

# ~~~~~~~~~~~~~~~~~~~~~~~~~~~~    SERVER / MACHINE / NODE TEMPLATE      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
TEMPLATE_NODE:
    Hostname: Template
    Descriptors:
        Make: 
        Model: 
        Version: 
        Serial Number: 
    Specs:
        CPU: 
        RAM:
            Total: 
            Bank 0: 
            Bank 1: 
        Storage:
            sda: Model=
            nvme0n1: 
        OS: 
        BIOS: 
    Network:
        VLAN: 
        IP: 10.1.x.x
        IP_Assignment: static
        MAC_ID: 
        Connection: 802.3 (Ethernet/wired)
        Device/Port: Switch 1, port 
        SSID: N/A
    Notes: |
        Used for 
        SSH Port: 

