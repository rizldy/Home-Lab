# 🔀 Lab Module: Load Balancing with Nginx & HAProxy

## 🎯 Objective
Implementing high-availability traffic distribution and load balancing across multiple backend server nodes using Nginx and HAProxy within the Proxmox VE lab environment.

---

## 🏗️ Architecture & Configuration Flow
* **Gateway Layer (CT 1):** Receives incoming HTTP requests from the client.
* **Load Balancer:** Distributes traffic using round-robin / least-conn algorithms.
* **Backend Nodes (VM 1):** Processes application requests across backend Apache/Nginx instances.

---

## ⚙️ Configuration Snippets
*(Example HAProxy backend routing setup)*
```haproxy
backend web_backends
    balance round-robin
    server web1 192.168.1.10:80 check
    server web2 192.168.1.11:80 check
```
