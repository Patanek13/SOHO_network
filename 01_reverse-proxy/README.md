# 📂 01_Reverse-Proxy – Routing and SSL Termination

This section contains the configuration for the central entry point for all web traffic (HTTP/S) within the infrastructure. **Nginx Proxy Manager (NPM)** was chosen as the solution.

## 🚀 Role in the System

Due to the isolation of individual services within the Docker network (`soho_net`), the reverse proxy acts as the sole authorized intermediary between the user (in the VPN) and the end applications.

### Main Functions

1. **SSL Termination:** NPM manages SSL certificates and ensures encrypted communication (HTTPS). Endpoint containers thus do not need to handle their own certificates. All certificates are self-signed which is sufficient for internal use but can be easily replaced with valid ones.
2. **Service Abstraction:** Allows access to applications via domain names (e.g., `cloud.mydomain.local`) instead of IP addresses and non-standard ports.
3. **Security Filtering:** Serves as a point for applying security policies and SSO authentication.

---

## 🛠️ Configuration

NPM is deployed in the Docker network on the static IP `172.19.0.2`.

### 🔑 Advanced Real IP Settings

Despite the limitations described below (see the Network Specifics section), a snippet for the `ngx_http_realip_module` is applied to each host. This prepares the infrastructure for transparent IP address passing:

```nginx
# Definition of trusted nodes
set_real_ip_from 172.19.0.0/16;
set_real_ip_from 10.8.0.0/24;

# Header carrying the original IP
real_ip_header X-Forwarded-For;
real_ip_recursive on;
```

## 📄 List of documentation in this folder

* `README.md`: This guide.
* `docker-compose.yml`: Configuration of the Nginx Proxy Manager container.
