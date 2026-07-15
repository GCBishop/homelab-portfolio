# 🌐 Homelab & Hybrid Cloud Infrastructure Portfolio

> **Overview**
> Production-grade, multi-site infrastructure built and maintained as a self-directed engineering project. This environment documents a zero-trust architecture spanning bare-metal virtualization, container orchestration, split-horizon DNS, and self-hosted AI data pipelines.

<img width="1865" height="917" alt="PVE_Dashboard" src="https://github.com/user-attachments/assets/9a9073f2-16f0-4efb-9fda-13663989dc9e" />


---

## 🖥️ Hardware & Storage Architecture

* **Primary Compute Node:** AMD Ryzen 9 5950X (16C/32T) | 32GB DDR4 RAM
* **AI/ML Accelerator:** AMD Radeon RX 7900 XT 20GB
* *Implementation:* Strictly isolated via IOMMU for exclusive VM PCIe passthrough; host drivers blacklisted at the kernel level.


* **Hypervisor:** Proxmox VE operating isolated LXC and KVM/QEMU workloads.
* **Tiered ZFS Storage Array:**
* **Tier 1 (OS/VMs):** Mirrored NVMe SSDs (High IOPS).
* **Tier 2 (Scratch):** Single NVMe vDev (Transient data/caching).
* **Tier 3 (Bulk):** RAIDZ1 HDD Array (Media and backups).
* *Optimization:* ZFS ARC rigidly hard-capped at 4.0 GiB to prevent hypervisor OOM conditions and guarantee stable memory allocation for AI and Windows virtual machines.



---

## 🔒 Network Topology & Zero-Trust Security

* **Cryptographic Mesh Routing:** Cross-site connectivity (Home Lab to Cloud VPS) powered by Tailscale. All administrative SSH access (password authentication disabled) is restricted exclusively to the mesh subnet or emergency hardware firewall whitelists.
* **Split-Horizon DNS:** Engineered a resilient, dual-node AdGuard Home infrastructure.
* Local clients route through the primary sinkhole.
* Roaming clients utilize the VPS as a full-tunnel exit node.
* Wildcard rewrite rules ensure seamless internal domain proxy resolution regardless of geographic location.


* **Container Hardening:** Telemetry and proxy services strictly enforce the principle of least privilege. Docker sockets are proxied via HAProxy (restricting traffic to `CONTAINERS=1`, `POST=0`) and locked at the kernel level utilizing `cap_drop: ALL` and `no-new-privileges:true`.

---

## 🧠 AI Infrastructure & Data Pipelines

* **Local Inference Engine:** Dedicated Ubuntu Server VM serving Ollama API endpoints. Configured with persistent VRAM caching to eliminate cold-start latency for Large Language Models (e.g., Qwen 27B parameters).
* **Map-Reduce Automation:** Production `n8n` workflows deployed for automated RSS ingestion, vectorized data sanitization, LLM-driven summarization, and chunked webhook payload delivery.
* **Distributed Frontends:** WebUI deployed locally for administrative access, while secondary chat frontends operate remotely on the VPS, securely querying the local GPU engine entirely over the encrypted mesh network.

---

## 🐳 Container Orchestration & Fleet Management

* **Centralized Orchestration:** `Dockge` utilized as the primary stack manager for Docker Compose deployments across nodes.
* **Edge Routing:** Nginx Proxy Manager serves as the domain gateway, orchestrating SSL termination for internal services.
* **Automated Resilience:** Custom `systemd` timers execute weekly automated backup protocols on the VPS—safely halting engines, generating SQLite tarballs, pulling image updates, and pushing heartbeat telemetry to Uptime Kuma upon successful completion.

---

## 🎫 ITSM & Incident Management

* **Service Desk:** `osTicket` deployed via Docker to provide an ITIL-aligned framework for incident tracking and change management.
* **Disaster Recovery Simulation:** The system is utilized to track and resolve simulated data center degradation, including array rebuilds, network partition events, container instability, and backup verification failures. Each ticket formally documents categorization, root-cause isolation, and resolution steps.
