## 4. Amazon VPC

### Purpose

The Amazon Virtual Private Cloud (VPC) provides an isolated networking environment for all AWS resources used in this project. It forms the foundation of the hybrid cloud architecture by allowing EC2 instances, VPN gateways, route tables, and security groups to communicate securely.

### Configuration (Sanitized)

| Property       | Value                   |
| -------------- | ----------------------- |
| Name           | Hybrid-Cloud-VPC        |
| CIDR Block     | 10.0.0.0/16 *(Example)* |
| DNS Resolution | Enabled                 |
| DNS Hostnames  | Enabled                 |

> **Note:** The CIDR block shown above is an example used for documentation purposes. Production network ranges have been replaced.

### Why was a VPC required?

The VPC isolates the cloud infrastructure from other AWS customers while allowing secure communication between the office network and AWS through the Site-to-Site VPN.

---

## 5. Public and Private Subnets

### Purpose

The infrastructure uses both public and private subnets to separate internet-facing resources from internal networking components.

### Public Subnet

The public subnet hosts the Ubuntu EC2 instance running the Squid Proxy service. It has access to the Internet Gateway, allowing outbound internet connectivity.

### Private Subnet

The private subnet is reserved for future expansion and private resources that should not be directly accessible from the internet.

### Benefits

* Improved security
* Better network segmentation
* Easier future expansion
* Follows AWS networking best practices

---

## 6. Internet Gateway

### Purpose

The Internet Gateway allows resources in the public subnet to communicate with the internet.

Without the Internet Gateway, the Squid Proxy server would not be able to forward internet traffic for office users.

### Configuration

* Internet Gateway attached to the VPC
* Public route table updated to send default traffic (0.0.0.0/0) to the Internet Gateway

### Verification

* EC2 instance successfully accessed the internet.
* System updates completed successfully.
* Squid Proxy was able to communicate with external websites.

---

## 7. Route Tables

### Purpose

Route tables determine how network traffic moves between AWS resources, the office network, and the internet.

### Configuration

The public route table contained:

* Local VPC route
* Default route to the Internet Gateway
* Routes exchanged through the Site-to-Site VPN using BGP

### Why BGP?

Border Gateway Protocol (BGP) dynamically exchanges routes between AWS and the on-premises Sophos XGS Firewall.

This eliminates the need to manually update routes whenever the network changes and improves scalability.

---

## 8. Security Group Configuration

The EC2 instance was protected using AWS Security Groups.

### Inbound Rules

| Port             | Purpose                |
| ---------------- | ---------------------- |
| 22               | SSH administration     |
| 3128             | Squid Proxy            |
| ICMP             | Connectivity testing   |
| Office Static IP | Administrative access  |
| Office Subnet    | Internal communication |

### Security Considerations

Administrative access was restricted to trusted office networks. Only the minimum required ports were opened, following the principle of least privilege.

---

## 9. EC2 Deployment

### Operating System

Ubuntu Server 24.04 LTS

### Instance Type

t3a.medium

### Purpose

The EC2 instance hosts the Squid Proxy Server responsible for routing outbound office traffic through a US-based public IP address.

### Elastic IP

An Elastic IP address was associated with the EC2 instance to provide a consistent public IP address for outbound communication.

Using an Elastic IP ensures that the public IP remains unchanged even if the EC2 instance is restarted.

