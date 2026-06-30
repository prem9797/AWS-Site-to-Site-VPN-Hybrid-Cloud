# Ubuntu Proxy Server Configuration

## 1. Overview

The Ubuntu Proxy Server was deployed on an Amazon EC2 instance to provide secure outbound internet access through a US-based public IP address. The server was configured using Squid Proxy and integrated into a hybrid cloud architecture connected to the on-premises network through an AWS Site-to-Site VPN.

The proxy server enables office users to access client applications that require requests to originate from the United States while maintaining secure communication through the corporate firewall.

> **Note:** All IP addresses and network identifiers in this document are examples. Production values have been sanitized.

---

# 2. Server Specifications

| Component        | Details                 |
| ---------------- | ----------------------- |
| Cloud Platform   | AWS EC2                 |
| Operating System | Ubuntu Server 24.04 LTS |
| Instance Type    | t3a.medium              |
| Proxy Software   | Squid Proxy             |
| Proxy Port       | 3128                    |
| Intended Users   | Approximately 100       |

---

# 3. Initial Server Preparation

Before installing Squid, the Ubuntu server was updated with the latest package information and security patches.

## Update Package Repository

```bash
sudo apt update
```

**Purpose**

Downloads the latest package metadata from the Ubuntu repositories.

---

## Upgrade Installed Packages

```bash
sudo apt upgrade -y
```

**Purpose**

Installs the latest available package updates and security fixes.

---

# 4. Install Squid Proxy

Install the Squid Proxy package.

```bash
sudo apt install squid -y
```

**Purpose**

Installs the Squid Proxy service and its dependencies.

---

# 5. Verify Installation

Verify that the Squid service is installed and running.

```bash
sudo systemctl status squid
```

Expected result:

* Active (running)

---

# 6. Enable Squid at Boot

```bash
sudo systemctl enable squid
```

**Purpose**

Ensures the Squid service starts automatically after every server reboot.

---

# 7. Backup the Default Configuration

Before modifying the default configuration, create a backup.

```bash
sudo cp /etc/squid/squid.conf /etc/squid/squid.conf.bak
```

**Purpose**

Maintaining a backup allows the original configuration to be restored quickly if required.

---

# 8. Squid Configuration File

The primary Squid configuration file is located at:

```text
/etc/squid/squid.conf
```

The configuration file controls:

* Proxy listening port
* Access Control Lists (ACLs)
* Access policies
* DNS behavior
* Memory allocation
* Logging
* Proxy behavior

---

# 9. Configure the Proxy Port

The proxy server was configured to listen on:

```conf
http_port 3128
```

**Purpose**

Port **3128** is the default Squid proxy port and was used by client browsers to access the proxy service.

---

# 10. Configure Access Control Lists (ACL)

Access Control Lists were configured to ensure that only authorized users could use the proxy server.

### Configured Networks

* Office Internal Network (LAN)
* Trusted Office Public IP
* SSL VPN User Network

Example configuration:

```conf
# Office LAN
acl office_lan src 10.0.0.0/24

# Trusted Office Public IP
acl office_public src 203.0.113.10

# SSL VPN Users
acl ssl_vpn src 10.20.0.0/24
```

> **Note:** Example IP addresses are used for documentation purposes.

**Purpose**

ACLs restrict proxy access to trusted office users and VPN-connected users while preventing unauthorized systems from using the proxy.

---

# 11. Configure Access Policies

After defining the ACLs, access rules were configured.

Example:

```conf
http_access allow office_lan
http_access allow office_public
http_access allow ssl_vpn

http_access deny all
```

**Purpose**

The final `http_access deny all` rule blocks all traffic that is not explicitly allowed.

This follows the Principle of Least Privilege by permitting access only to approved networks.

---

# 12. Performance Optimization

The following parameters were configured to improve proxy performance.

```conf
cache_mem 512 MB

maximum_object_size_in_memory 512 KB

dns_v4_first on
```

### cache_mem

Allocates memory used by Squid for frequently accessed objects.

Configured value:

```text
512 MB
```

---

### maximum_object_size_in_memory

Limits the maximum object size stored in memory.

Configured value:

```text
512 KB
```

This helps optimize memory utilization.

---

### dns_v4_first

```conf
dns_v4_first on
```

Forces Squid to prefer IPv4 DNS responses before IPv6.

This improves compatibility in environments where IPv4 networking is primarily used.

---

13. Proxy Mode

The Squid server was configured to operate primarily as a forward proxy.

Purpose

The primary objective of the proxy server was not to cache web content, but to securely forward outbound client traffic through the AWS-hosted public IP address.

This design allowed office users to access client applications that required requests to originate from a United States IP address.

Caching

No dedicated disk cache (cache_dir) was configured as part of this implementation.

The proxy server was optimized for secure traffic forwarding rather than content caching.


# 14. Validate Configuration

Before restarting Squid, validate the configuration syntax.

```bash
sudo squid -k parse
```

**Purpose**

Checks the Squid configuration for syntax errors without restarting the service.

---

# 15. Restart the Squid Service

```bash
sudo systemctl restart squid
```

Verify the service:

```bash
sudo systemctl status squid
```

---

# 16. Verify Listening Port

```bash
sudo ss -tulpn | grep 3128
```

**Purpose**

Confirms that Squid is actively listening on TCP port 3128.

---

# 17. Monitor Proxy Logs

The Squid access log is located at:

```text
/var/log/squid/access.log
```

To monitor requests in real time:

```bash
sudo tail -f /var/log/squid/access.log
```

**Purpose**

Used during deployment and testing to confirm that client requests were reaching the proxy server successfully.

---

# 18. Security Best Practices

The following security practices were followed during deployment:

* Restricted SSH access to trusted administrative networks.
* Allowed proxy access only from authorized office networks.
* Created Access Control Lists (ACLs) for approved users.
* Denied all unauthorized traffic using `http_access deny all`.
* Backed up the original Squid configuration before making changes.
* Kept Ubuntu packages updated with the latest security patches.

---

# 19. Outcome


The Ubuntu 24.04 LTS server was successfully configured as an enterprise Squid Forward Proxy hosted on AWS EC2.

The server was prepared to receive traffic from the on-premises network through the AWS Site-to-Site VPN and Sophos XGS Firewall.

Key achievements:

- Successfully deployed Squid Proxy on Ubuntu 24.04 LTS.
- Restricted proxy access using Access Control Lists (ACLs).
- Applied performance tuning parameters for stable operation.
- Configured the proxy to listen on TCP port 3128.
- Validated the Squid configuration and service status.
- Prepared the server for secure integration with the hybrid cloud network.

The next phase of the implementation involved importing the AWS VPN configuration into the Sophos XGS Firewall, configuring BGP, creating firewall and NAT policies, and establishing secure communication between the office network and AWS.

