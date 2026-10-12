# Configuration Change Log

**Project:** Ozark Precision Manufacturing  
**Adapted and tested by:** Cody Phillips  
**Date:** October 12, 2026

## Starting Point

This project began with a provided Packet Tracer topology and baseline configuration.

Existing features included VLANs, routing, HSRP, spanning tree, static EtherChannel, DHCP, NAT, voice services, and a Branch 2 IPsec VPN.

This log separates my customization and configuration changes from validation of those existing features.

## Company Customization

- Adapted the scenario to fictional Ozark Precision Manufacturing.
- Updated topology labels for headquarters, the Branch 1 Production Office, and the Branch 2 Distribution Office.
- Updated company-device banners to identify the fictional portfolio lab and my adaptation work.
- Standardized selected device hostnames.

| Original Hostname | Updated Hostname |
|---|---|
| Access-SW1 | HQ-ASW1 |
| Access-SW2 | HQ-ASW2 |
| Access-SW3 | HQ-ASW3 |
| HQ-Voice | HQ-VOICE-RTR |
| B1-SW | B1-SW1 |

## Wireless Customization

| Wireless Network | Updated SSID |
|---|---|
| HQ corporate | Ozark-Corporate |
| HQ guest | Ozark-Guest |
| Branch 1 corporate | Ozark-B1-Corporate |

Clients were reconnected to the updated SSIDs. Existing WPA2-PSK/AES settings were retained.

The HQ corporate tablet and both Branch 1 tablets successfully reached the HQ server and simulated internet.

## Guest Access Control

### Finding

The INTERNET-ONLY ACL was defined but was not applied inbound to the guest VLAN interface. Initial testing allowed guest access to the HQ server.

### Change

Applied the existing ACL to VLAN 172 on both HQ core switches:

    interface vlan 172
     ip access-group INTERNET-ONLY in

### Validation

- Guest access to the tested HQ and branch hosts was blocked.
- Guest access to the simulated internet succeeded.
- HQ-server blocking and simulated internet access were checked again after the guest SSID rename.

## Temporary HSRP Test Changes

- Temporarily shut down VLAN 192 on HQ-CORE-SW1.
- Restored the interface with no shutdown.
- Temporarily changed VLAN 192 priorities to move the active gateway role to HQ-CORE-SW2.
- Restored the original VLAN 192 priorities: Core 1 at 255 and Core 2 at 0.

Priority-based takeover passed connectivity testing.

The separate VLAN-interface shutdown test produced inconsistent state output and unsuccessful virtual-gateway forwarding. Its cause remains unresolved.

The intended HSRP roles and baseline connectivity were restored.

## Temporary EtherChannel Test Changes

- Temporarily shut down GigabitEthernet0/1 on HQ-CORE-SW1.
- Confirmed Po1 remained in use with GigabitEthernet0/2 bundled.
- Verified inter-core ping connectivity.
- Restored GigabitEthernet0/1 and confirmed both members were bundled.

The existing static EtherChannel configuration was retained.

## Branch 2 VPN Troubleshooting

Initial HQ-to-Branch-2 management ping and SSH attempts timed out.

An extended ping from B2-RTR used source 192.168.130.1 and destination 192.168.110.50. After this traffic initiated the management-subnet security associations, NOC ping and SSH access succeeded.

No VPN configuration change was made during this troubleshooting sequence.

Fresh-start initiation behavior and bidirectional counter validation remain open items.

## Validation of Existing Features

Additional testing covered:

- HQ and branch wired connectivity
- Inter-branch connectivity
- HQ NAT/PAT translations
- Spanning-tree root and alternate-port states
- Local and inter-site simulated phone calls
- SSH logins from the NOC management PC
- Denied SSH attempts from tested data-VLAN PCs

Detailed results are recorded in [test-results.md](test-results.md).

## Portfolio Documentation

- Updated README with addressing, features, validation, and known issues.
- Updated test-results.md with completed tests and limitations.
- Uploaded the updated Ozark_Precision_Network.pkt file.
- Linked proposal documents, topology, and screenshot evidence.
- Updated GitHub profile information and pinned the repository.
- Added a direct repository link to LinkedIn Featured.

## Final State

Temporary shutdowns and priority changes were reversed. Device configurations and the Packet Tracer project were saved.

The project remains a fictional simulation adapted from a supplied starter lab. Successful tests and unresolved issues are documented separately.
