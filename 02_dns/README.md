# 📂 02_DNS – Pi-hole & Encrypted Resolution (DoT)

This section documents the configuration of the DNS infrastructure, which ensures not only local name resolution but also maximum privacy when querying the public internet.

## 🚀 Role in the System

In this project, DNS functions not just as a simple “translator,” but as a security gateway protecting user privacy and the integrity of network traffic.

### Main Functions

1. **Local DNS Resolution:** Resolves `*.mydomain.local` domains to the Raspberry Pi’s IP address.
2. **Ad-blocking & Sinkholing:** Blocking of advertising and tracking scripts at the network level. Automaic blocking of analytical scripts and trackers. Using pihole's blocklists such as [StevenBlack's Unified Hosts], which contains hundreds of thousands of domains known for dangerous behavior.
3. **DNS-over-TLS (DoT):** Encryption of all outgoing DNS queries to prevent eavesdropping by the Internet Service Provider (ISP).

---

## 🔐 Encrypted Upstream (dnsproxy)

Unlike standard Pi-hole configurations, this setup does not use unencrypted upstream servers. All outgoing traffic is routed to a dedicated **dnsproxy** container (`172.19.0.8`).

**Technical query chain:**

1. The client (e.g., a mobile device on a VPN) sends a query to **Pi-hole**.
2. Pi-hole checks whether the domain is on the blacklist or if it is a local entry.
3. If not, Pi-hole forwards the query to the internal **dnsproxy** service.
4. **dnsproxy** creates an encrypted TLS tunnel to the address `tls://1.1.1.1` (Cloudflare) and securely processes the query.

**Security Impact:**
Thanks to this solution, DNS traffic is protected against **DNS Hijacking** and **DNS Cache Poisoning** attacks. At the same time, it prevents anyone along the route (including the ISP) from analyzing which pages are being accessed, thus enhancing user privacy.

---

## 🛠️ Local Record Configuration

To ensure the reverse proxy (NPM) functions correctly, Pi-hole redirects all subdomains to the host’s IP address.

### Static Records (Local DNS Records)

Defined in `custom.list`:

* `cloud.mydomain.local` -> `192.168.1.69`
* `wiki.mydomain.local`  -> `192.168.1.69`
* `mail.mydomain.local`  -> `192.168.1.69`
* `auth.mydomain.local`  -> `192.168.1.69`

---

## 🛡️ Ad-blocking and DNSSEC

The system uses a combination of aggressive blocklists and **DNSSEC** validation.

* **DNSSEC:** Every response from the internet is digitally signed. If an attacker attempts to spoof the IP address of the target server, Pi-hole will discard such a response due to an invalid signature.

---

## 📄 List of files in this folder

* `README.md`: This guide.
* `docker-compose.yml`: Definitions of the Pi-hole and dnsproxy containers.
* `custom.list`: Export of local DNS records.

---

## 🔍 Verifying functionality

We can verify the encrypted connection by capturing traffic on the Raspberry Pi’s external interface (e.g., using `tcpdump`).

* **Observation:** No traffic is leaving for the internet on port 53 (standard DNS). All DNS communication takes place exclusively on port 853 (TLS).
