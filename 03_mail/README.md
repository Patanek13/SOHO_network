# 📂 03_Mail – Stalwart Mail Server & Traffic Scrubbing

This section documents the implementation and security of the mail server. The chosen solution is **Stalwart Mail Server**, a modern email solution written in **Rust**, which natively eliminates memory safety vulnerabilities.

## 🚀 Roles and Architecture

The mail server handles the entire email cycle. Within the project, it serves as a "security filter" that subjects all incoming traffic to an in-depth analysis of the sender.

### Key Parameters

* **Protocols:** SMTP (25, 465, 587) and IMAP (143, 993).
* **Administration Security:** The administrative interface (port 8080) is accessible exclusively via VPN and a reverse proxy.
* **Authentication:** Passwords are protected in the configuration by the **sha-512** algorithm.

---

## 🛠️ Security Mechanisms and DNS

To enable the server to legitimately send and receive mail, the following key records are defined in the local DNS (Pi-hole):

| Type | Name | Value (example) | Meaning |
| :--- | :--- | :--- | :--- |
| **SPF** | `mydomain.local` | `v=spf1 mx -all` | Defines which servers are authorized to send emails on behalf of the domain. |
| **DKIM** | `202604r._domainkey.mydomain.local` | `"v=DKIM1; k=rsa; h=sha256; p=MIIBI...` | Public key for verifying the digital signature. |
| **DMARC** | `_dmarc.mydomain.local` | `v=DMARC1; p=reject;` | Policy for handling invalid emails. |

> [!TIP]
> You can find the whole dns records in the file `99-mail.conf` in this folder.

---

## 📄 List of files in this folder

* `README.md`: This document.
* `config.toml`: Main configuration of the Stalwart server.
* `docker-compose.yml`: Configuration of the Stalwart Mail Server container.
* `99-mail.conf`: Export of the necessary DNS records for mail functionality.
