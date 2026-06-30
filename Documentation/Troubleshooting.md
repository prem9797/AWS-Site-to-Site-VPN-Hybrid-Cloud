# Troubleshooting and Root Cause Analysis

## 1. Overview

This document describes the troubleshooting process performed during the deployment of the AWS Hybrid Cloud Proxy solution.

During implementation, the proxy server functioned correctly when accessed through its AWS public IP address. However, communication failed when office users attempted to access the same proxy using its private IP address across the AWS Site-to-Site VPN.

This document explains the investigation process, identifies the root cause, and documents the corrective actions taken to restore communication.

---

# 2. Problem Statement

After completing the AWS infrastructure, Ubuntu proxy server configuration, and Site-to-Site VPN deployment, users were instructed to configure Mozilla Firefox to use the AWS-hosted Squid Proxy Server.

### Firefox Proxy Configuration

* Proxy Server: AWS EC2 Private IP
* Proxy Port: 3128

### Expected Result

Office users should securely access the client application through the AWS-hosted proxy server using the Site-to-Site VPN.

### Actual Result

The proxy functioned correctly when using the EC2 Public IP address.

However, communication failed when using the EC2 Private IP address over the VPN.

---

# 3. Symptoms Observed

The following behaviour was observed during testing.

* Firefox could not browse through the proxy.
* Connection attempts timed out.
* Some requests returned "Connection Refused".
* Client applications were unable to communicate through the private VPN path.

The VPN tunnel itself remained established throughout testing.

---

# 4. Initial Investigation

The troubleshooting process followed a structured approach.

## Step 1 – Verify Squid Service

The Squid service status was checked.

```bash
sudo systemctl status squid
```

Result:

The Squid service was running normally.

---

## Step 2 – Review Squid Logs

The Squid access log was monitored.

```bash
sudo tail -f /var/log/squid/access.log
```

Purpose:

To determine whether requests from office users were reaching the proxy server.

---

## Step 3 – Verify AWS Security Groups

The EC2 Security Group configuration was reviewed.

Verification included:

* SSH access
* TCP Port 3128
* ICMP
* Office Network access

No issues were identified.

---

## Step 4 – Verify Route Tables

AWS Route Tables were reviewed to confirm that VPN traffic was correctly routed between the office network and the VPC.

The routing configuration was verified successfully.

---

## Step 5 – Verify VPN Status

The Site-to-Site VPN status was examined.

Verification confirmed:

* VPN Tunnel: Connected
* BGP Routes: Successfully exchanged

The VPN infrastructure itself was operating normally.

---

# 5. Root Cause Analysis

After eliminating issues related to the AWS infrastructure, VPN connectivity, and Squid configuration, the investigation focused on Source Network Address Translation (SNAT) performed by the Sophos Firewall.

The firewall was initially configured to use the default **MASQ (Masquerade)** policy.

MASQ translated the original office client IP address into the firewall's public IP address before forwarding traffic through the VPN.

As a result, AWS received requests originating from the firewall instead of the original office client.

This source address translation disrupted the expected traffic flow across the VPN and prevented successful communication with the proxy server over its private IP address.

---

# 6. Resolution

The default MASQ policy was replaced with an appropriate Source NAT (SNAT) configuration.

The updated SNAT policy preserved the original source IP addresses of office clients when forwarding traffic through the Site-to-Site VPN.

After applying the change:

* Original client IP addresses were maintained.
* AWS received traffic from the expected internal source networks.
* Communication between the office network and the AWS-hosted Squid Proxy was successfully restored.

---

# 7. Validation

After implementing the SNAT policy, the following validation steps were completed.

### Firefox Configuration

Mozilla Firefox was configured to use:

* AWS EC2 Private IP
* Port 3128

### Connectivity Test

* Client application loaded successfully.
* Internet requests passed through the Squid Proxy.
* Communication over the Site-to-Site VPN was successful.

### Public IP Verification

The external public IP was verified to ensure that outbound requests originated from the AWS-hosted US public IP address rather than the office public IP.

This confirmed that the hybrid cloud proxy solution was functioning as designed.

---

# 8. Lessons Learned

This troubleshooting exercise reinforced several important infrastructure concepts:

* A connected VPN tunnel does not always guarantee successful application communication.
* NAT policies can significantly affect traffic flow across VPN connections.
* Source IP preservation is critical in hybrid cloud networking.
* Troubleshooting should follow a structured, layered approach by validating each component individually before identifying the root cause.

The incident also demonstrated the importance of understanding how firewall NAT policies interact with AWS networking and IPSec VPN connectivity.

---

# 9. Outcome

After replacing the MASQ policy with Source NAT (SNAT), the hybrid cloud architecture functioned successfully.

Office users were able to securely route traffic through the AWS-hosted Squid Proxy using the Site-to-Site VPN, while client applications recognized the AWS public IP address as the source of outbound requests.

The issue was fully resolved without requiring changes to the AWS infrastructure or the Squid Proxy configuration.
