```markdown
# Project Citadel: Sovereign Homelab Infrastructure

![Proxmox](https://img.shields.io/badge/Proxmox-E57000?style=for-the-badge&logo=proxmox&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Tor](https://img.shields.io/badge/Tor-7D4698?style=for-the-badge&logo=tor-project&logoColor=white)
![Bitcoin](https://img.shields.io/badge/Bitcoin-F7931A?style=for-the-badge&logo=bitcoin&logoColor=white)
![Home Assistant](https://img.shields.io/badge/Home_Assistant-41BDF5?style=for-the-badge&logo=home-assistant&logoColor=white)

> *"The network is the battleground. The server is the fortress. Your data is the sovereignty."*

## 📋 Table of Contents

- [Philosophy](#philosophy)
- [Architecture Overview](#architecture-overview)
- [Hardware Stack](#hardware-stack)
- [Infrastructure Deployment](#infrastructure-deployment)
- [Services & Workloads](#services--workloads)
- [Roadmap](#roadmap)
- [Lessons Learned](#lessons-learned)
- [Contributing](#contributing)

---

## 🔐 Philosophy

**Digital sovereignty is not optional—it's existential.**

In an era of mass surveillance, algorithmic censorship, and corporate data monopolies, self-hosting is the technical expression of autonomy. Project Citadel implements a privacy-first, air-gap-capable homelab infrastructure designed to:

- **Eliminate third-party custody** of personal data
- **Enforce cryptographic anonymity** for sensitive operations
- **Maintain operational resilience** against network-level surveillance
- **Enable censorship-resistant communications** and financial transactions

This is not a cloud alternative. This is **infrastructure as resistance**.

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────┐
│            Proxmox VE Hypervisor                │
├─────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────────────────┐ │
│  │ Home Assistant│  │   Whonix Gateway (Tor)  │ │
│  │   (VM/LXC)   │  │                          │ │
│  │  + Zigbee    │  │   Whonix Workstation     │ │
│  │  Passthrough │  │   (Isolated Operations)  │ │
│  └──────────────┘  └──────────────────────────┘ │
│                                                  │
│  [Future: Bitcoin Node | Monero Node | Matrix]  │
└─────────────────────────────────────────────────┘
                       │
                  Physical Host
          Lenovo ThinkCentre M720q Tiny
```

**Design Principles:**
- **Zero cloud dependency** for core services
- **Hardware-backed isolation** via virtualization
- **Minimal attack surface** (headless post-deployment)
- **Reproducible deployments** via scripting

---

## 💻 Hardware Stack

### Physical Node

| Component | Specification | Rationale |
|-----------|--------------|-----------|
| **Platform** | Lenovo ThinkCentre M720q Tiny | Low power consumption (~15W idle), enterprise-grade reliability, compact form factor |
| **Storage** | 512GB NVMe SSD | Sufficient for OS, VMs, and lightweight workloads; NVMe for low-latency I/O |
| **Boot Method** | Ventoy USB | Multi-ISO boot capability for recovery/reinstallation scenarios |

### BIOS Hardening

Critical firmware configurations applied:

```
- Secure Boot: DISABLED (incompatible with custom kernels/Proxmox)
- Intel VT-x/VT-d: ENABLED (required for hardware passthrough)
- Boot Priority: NVMe → USB → Network
- UEFI Mode: ENABLED
```

**Challenge Resolved:** Initial headless installation failed due to Secure Boot enforcement. Required physical monitor access for BIOS reconfiguration—**reminder that physical security remains foundational**.

---

## 🚀 Infrastructure Deployment

### Hypervisor: Proxmox VE

**Installation:**
- Base OS deployed via Ventoy-mounted ISO
- Post-install repository configuration:

```bash
# Disable enterprise repo (requires subscription)
sed -i 's/^deb/#deb/' /etc/apt/sources.list.d/pve-enterprise.list

# Enable no-subscription repo
echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" > \
  /etc/apt/sources.list.d/pve-no-subscription.list

# Full system upgrade
apt update && apt full-upgrade -y
```

