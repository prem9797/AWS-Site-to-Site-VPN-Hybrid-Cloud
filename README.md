# AWS Site-to-Site VPN Hybrid Cloud Infrastructure

Enterprise-grade hybrid cloud infrastructure built using AWS, Ubuntu Linux, Squid Proxy, Sophos XGS Firewall, and IPSec Site-to-Site VPN with BGP dynamic routing.

---

## Project Overview

This project demonstrates the implementation of a secure hybrid cloud solution that enables users in an on-premises office to access a client application through a US-based public IP address.

The solution was designed by deploying an Ubuntu Linux EC2 instance in AWS, configuring Squid Proxy, and securely integrating the AWS environment with the office network using an IPSec Site-to-Site VPN.

Dynamic routing (BGP) was implemented between AWS and the on-premises Sophos XGS Firewall to provide secure and reliable communication between both environments.

> **Note:** This repository contains a sanitized version of the implementation. All IP addresses, resource names, firewall configurations, and company-specific information have been modified or omitted.

---

# Business Requirement

The client application could only be accessed from a United States IP address.

To satisfy this requirement, internet traffic from office users needed to be securely routed through an AWS-hosted proxy server located in a US region.

The implemented solution:

- Deploy an Ubuntu Linux EC2 instance
- Configure Squid Proxy Server
- Create an AWS Site-to-Site VPN
- Configure BGP Dynamic Routing
- Integrate AWS with Sophos XGS Firewall
- Route office traffic securely through AWS
- Present a US Public IP to the client application

---

# Solution Architecture

```
                  Internet
                       │
               AWS Internet Gateway
                       │
                AWS VPC (US Region)
                       │
                 Ubuntu EC2 Instance
                  Squid Proxy Server
                       │
             Virtual Private Gateway
                       │
================ IPSec VPN Tunnel ================
                       │
                Sophos XGS Firewall
                       │
                 Office Network
                       │
                  End Users
```

---

# Technologies Used

| Technology | Purpose |
|------------|---------|
| AWS EC2 | Ubuntu Proxy Server |
| Amazon VPC | Virtual Network |
| Internet Gateway | Internet Connectivity |
| Route Tables | Network Routing |
| Security Groups | Traffic Filtering |
| Ubuntu Linux | Operating System |
| Squid Proxy | Proxy Service |
| AWS Site-to-Site VPN | Secure Connectivity |
| BGP | Dynamic Route Exchange |
| Sophos XGS Firewall | On-premises Firewall |

---

# Project Specifications

| Component | Details |
|-----------|---------|
| Cloud Provider | AWS |
| Compute | EC2 Ubuntu Linux |
| Proxy Server | Squid Proxy |
| VPN Type | IPSec Site-to-Site VPN |
| Routing | BGP |
| Firewall | Sophos XGS |
| Primary Objective | Secure US-based Internet Access |
| Estimated Users | Approximately 100 |
| Instance Networking Capability | Up to 40 Gbps (depending on instance type) |

---

# Documentation

The complete implementation is documented in the following files.

| Document | Description |
|----------|-------------|
| AWS-Setup.md | AWS infrastructure deployment |
| Firewall.md | Sophos firewall configuration |
| VPN-Configuration.md | VPN and BGP configuration |
| Proxy-Server.md | Ubuntu and Squid Proxy installation |
| Troubleshooting.md | Production issues and resolutions |

---

# Key Features

- Secure Hybrid Cloud Architecture
- AWS Site-to-Site VPN
- BGP Dynamic Routing
- Ubuntu Linux Administration
- Squid Proxy Deployment
- Sophos Firewall Integration
- Secure Network Routing
- Enterprise Documentation

---

# Troubleshooting Highlight

One of the major issues encountered during implementation was that users could access the proxy through the public IP but not through the private IP across the VPN.

After analyzing the traffic flow, the root cause was identified as Source NAT (MASQ) on the firewall. The NAT policy was updated to preserve the original client IP over the VPN, restoring successful communication between the office network and AWS.

This experience strengthened understanding of enterprise networking, routing, NAT behavior, and VPN troubleshooting.

---

# Future Improvements

- Infrastructure as Code using Terraform
- Configuration Automation using Ansible
- CloudWatch Monitoring
- High Availability Deployment
- Multi-AZ Architecture
- Automated Health Checks

---

# License

This project is licensed under the MIT License.
