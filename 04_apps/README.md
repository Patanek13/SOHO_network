# 📂 04_Apps – User Applications and Internal Services

This folder documents the deployed applications that form the functional core of the entire system. All services run as Docker containers on the isolated `soho_net` network.

## 🚀 Application Architecture

All applications in this section share common security principles:

1. **No public ports:** Containers do not have ports mapped to the host (e.g., Nextcloud does not run on the host’s port 80). They are accessible exclusively through **Nginx Proxy Manager**.
2. **Separate Databases:** Critical applications use their own MariaDB or PostgreSQL instances for maximum stability and easy backup.
3. **SSO Integration:** Access to administrative interfaces is protected by **Authentik** central authentication.

---

## 🛠️ Overview of Deployed Services

### ☁️ Nextcloud (Collaboration Suite)

The main platform for file storage and collaboration.

* **Database:** MariaDB
* **Cache:** Redis (to improve performance when working with files)
* **Security:** Transport encryption (SSL) and protection against brute-force attacks at the application level.

### 📝 Wiki.js (Knowledge Base)

A central hub for project documentation and internal procedures.

* **Database:** PostgreSQL
* **Features:** A modern, fast wiki with Markdown support that serves as the "living documentation" for this server.

### 🐳 Portainer (Docker Management)

A graphical interface for managing and monitoring all containers.

* **Features:** Enables quick log diagnostics and container lifecycle management without the need for constant SSH connection.

---

## 🛡️ Security Layers (App Hardening)

The following restrictions are applied to each application within Docker:

* **Network Isolation:** "Database" applications are only visible on the network to their "Backend" counterparts. For example, the Nextcloud database is not visible to Wiki.js.
* **Restart Policy:** Set to `unless-stopped`, which ensures that services start automatically after a Raspberry Pi restart (e.g., after a power outage).
* **Volume Mapping:** All user data is stored in persistent volumes (Docker Volumes), which allows for easy migration and incremental backups of the entire system.

---

## 🔗 Integration with Other Modules

| Application | Access URL | Authentication |
| :--- | :--- | :--- |
| **Nextcloud** | `cloud.mydomain.local` | Internal + Authentik (optional) |
| **Wiki.js** | `wiki.mydomain.local` | Authentik (SSO) |
| **Portainer** | `portainer.mydomain.local` | Authentik (SSO) |

---

## 📄 List of files in this folder

* `README.md`: This document.
* `docker-compose_nextcloud.yml`: Definitions of the Nextcloud container and its dependencies.
* `docker-compose_wikijs.yml`: Definitions of the Wiki.js container and its dependencies.
* `docker-compose_portainer.yml`: Definitions of the Portainer container.
