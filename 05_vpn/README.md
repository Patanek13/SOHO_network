# 📂 05_VPN – WireGuard (Secure Access)

This folder documents the implementation of a VPN tunnel that serves as the sole authorized entry point for server management and access to internal services.

## 🚀 "Invisible Infrastructure" Concept

The entire infrastructure is designed to be invisible from the public internet. The only open port on the border router (our home router) is the VPN port.

### Main Benefits

1. **Elimination of the attacks from internet:** Services such as SSH, Portainer, or Stalwart administration are not exposed to the internet.
2. **Encrypted Transmission:** All communication between the client (mobile/laptop) and the Raspberry Pi is encrypted using modern cryptography (ChaCha20-Poly1305).
3. **Forced DNS:** The VPN automatically tunnels DNS queries through **Pi-hole**, ensuring privacy even when on the go.

---

## 🛠️ Server configuration (wg0.conf)

The server runs in a Docker container and uses a virtual network interface in the `10.8.0.0/24` range.

**Key parameters:**

* **`Address = 10.8.0.1/24`**: The VPN gateway’s IP address.
* **`ListenPort = 51820`**: The only port allowed in the home router’s NAT.
* **`PostUp / PostDown`**: IPTables rules ensuring proper forwarding between the VPN and the Docker network `soho_net`.

---

## 📱 Client Configuration

Clients are added using public keys. A unique configuration file is generated for each device.

**Client Security Settings:**

* **`AllowedIPs = 0.0.0.0/0`**: (Full Tunnel) All traffic from the mobile device goes through the VPN. This ensures secure browsing even on unsecured public Wi-Fi networks.
* **`DNS = 10.8.0.1`**: Forces the use of the local Pi-hole, which allows for the resolution of `.local` domains even remotely.

---

## 📄 List of files in this folder

* `README.md`: This guide.
* `docker-compose.yml`: Configuration of the WireGuard container.
* `wg-server.conf`: Example of the WireGuard server configuration file.
* `wg-client.conf`: Example of the WireGuard client configuration file.
