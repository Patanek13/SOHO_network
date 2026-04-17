# 📂 00_Router – Network Security and Firewall

This section documents the configuration of the network entry point. The Raspberry Pi does not act as a traditional router (L3 router) here, but functions as an **Edge Server / Security Gateway** that manages all traffic between the external network (Internet/VPN) and internal services running in Docker.

## 🌐 Network Topology and NAT

From a network infrastructure perspective, the system is hidden behind the main gateway router (home router), which enforces a strict NAT policy.

### Port Forwarding (Home Router)

To enhance security, **only a single port** is enabled on the home router and forwarded to the Raspberry Pi:

* **Port 51820 (UDP)** -> Points to the WireGuard VPN.

**Security Implications:** All other services (Mail Server, Web Applications, Administration) **are not directly exposed to the internet**. An attacker from the external network sees only the open VPN port, while HTTP/S and SMTP traffic is accessible only after an encrypted tunnel is established, which dramatically reduces the risk of exploiting vulnerabilities in applications.

---

## 🛡️ Security Concept on the Edge Server

The security of the Raspberry Pi (Edge Server) itself is based on the principle of **Defense in Depth**:

1. **UFW (Uncomplicated Firewall):** Static port filtering at the OS level.
2. **SSH Hardening:** Strict rules for password-less remote administration.
3. **Fail2ban:** Active protection that blocks port scanning attempts and brute-force attacks.

---

## 🔑 1. SSH Hardening (sshd_config)

Remote server administration is a critical point. The SSH configuration has been modified to eliminate the most common attack vectors (brute-force):

* **`PermitRootLogin no`**: Disables direct login as the `root` user. Administration is performed via a standard user using `sudo`.
* **`PasswordAuthentication no`**: **Key-based security.** Completely disables password-based login. Access is possible exclusively via **SSH keys** (ed25519/RSA).
* **`KbdInteractiveAuthentication no`**: Disables interactive password prompts, thereby preventing automated dictionary attacks.

> [!TIP]
> You can find the complete configuration file in this folder as `sshd_config`.

---

## 🧱 2. Firewall Configuration (UFW)

The firewall is configured to minimize the attack possibilities. Only services necessary for operation are publicly accessible.

### Overview of Allowed Ports

| Port | Protocol | Service | Description |
| :--- | :--- | :--- | :--- |
| **51820** | UDP | **WireGuard** | **Key entry point.** Secure tunnel to the internal network. |
| **80, 443** | TCP | HTTP/S | Entry point for Nginx Proxy Manager (all web applications). |
| **25, 465, 587** | TCP | SMTP | Receiving and sending email (Stalwart Mail Server). |
| **143, 993** | TCP | IMAP/S | Access to email accounts via encrypted protocols. |
| **53** | TCP/UDP | DNS | Traffic for Pi-hole (resolution of `.local` domains). |
| **22** | TCP | SSH | Remote server administration (restricted by firewall and SSH keys). |

---

## 🚩 3. Intrusion Prevention (Fail2ban)

Fail2ban is used to eliminate botnets and vulnerability scanners.

* **SSH Jail:** Although passwords are disabled, Fail2ban still monitors connection attempts and blocks IP addresses that generate excessive log noise.
* **Nginx Proxy Jail:** Monitors Nginx Proxy Manager logs and blocks IP addresses upon repeated detection of `401` or `403` errors.

### 🛡️ Whitelisting

The `ignoreip` parameter is defined in the `jail.local` file to prevent the administrator from being blocked when working from the home network or a VPN (`127.0.0.1`, `192.168.1.0/24`, `10.8.0.1/24`, `172.19.0.0/25`).

---

## 📄 List of documentation in this folder

* `README.md`: This guide.
* `ufw_status.conf`: A direct output of the firewall status from the system.
* `sshd_config`: Secure SSH server configuration.
* `jail.local`: Fail2ban configuration file.

---

## 🔍 Verifying functionality

Effectiveness was tested by attempting to log in to SSH without a private key. The server immediately terminates the connection with the error `Permission denied (publickey)`, without offering the attacker the option to enter a password. Additionally, the firewall blocks all unauthorized ports.
