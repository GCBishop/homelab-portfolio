# Homelab Infrastructure Portfolio

Production-grade multi-site server and network infrastructure built and maintained as a self-directed learning project. This repository documents the architecture, configuration, and operational practices for a homelab environment spanning virtualization, container orchestration, zero-trust networking, and automated monitoring.

## Hardware
- **Primary Host:** AMD Ryzen 9 5950X (16C/32T), 128GB RAM
- **GPU:** AMD RX 7900 XT 20GB (passed through to VM)
- **OS Storage:** Mirrored NVMe SSDs
- **Bulk Storage:** RAIDZ1 HDD pool
- **Hypervisor:** Proxmox VE (Debian-based)
- **Remote VPS:** IONOS VPS (Debian) - Cloud services and backup target

## Architecture Overview

### Server and Storage Layer
- Custom server build with AMD Ryzen 9 5950X
- Proxmox VE hypervisor with LXC containers and KVM/QEMU VMs
- Tiered ZFS storage: mirrored NVMe (OS), RAIDZ1 HDD (bulk), scratch pool (transient)
- GPU passthrough via IOMMU group isolation for AI/ML workloads

### Network Layer
- Zero-trust Tailscale mesh network spanning two geographic sites
- Subnet routing and exit node configuration
- No administrative interfaces exposed to public internet
- All remote access via WireGuard/Tailscale VPN only

### Container & Automation Layer
- Docker + Docker Compose for service orchestration (Nginx Proxy Manager, Uptime Kuma, n8n, osTicket, Open WebUI)
- Inter-container networking with persistent storage
- Security hardening: capability dropping, socket proxying
- Automated backup protocol via systemd timers with webhook heartbeat verification to Uptime Kuma

## Incident Management
- Deployed osTicket (Docker) for ITIL-style ticketing and incident tracking
- Created sample tickets simulating real data center scenarios: disk failures, network outages, container instability, and backup verification failures
- Each ticket includes categorization, prioritization, troubleshooting steps, and resolution documentation

## Skills Demonstrated
- Server hardware assembly and component installation
- Storage architecture (ZFS, RAID, tiered storage)
- Network architecture (VLANs, subnet routing, VPN mesh, DNS design)
- Linux administration (Ubuntu Server, Debian, Bash)
- Container orchestration (Docker, Docker Compose)
- Monitoring, alerting, and incident response (Uptime Kuma, SNMP, osTicket)
- Automation and scripting (systemd, n8n, Python, Bash)
