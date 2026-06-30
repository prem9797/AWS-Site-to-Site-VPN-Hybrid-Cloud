# Architecture

This folder contains the architecture diagrams for the AWS Site-to-Site VPN Hybrid Cloud implementation.

## High-Level Architecture

![AWS Hybrid Cloud Architecture](AWS-Hybrid-Cloud-Architecture.png)

## Components

- AWS US West (N. California) Region
- Amazon VPC
- Public & Private Subnets
- Ubuntu 24.04 EC2 Instance
- Squid Proxy
- Internet Gateway
- Virtual Private Gateway
- AWS Site-to-Site VPN
- IPSec Tunnel
- BGP Dynamic Routing
- Sophos XGS 136 Firewall
- Office LAN
- Firefox Users

## Description

This architecture illustrates a hybrid cloud environment where office users securely access a client application through an AWS-hosted Squid Proxy. Communication between the on-premises office and AWS is established using an IPSec Site-to-Site VPN with dynamic routing through BGP.
