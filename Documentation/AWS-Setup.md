# AWS Infrastructure Setup

## 1. Introduction

This document explains the implementation of the AWS infrastructure used to provide secure US-based internet access for office users through a Squid Proxy server.

The solution forms part of a hybrid cloud architecture where an on-premises office network communicates securely with AWS using an IPSec Site-to-Site VPN and BGP dynamic routing.

---

## 2. Business Requirement

The client application accepted requests only from a United States public IP address.

Since office users were located outside the US, a cloud-hosted proxy server was required to present a US-based public IP while maintaining secure communication with the office network.

The solution needed to:

- Provide secure connectivity between the office and AWS
- Route office internet traffic through AWS
- Present a US public IP to the client application
- Maintain encrypted communication
- Support approximately 100 users

---

## 3. AWS Services Used

| AWS Service | Purpose |
|-------------|----------|
| Amazon VPC | Private network |
| Public Subnet | Hosts the proxy server |
| Internet Gateway | Internet access |
| Route Table | Network routing |
| Security Group | Firewall for EC2 |
| EC2 | Ubuntu proxy server |
| Customer Gateway | Represents Sophos firewall |
| Virtual Private Gateway | AWS VPN endpoint |
| Site-to-Site VPN | Secure tunnel |
