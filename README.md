# 🛠️ Home-Lab: Infrastructure & SysAdmin Journal

This repository serves as a personal knowledge base and engineering journal, capturing a full semester of hands-on infrastructure experiments. Beyond academic coursework, it documents real-world configurations, deployment steps, and practical evaluations across System Administration, Distributed Systems, and Network Management.

---

## 🖥️ Lab Infrastructure Overview

All modules and services are hosted and tested on a **Proxmox VE Cluster** with the following node setup:

```mermaid
graph TD
    Client[Client / External Access] -->|Port 80/443| Gateway[CT 1: Reverse Proxy / Load Balancer]
    Gateway -->|Private Network| AppVM[VM 1: Ubuntu Server Workloads]
    Gateway -->|Private Network| MgmtCT[CT 2: Automation & Centralized Services]
