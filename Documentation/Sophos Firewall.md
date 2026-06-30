# Sophos XGS Firewall Configuration

## 1. Overview

This document explains the configuration of the Sophos XGS 136 Firewall used to securely integrate the on-premises office network with AWS using a Site-to-Site VPN.

The firewall was responsible for establishing the IPSec VPN tunnel, exchanging routes using Border Gateway Protocol (BGP), applying security policies, and securely routing office traffic to the AWS-hosted Squid Proxy server.

> **Note:** All IP addresses, object names, and identifiers used in this document are examples. Production values have been sanitized.

---

# 2. Firewall Information

| Component          | Details                |
| ------------------ | ---------------------- |
| Firewall           | Sophos XGS 136         |
| VPN Type           | IPSec Site-to-Site VPN |
| Routing            | BGP (Dynamic Routing)  |
| Cloud Platform     | AWS                    |
| Integrated Service | Squid Proxy            |

---

# 3. Business Requirement

The office network required secure connectivity to the AWS-hosted Ubuntu Proxy Server.

Rather than exposing internal resources to the public internet, encrypted communication was established using an IPSec Site-to-Site VPN between AWS and the Sophos XGS Firewall.

This allowed office users to securely route traffic through AWS while maintaining centralized security controls.

---

# 4. Import AWS VPN Configuration

After creating the Customer Gateway, Virtual Private Gateway, and Site-to-Site VPN connection in AWS, the VPN configuration file was downloaded.

The configuration file was then imported into the Sophos XGS Firewall under the **Site-to-Site VPN** section.

### Purpose

Importing the AWS-generated configuration automatically populated the IPSec tunnel parameters required for communication with AWS.

### Benefits

* Faster deployment
* Reduced configuration errors
* Consistent VPN parameters
* Simplified AWS integration

---

# 5. IPSec VPN

After importing the VPN configuration:

* IPSec tunnel settings were created automatically.
* VPN interfaces were generated.
* Tunnel parameters matched the AWS configuration.

The firewall then initiated the VPN tunnel with AWS.

---

# 6. BGP Configuration

The VPN was configured to use Border Gateway Protocol (BGP) for dynamic route exchange.

After the VPN tunnel was established, BGP neighborship was formed automatically between AWS and the Sophos Firewall.

### Advantages

* Automatic route learning
* Simplified network management
* Easier future expansion
* Dynamic route updates without manual intervention

No manual static route configuration was required because routing information was exchanged dynamically through BGP.

---

# 7. Firewall Rule Configuration

A dedicated firewall rule was created to allow office users to communicate with AWS through the VPN.

### Rule Summary

| Property            | Value                     |
| ------------------- | ------------------------- |
| Source Zone         | LAN                       |
| Source Networks     | Office LAN, SSL VPN Users |
| Destination Zone    | VPN                       |
| Destination Network | AWS Server                |
| Action              | Allow                     |

### Purpose

This rule permits authorized users from the internal office network and SSL VPN users to securely access AWS resources through the Site-to-Site VPN.

The rule follows the principle of least privilege by allowing only approved traffic.

---

# 8. NAT Configuration

Initially, outbound traffic was processed using the default MASQ (Masquerade) Source NAT policy.

During implementation, this configuration resulted in communication issues between the office network and AWS because the original client IP addresses were translated.

To preserve the original source addresses across the VPN tunnel, the MASQ policy was replaced with an appropriate Source NAT (SNAT) configuration.

This ensured that AWS received the correct client source addresses, allowing successful communication through the VPN.

> The complete troubleshooting process is documented in **Troubleshooting.md**.

---

# 9. Routing

Routing between the office network and AWS was handled dynamically using BGP.

No manual static routes were required because network prefixes were exchanged automatically between AWS and the Sophos Firewall.

This reduced administrative effort and simplified future network expansion.

---

# 10. Verification

After completing the firewall configuration, the following validation steps were performed.

### VPN

* VPN tunnel established successfully
* Tunnel status displayed as **Connected**

### BGP

* Dynamic routes exchanged successfully
* AWS networks became reachable through the VPN

### Traffic Validation

Traffic logs were monitored to verify that office traffic was successfully traversing the VPN tunnel towards AWS.

The firewall logs confirmed successful communication between the office network and AWS resources.

---

# 11. Security Best Practices

The following practices were implemented:

* Imported AWS-generated VPN configuration to reduce configuration errors.
* Used BGP for dynamic route exchange.
* Allowed only trusted internal networks.
* Applied the principle of least privilege to firewall rules.
* Preserved original client IP addresses using SNAT.
* Verified tunnel health and monitored traffic logs after deployment.

---

# 12. Outcome

The Sophos XGS 136 Firewall was successfully integrated with AWS, providing secure hybrid cloud connectivity through an IPSec Site-to-Site VPN.

The firewall established encrypted communication with AWS, exchanged routes using BGP, and securely routed office traffic to the AWS-hosted Squid Proxy Server.

This completed the network integration required before client systems were configured to use the proxy service.
