# Troubleshooting

## Issue

Users could connect using the public IP but failed when using the private IP over the VPN tunnel.

## Root Cause

 Firewall was performing MASQ, causing AWS to receive translated source addresses instead of the original client IP.

## Resolution

Updated the Source NAT policy to preserve the original client addressing over the VPN, allowing successful communication through the private IP.
