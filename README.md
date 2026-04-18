# 🛡️ SOHO Infrastructure Security Project

This repository contains the complete configuration and documentation for a secure home/office (SOHO) infrastructure built on the **Raspberry Pi** platform and using **Docker** containerization.

## 🎯 Project Goal

The main goal was to create an isolated, encrypted, and monitored environment for running critical services (Cloud, Mail, Wiki) with an emphasis on the three pillars of information security: **Confidentiality, Integrity, and Availability (CIA Triad)**.

---

## 🏗️ System Architecture (Defense in Depth)

The project employs a "defense in depth" strategy. An attacker must overcome several independent barriers to compromise the data:

1. **External barrier:** Port filtering and WireGuard VPN (the infrastructure is not visible from the internet).
2. **Network barrier:** Isolated Docker networks (`soho_net`) and a reverse proxy.
3. **Identity barrier:** Centralized AAA (Authentik).
4. **Application barrier:** Active email traffic scrubbing and security DNS filtering.

---

## 📂 Documentation Structure

The project is divided into logical modules, each of which has its own detailed documentation:

| Folder | Module | Key Technologies |
| :--- | :--- | :--- |
| **`00_router`** | Host Security | UFW, Fail2ban, SSH Hardening |
| **`01_reverse-proxy`** | Entry Point | Nginx Proxy Manager, SSL, Real IP |
| **`02_dns`** | Secure Querying | Pi-hole, DNS-over-TLS, DNSSEC |
| **`03_mail`** | Email Scrubbing | Stalwart, SPF/DKIM/DMARC |
| **`04_apps`** | User Services | Nextcloud, Wiki.js, Portainer |
| **`05_vpn`** | Remote Access | WireGuard, WG-Easy |
| **`06_aaa`** | Identity management | Authentik, OIDC, SSO, MFA |

---

## 🚀 How to use the system

1. **Connection:** The user connects to the VPN tunnel (WireGuard).
2. **Name resolution:** The local DNS (Pi-hole) resolves the domain (e.g., `cloud.mydomain.local`) to the Raspberry Pi’s IP address.
3. **Authentication:** When accessing any service, the user is prompted to log in to the Authentik portal.
4. **Work:** After successful SSO (Single Sign-On), the user can seamlessly switch between all applications.