**Script Compatibility Fixes:**
- Community LXC/VM helper scripts required manual patching for Proxmox 8.x compatibility
- Applied version detection bypass for Debian 12 base

---

## 🏠 Services & Workloads

### 1. Home Assistant OS (Domotics Layer)

**Deployment Method:** Automated VM creation via Proxmox helper script

**Migration Path:**
```
Backup Source: External Linux Mint laptop
    ↓
Restore Target: Proxmox VM (HAOS)
    ↓
USB Passthrough: Zigbee/Z-Wave controller (vendor-agnostic dongle)
```

**Technical Configurations:**
- VM set to **start on boot** for high availability
- USB device ID permanently mapped to VM (survives host reboots)
- Network: Bridged mode on `vmbr0` (isolated VLAN planned)

**OpSec Note:** All integrations configured for **local-only** communication. No cloud webhooks. No telemetry.

---

### 2. Whonix (Anonymity Layer)

**Architecture:** Dual-VM setup (Gateway + Workstation)

```
Internet → Whonix Gateway (Tor entry) → Workstation (Isolated apps)
```

**Use Cases:**
- Sensitive research and OSINT operations
- Cryptocurrency transactions requiring network-level anonymity
- Testing censorship circumvention techniques

**Isolation Guarantee:** Workstation VM has **no direct internet access**—all traffic forced through Tor via Gateway VM.

---

## 🗺️ Roadmap

### Phase 1: Foundation (✅ COMPLETE)
- [x] Proxmox deployment and hardening
- [x] Home Assistant migration with hardware passthrough
- [x] Whonix anonymity infrastructure

### Phase 2: Sovereignty Stack (🚧 IN PROGRESS)
- [ ] **Bitcoin Core** (pruned node) - Verify transactions without trusted third parties
- [ ] **Monero Node** (pruned) - Privacy-preserving financial sovereignty
- [ ] **Matrix Homeserver** (Synapse/Dendrite) - E2EE communications
- [ ] **VLAN Segmentation** - IoT isolation, DMZ for public-facing services

### Phase 3: Advanced Resilience (📅 PLANNED)
- [ ] Automated off-site encrypted backups (rsync over Wireguard)
- [ ] Intrusion detection (Suricata/Zeek)
- [ ] Mesh networking capability (Yggdrasil/cjdns)
- [ ] Airgap-capable signing environment (Tails/QubesOS integration)

---

## 📚 Lessons Learned

### Operational Security
1. **Headless installations are fragile** - Firmware-level issues require physical access. Always have a backup monitor.
2. **USB passthrough requires device ID stability** - Use `usb-by-id` paths, not `/dev/ttyUSB*`.
3. **Enterprise repositories != Community repositories** - Proxmox subscription model requires manual repo management.

### Architectural
4. **Backup hygiene is non-negotiable** - Home Assistant's backup portability saved hours of reconfiguration.
5. **Scripting fragility** - Community scripts break between major versions. Fork and version-lock critical automation.
6. **Network segmentation is a day-one requirement** - Retrofitting VLANs post-deployment is painful.

---

## 🤝 Contributing

This repository serves as **documentation**, not a distribution.

If you're building similar infrastructure:
- **Fork freely** - Adapt to your threat model
- **Test in VMs first** - Bare metal mistakes are expensive
- **Document your changes** - Future you will thank present you

### Philosophy over Tools
The specific tools (Proxmox, Whonix, etc.) are implementation details. The **principles** are universal:
- Own your hardware
- Control your network
- Encrypt your data
- Verify everything

---

## ⚠️ Disclaimer

This infrastructure is built for **educational purposes** and **personal sovereignty**. The author assumes no liability for misconfiguration, data loss, or legal consequences arising from deployment in your jurisdiction.

**Operational security is a process, not a product.** Stay vigilant.

---

## 📜 License

Documentation: CC BY-SA 4.0  
Code/Scripts: MIT License

---

**Built with conviction. Deployed with caution. Maintained with discipline.**

*"Cypherpunks write code. Sovereign engineers run nodes."*
```
