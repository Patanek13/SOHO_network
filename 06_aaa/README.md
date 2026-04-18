# 📂 06_AAA – Authentik & Identity Management

This section documents central identity management using the **Authentik** platform. The project focuses on consolidating login credentials and implementing multi-factor authentication.

## 🚀 The AAA Concept in the Project

In a modern infrastructure, it is critical to have a single trusted source of truth for user accounts. In this project, Authentik provides:

1. **Authentication:** Verification that the user is who they claim to be (password, MFA).
2. **Authorization:** Determining whether a given user has the right to access a specific application (e.g., admin vs. guest).
3. **Accounting:** Logging all login attempts and application accesses for auditing purposes.

## 🚀 Architecture: Identity Provider (IdP)

In the current configuration, Authentik functions as a central authority for applications supporting modern federated protocols.

**Main Protocol: OIDC (OpenID Connect)**
All key applications (Nextcloud, Wiki.js, Portainer) are connected to Authentik via the OIDC protocol.

* **Process:** When a user accesses an application, it redirects them to Authentik. After successful authentication (including MFA), Authentik sends the application a digitally signed token, which grants the user access.

---

## 🔐 Security Layers

### 🔑 Single Sign-On (SSO)

The user logs in only once during their session. They can then seamlessly switch between Nextcloud, Wiki.js, and the administration panel without having to re-enter their credentials. This enhances both convenience and security (the user is not required to enter passwords into various forms).

### 👥 User Groups & Policies

Access to applications is controlled via groups. In our case:

* **`admins` group:** Has access to Portainer and Nextcloud/Wiki management.
* **`Family` group:** Has access only to Nextcloud and Wiki.

---

## 📊 Connection Overview (by NPM and OIDC)

| Domain | Destination IP | Security Method |
| :--- | :--- | :--- |
| `aaa.mydomain.local` | `172.19.0.7` | **Authentik Core** |
| `portainer.mydomain.local` | `172.19.0.14` | **OIDC (Authentik)** |
| `cloud.mydomain.local` | `172.19.0.9` | **OIDC (Authentik)** |
| `wiki.mydomain.local` | `172.19.0.3` | **OIDC (Authentik)** |

---

## 🛡️ Isolation and Access Control

Access to individual applications in Authentik is controlled using **policies**. This allows you to define which users are authorized to view specific applications in the user portal and who has permissions to administer the entire stack.

---

## 📄 List of files

* `README.md`: This document.
* `docker-compose.yml`: Definition of the Authentik stack (Server, Worker, Redis, PostgreSQL).
