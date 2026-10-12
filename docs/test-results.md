# Network Test Results

## Project Overview

Ozark Precision Manufacturing is a fictional company used for a Cisco Packet Tracer networking portfolio.

This project was adapted from a provided starter topology and configuration. These results document connectivity testing, guest access control changes, and an unresolved gateway failover issue.

All internet tests refer to the simulated internet inside Packet Tracer.

## Connectivity Tests

| Source | Destination | Purpose | Observed Result | Status |
|---|---|---|---|---|
| HQ-PC1 | 192.168.10.100 | HQ virtual gateway | 4/4 replies during baseline testing | Passed |
| HQ-PC1 | 192.168.10.50 | HQ server access | 4/4 replies | Passed |
| HQ-PC1 | 8.8.8.8 | Simulated internet access | 4/4 replies during baseline testing | Passed |
| B1-PC1 | 192.168.20.1 | Branch 1 gateway | 4/4 replies | Passed |
| B1-PC1 | 192.168.10.50 | Branch 1 to HQ server | 4/4 replies | Passed |
| B1-PC1 | 8.8.8.8 | Branch 1 simulated internet access | 4/4 replies | Passed |
| B2-PC1 | 192.168.30.1 | Branch 2 gateway | 4/4 replies | Passed |
| B2-PC1 | 192.168.10.50 | Branch 2 to HQ server | 4/4 replies on repeat test | Passed |
| B2-PC1 | 8.8.8.8 | Branch 2 simulated internet access | 4/4 replies | Passed |
| B2-PC1 | 192.168.20.103 | Branch 2 to Branch 1 | 4/4 replies | Passed |

## NAT Verification

On HQ-INET-RTR, `show ip nat translations` showed ICMP translations from HQ-PC1's private address, 192.168.10.102, to the outside interface address, 201.45.66.98.

This confirmed PAT operation for the tested HQ traffic.

## Guest Access Control

Guest-Tablet received:

- IP address: 172.16.10.101
- Subnet mask: 255.255.255.0
- Default gateway: 172.16.10.100
- DNS server: 8.8.8.8

### Problem and Change

Initial testing showed that Guest-Tablet could reach the HQ server.

The INTERNET-ONLY ACL was defined but was not applied inbound to the guest VLAN interface. It was applied to VLAN 172 on both HQ core switches:

    interface vlan 172
     ip access-group INTERNET-ONLY in

### Results After the Change

| Source | Destination | Expected Result | Observed Result | Status |
|---|---|---|---|---|
| Guest-Tablet | 192.168.10.50 | Block HQ server access | No successful replies; unreachable messages | Passed |
| Guest-Tablet | 192.168.20.103 | Block Branch 1 host access | No successful replies; unreachable messages | Passed |
| Guest-Tablet | 192.168.30.101 | Block Branch 2 host access | No successful replies; unreachable messages | Passed |
| Guest-Tablet | 8.8.8.8 | Allow simulated internet access | 4/4 replies | Passed |

These results verify the listed ICMP tests. They do not establish isolation for every protocol or destination.

## HSRP Failover Test — Unresolved

### Procedure

1. Confirmed Core 1 was Active for the VLAN 192 virtual gateway.
2. Temporarily shut down interface VLAN 192 on Core 1.
3. Confirmed Core 2 transitioned to Active.
4. Tested connectivity from HQ-PC1.

### Results

| Check | Observed Result |
|---|---|
| Core 2 HSRP state | Active for VLAN 192 |
| HQ-PC1 to Core 2 physical address, 192.168.10.2 | 4/4 replies |
| HQ-PC1 to virtual gateway, 192.168.10.100 | 0/4 replies |
| HQ-PC1 to simulated internet, 8.8.8.8 | 0/4 replies |

Clearing the PC's ARP cache and the access switch's dynamic MAC table did not restore virtual gateway connectivity.

### Restoration

Core 1's VLAN 192 interface was restored with `no shutdown`. The subsequent virtual gateway ping returned 3/4 replies.

### Status

HSRP control-plane takeover occurred, but successful client forwarding during failover was not demonstrated. The cause remains under investigation. Simulated internet connectivity after restoration still needs to be confirmed.

## Evidence

Screenshots accompany the project where available. Test results above reflect observed CLI output and endpoint ping results.

## Remaining Validation

- Resolve and repeat the HSRP failover test.
- Confirm simulated internet connectivity after restoration.
- Test voice calls.
- Verify wireless security settings.
- Verify EtherChannel and spanning-tree operation.
- Test SSH access restrictions.
