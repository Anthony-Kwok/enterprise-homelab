# Enterprise IT & Security Homelab (HA + SOC)

## Purpose

This repository provides a **fully reproducible, enterprise-style IT and Security homelab** designed to demonstrate **system administration, network administration, and SOC/security engineering capabilities**.

The goal is simple and explicit:

> If someone clones this repository and follows the README, they can build a working, high-availability enterprise lab that mirrors real-world corporate infrastructure.

This is **not** a demo environment. It is intentionally designed to surface:

* Operational complexity
* Failure modes
* Security visibility challenges

---

## What This Lab Demonstrates

### System Administration

* Windows Active Directory (users, groups, service accounts)
* Linux server administration
* Patch management concepts
* Baseline OS hardening
* Logging configuration

### Network Administration

* VLAN segmentation
* High-availability firewalls (pfSense CARP)
* Stateful failover (pfsync)
* Internal routing and access control
* Load balancer tier separation

### Security Operations (SOC)

* Centralized log ingestion
* Network telemetry (Zeek, Suricata)
* Detection engineering
* Incident response workflows
* Investigation and lessons learned

---

## High-Level Architecture

This lab models a **small enterprise with redundancy and separation of duties**.

```
                           Internet
                               |
                        Public CARP VIP
                               |
                 =================================
                 |                               |
           pfSense-A (Active)              pfSense-B (Standby)
           WAN / LAN / SYNC                WAN / LAN / SYNC
                 |                               |
                 -------- pfsync / config -------
                               |
                         Core Virtual Switch
                               |
        ----------------------------------------------------------------
        |              |               |               |               |
   Mgmt VLAN       User VLAN        App VLAN         DB VLAN        Monitor VLAN
  10.0.10.0/24    10.0.20.0/24     10.0.30.0/24     10.0.40.0/24     10.0.50.0/24
        |              |               |               |               |
   Jump Host     Win / Linux PCs    HAProxy LB      PostgreSQL      Security Onion
                                    (VIP)          Primary/Replica  (SPAN + Logs)
                                      |
                              ----------------
                              |              |
                           App Server 1   App Server 2
```

---

## Prerequisites

Before starting, you should have:

* A system capable of running **Proxmox VE** or other virtualization software (Hyper-V, ESXi, etc.)

  * Minimum: 32 GB RAM, 8 CPU cores recommended
* Basic familiarity with:

  * Linux
  * Networking concepts (IP, VLANs, firewalls)
  * Git

---

## How to Use This Repository (High Level)

1. Install Proxmox VE or other virtualization software
2. Clone this repository
3. Review the architecture documentation
4. Use Terraform to provision infrastructure 
5. Use Ansible to configure systems 
6. Validate logging and SOC visibility
7. Execute failure and attack scenarios

Each step is documented in detail.

---

## Repository Structure Overview

```
enterprise-it-security-lab/
├── README.md                # Start here
├── architecture/            # Network & trust design
├── terraform/               # Infrastructure as Code
├── ansible/                 # System configuration
├── sysadmin/                # Admin workflows
├── network-admin/           # Firewall & routing logic
├── soc/                     # Detection & IR
└── lessons-learned.md       # Failures & improvements
```

---

## Design Philosophy (Important)

* **Reproducibility over screenshots**
* **Documentation over guesswork**
* **Failure testing over happy paths**

This mirrors how real enterprises operate.

---

## Who This Is For

* Aspiring or junior **Systems Administrators**
* **SOC Analysts** who want infrastructure depth
* **Security Engineers** transitioning from SOC
* Hiring managers evaluating real-world capability

---

## Disclaimer

This lab is for **educational purposes only**.
All attack simulations are performed in an isolated environment you control.

---

## Next Step

Begin with:

```
architecture/network-topology.md
```

This explains the network design in full detail before any infrastructure is built.

---

## 1. Architecture Overview

```
                           Internet
                               |
                        Public CARP VIP
                               |
                 =================================
                 |                               |
           pfSense-A (Active)              pfSense-B (Standby)
           WAN / LAN / SYNC                WAN / LAN / SYNC
                 |                               |
                 -------- pfsync / config -------
                               |
                         Core Virtual Switch
                               |
        ----------------------------------------------------------------
        |              |               |               |               |
   Mgmt VLAN       User VLAN        App VLAN         DB VLAN        Monitor VLAN
  10.0.10.0/24    10.0.20.0/24     10.0.30.0/24     10.0.40.0/24     10.0.50.0/24
        |              |               |               |               |
   Jump Host     Win / Linux PCs    HAProxy LB      PostgreSQL      Security Onion
                                    (VIP)          Primary/Replica  (SPAN + Logs)
                                      |
                              ----------------
                              |              |
                           App Server 1   App Server 2
```

---

## 2. Technology Stack

| Layer             | Tool                        |
| ----------------- | --------------------------- |
| Virtualization    | Proxmox VE                  |
| Firewall          | pfSense (HA via CARP)       |
| Load Balancer     | HAProxy                     |
| Identity          | Windows Server 2022 (AD DS) |
| Servers           | RHEL / Ubuntu               |
| SIEM              | Security Onion              |
| IaC               | Terraform                   |
| Config Mgmt       | Ansible                     |
| Attack Simulation | Atomic Red Team             |

---

## 3. Repository Structure

```
enterprise-it-security-lab/
├── README.md
├── architecture/
│   ├── network-topology.md
│   ├── vlan-plan.md
│   └── trust-boundaries.md
├── terraform/
│   ├── providers.tf
│   ├── networking/
│   │   ├── vlans.tf
│   │   └── firewall-ha.tf
│   ├── compute/
│   │   ├── ad.tf
│   │   ├── app_servers.tf
│   │   ├── db.tf
│   │   └── security_onion.tf
│   └── loadbalancer/
│       └── haproxy.tf
├── ansible/
│   ├── inventory.ini
│   ├── roles/
│   │   ├── linux-hardening/
│   │   ├── windows-logging/
│   │   ├── haproxy/
│   │   └── postgres-ha/
│   └── playbooks/
│       ├── baseline.yml
│       ├── logging.yml
│       └── haproxy.yml
├── soc/
│   ├── ingest.md
│   ├── detections/
│   │   ├── ad-bruteforce.md
│   │   ├── lateral-movement.md
│   │   └── dns-tunneling.md
│   ├── hunts/
│   └── incidents/
│       ├── 001-ad-bruteforce.md
│       └── 002-lateral-movement.md
└── lessons-learned.md
```

