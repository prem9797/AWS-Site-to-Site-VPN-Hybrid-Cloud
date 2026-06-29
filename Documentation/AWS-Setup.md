## 1. Amazon VPC

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



##  Customer Gateway

### Purpose

A Customer Gateway (CGW) represents the on-premises VPN device in AWS. In this project, the Customer Gateway represents the Sophos XGS Firewall located in the office network.

The Customer Gateway provides AWS with the information required to establish an IPSec VPN connection with the on-premises firewall.

### Configuration

The Customer Gateway was configured using:

* Public IP address of the Sophos XGS Firewall
* Border Gateway Protocol (BGP) using the assigned ASN
* IPSec Site-to-Site VPN

### Why is it required?

Without a Customer Gateway, AWS has no information about the remote firewall that will establish the VPN tunnel.

---

##  Virtual Private Gateway

### Purpose

A Virtual Private Gateway (VGW) is the AWS endpoint for the Site-to-Site VPN.

It is attached to the VPC and securely connects AWS resources to the on-premises office network.

### Configuration

The Virtual Private Gateway was:

* Created in AWS
* Attached to the Hybrid Cloud VPC
* Associated with the Site-to-Site VPN connection

### Benefits

* Secure encrypted communication
* Dynamic route exchange
* Reliable hybrid cloud connectivity

---

##  Site-to-Site VPN

### Purpose

The Site-to-Site VPN establishes an encrypted IPSec tunnel between the office network and AWS.

This tunnel allows office users to securely access the proxy server deployed inside AWS without exposing internal traffic to the public internet.

### Deployment Steps

1. Create Customer Gateway
2. Create Virtual Private Gateway
3. Create Site-to-Site VPN Connection
4. Download the VPN configuration from AWS
5. Import the VPN configuration into the Sophos XGS Firewall
6. Verify that both VPN tunnels are established
7. Validate route exchange using BGP


##  Dynamic Routing using BGP

### Why BGP?

Border Gateway Protocol (BGP) dynamically exchanges network routes between AWS and the on-premises firewall.

Instead of manually maintaining static routes, BGP automatically advertises and learns available networks.

### Advantages

* Automatic route exchange
* Better scalability
* Simplified route management
* Easier future network expansion

BGP was used to ensure reliable communication between AWS resources and the office LAN.

---

##  Validation

After deployment, multiple validation steps were performed to verify the environment.

### Infrastructure Validation

* VPC successfully created
* Public and Private Subnets available
* Internet Gateway attached
* Route Tables configured
* Security Groups applied
* EC2 instance accessible through SSH
* Elastic IP associated

### VPN Validation

* VPN tunnels established successfully
* BGP routes exchanged successfully
* Office network reachable from AWS

