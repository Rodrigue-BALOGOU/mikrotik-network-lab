MikroTik Network Lab

[Project](https://img.shields.io/badge/Project-MikroTik%20Network%20Lab-blue)
[Environment](https://img.shields.io/badge/Environment-Virtualized%20Network%20Lab-darkgreen)
[Router](https://img.shields.io/badge/Router-MikroTik%20RouterOS-red)
[Virtualization](https://img.shields.io/badge/Virtualization-VMware-blue)
[Networking](https://img.shields.io/badge/Domain-Networking-blue)
[DHCP](https://img.shields.io/badge/Service-DHCP-orange)
[HotSpot](https://img.shields.io/badge/Service-HotSpot-purple)
[Firewall](https://img.shields.io/badge/Security-Firewall-red)
[NAT](https://img.shields.io/badge/Network-NAT-yellow)
[Status](https://img.shields.io/badge/Status-Completed-success)


Practical network laboratory focused on configuring and administering MikroTik RouterOS in a virtualized VMware environment.

Practical network laboratory focused on configuring and administering MikroTik RouterOS in a virtualized VMware environment.

A practical networking laboratory built with MikroTik RouterOS and VMware Workstation to develop hands-on skills in network administration, configuration and security.

The lab reproduces a small network environment with separated management, WAN and client networks, allowing network services and access-control mechanisms to be configured and tested in a controlled environment.

---

About the Lab

This project focuses on the practical configuration and troubleshooting of a MikroTik router in a virtualized environment.

The laboratory includes:

- A dedicated management network
- A WAN connection through VMware NAT
- A bridged LAN network
- DHCP services
- MikroTik HotSpot and captive portal
- NAT configuration
- Firewall rules
- Network connectivity and service testing

The objective is not only to configure the router, but also to understand how the different network components interact and to validate their behavior through practical tests.

---

Network Architecture

The current lab architecture is organized around three main network zones:

                         INTERNET
                            │
                        VMware NAT
                            │
                         ether1
                            │
                    ┌──────────────┐
                    │   MikroTik   │
                    └──────────────┘
                       │          │
                    ether2    bridge-lan
                       │          │
                  Management    /       \
                              ether3   ether4
                                │        │
                                └──┬─────┘
                                   │
                            10.10.20.0/24
                                   │
                              DHCP / HotSpot
                                   │
                                Clients

Network addressing

Network| Purpose| Interface
"192.168.162.0/24"| Management| "ether2"
WAN| Internet access| "ether1"
"10.10.20.0/24"| LAN / HotSpot| "bridge-lan"
—| LAN physical ports| "ether3", "ether4"

The LAN IP "10.10.20.1/24" is configured on "bridge-lan", which groups "ether3" and "ether4" into a single logical network.

---

Technical Skills Demonstrated

This laboratory demonstrates practical experience with:

Network Administration

- MikroTik RouterOS configuration
- Interface configuration
- IP addressing and subnetting
- Bridge configuration
- Network segmentation
- WAN configuration
- Routing and default gateway
- Connectivity testing and troubleshooting

Network Services

- DHCP server configuration
- DHCP address pools
- DHCP leases
- DNS configuration
- HotSpot deployment
- Captive portal
- User authentication

Network Security

- Dedicated management network
- Separation of management and client traffic
- NAT configuration
- Firewall rule configuration
- Access control concepts
- Basic network security testing

Virtualization

- VMware Workstation
- VMware Host-Only networking
- VMware NAT networking
- Virtual network interface mapping
- Testing network behavior in an isolated laboratory

---

Configuration & Documentation

The complete technical configuration is documented step by step.

The documentation explains not only the commands used, but also the purpose of each configuration, the expected behavior and the verification process.

Detailed configuration

"→ View the complete configuration" (configuration.md)

The documentation currently covers:

1. Initial laboratory setup
2. Management network configuration
3. WAN configuration
4. LAN and bridge configuration
5. DHCP configuration
6. MikroTik HotSpot configuration
7. Connectivity and service verification

Each section includes configuration commands, network diagrams and verification procedures.

---

Screenshots & Practical Evidence

The repository also contains screenshots captured during the configuration and testing phases.

These screenshots provide visual evidence of the actual laboratory implementation, including:

- IP address configuration
- Interface configuration
- Bridge configuration
- DHCP configuration
- DHCP address pools and leases
- WAN connectivity
- Routing information
- HotSpot configuration
- Captive portal
- Authenticated HotSpot sessions

The screenshots are integrated directly into the technical documentation to make the configuration easier to understand and verify.

screenshots/
├── management/
├── wan/
├── lan/
└── hotspot/

The goal is to make the project reproducible and transparent: a reviewer can follow the documentation and see the corresponding configuration evidence.

---

Repository Structure

mikrotik-network-lab/
│
├── README.md
│
├── configuration.md
│
└── screenshots/
    ├── management/
    ├── wan/
    ├── lan/
    └── hotspot/

---

What This Project Demonstrates

This project reflects a hands-on approach to network administration rather than a purely theoretical understanding of networking concepts.

It demonstrates the ability to:

- design a small network architecture;
- configure a MikroTik router;
- assign and manage IP addressing;
- create and use bridges;
- deploy DHCP services;
- configure a WAN connection;
- provide controlled client access through HotSpot;
- configure basic network security mechanisms;
- troubleshoot connectivity issues;
- validate configurations through practical tests;
- document technical work clearly.

---

Project Status

In progress — laboratory under continuous exploration and improvement.

The current configuration is being progressively documented and tested. New configurations and experiments may be added as the laboratory evolves.

---

Author

Rodrigue BALOGOU

Student in Computer Systems and Network Security

Promoter chain Youtube : CyberRodrigue

Focused on:

Network Infrastructure · Systems Administration · Cybersecurity · Cloud · DevOps

This laboratory is part of my practical portfolio to demonstrate hands-on experience with infrastructure, networking and security technologies.
