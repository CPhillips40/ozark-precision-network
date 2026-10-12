# Network Test Results

**Project:** Ozark Precision Manufacturing  
**Adapted and tested by:** Cody Phillips  
**Test date:** October 12, 2026  
**Environment:** Cisco Packet Tracer

## Project Overview

Ozark Precision Manufacturing is a fictional company used for a networking portfolio project covering headquarters and two branch offices.

The project was adapted from a provided starter topology and configuration. Work included company branding, device naming, guest access controls, and manual connectivity and feature validation.

All internet tests refer to the simulated internet inside Packet Tracer.

## Wired Connectivity

| Source | Destination | Purpose | Observed Result | Status |
|---|---|---|---|---|
| HQ-PC1 | 192.168.10.100 | HQ virtual gateway | 4/4 replies | Passed |
| HQ-PC1 | 192.168.10.50 | HQ server | 4/4 replies | Passed |
| HQ-PC1 | 8.8.8.8 | Simulated internet | 4/4 replies on repeat | Passed |
| B1-PC1 | 192.168.20.1 | Branch 1 gateway | 4/4 replies | Passed |
| B1-PC1 | 192.168.10.50 | HQ server | 4/4 replies | Passed |
| B1-PC1 | 8.8.8.8 | Simulated internet | 4/4 replies | Passed |
| B2-PC1 | 192.168.30.1 | Branch 2 gateway | 4/4 replies | Passed |
| B2-PC1 | 192.168.10.50 | HQ server | 4/4 replies on repeat | Passed |
| B2-PC1 | 8.8.8.8 | Simulated internet | 4/4 replies | Passed |
| B2-PC1 | 192.168.20.103 | Branch 1 PC | 4/4 replies | Passed |

## NAT / PAT Verification

On HQ-INET-RTR, `show ip nat translations` showed ICMP translations from HQ-PC1's private address, 192.168.10.102, to the outside interface address, 201.45.66.98.

**Status: Passed.** PAT translation and simulated internet connectivity were observed for the tested HQ client.

## Corporate Wireless

Corporate wireless settings used WPA2-PSK with AES. SSIDs were personalized, and clients reconnected.

| Client | SSID | HQ Server: 192.168.10.50 | Simulated Internet: 8.8.8.8 |
|---|---|---|---|
| HQ corporate tablet | Ozark-Corporate | 4/4 replies | 4/4 replies |
| B1-Tablet1 | Ozark-B1-Corporate | 4/4 replies | 4/4 replies |
| B1-Tablet2 | Ozark-B1-Corporate | 4/4 replies | 4/4 replies |

**Status: Passed.** All listed corporate wireless clients reached both test destinations.

These tests verify association and connectivity. Incorrect-password rejection was not tested.

## Guest Access Control

Guest-Tablet received the following settings:

| Setting | Value |
|---|---|
| IPv4 address | 172.16.10.101 |
| Subnet mask | 255.255.255.0 |
| Default gateway | 172.16.10.100 |
| DNS server | 8.8.8.8 |
| Personalized SSID | Ozark-Guest |

### Problem and Change

Initial testing showed that Guest-Tablet could reach the HQ server.

The INTERNET-ONLY ACL existed but was not applied inbound to the guest VLAN interface. The following configuration was applied to both HQ core switches:

```text
interface vlan 172
 ip access-group INTERNET-ONLY in
```

### Results

| Destination | Expected Behavior | Observed Result | Status |
|---|---|---|---|
| HQ server: 192.168.10.50 | Block access | Unreachable; no successful replies | Passed |
| B1-PC1: 192.168.20.103 | Block access | Unreachable; no successful replies | Passed |
| B2-PC1: 192.168.30.101 | Block access | Unreachable; no successful replies | Passed |
| Simulated internet: 8.8.8.8 | Allow access | 4/4 replies | Passed |

After the guest SSID was renamed, simulated internet access and HQ-server blocking were verified again.

These results verify the listed ICMP tests. They do not establish isolation for every protocol or destination.

## HSRP Gateway Redundancy

### Normal Roles

| VLAN | Purpose | Virtual Gateway | Active Device | Standby Device |
|---|---|---|---|---|
| 10 | Voice | 10.10.10.100 | HQ-CORE-SW2 | HQ-CORE-SW1 |
| 100 | Management | 192.168.110.100 | HQ-CORE-SW1 | HQ-CORE-SW2 |
| 172 | Guest | 172.16.10.100 | HQ-CORE-SW2 | HQ-CORE-SW1 |
| 192 | Data | 192.168.10.100 | HQ-CORE-SW1 | HQ-CORE-SW2 |

### Priority-Based Takeover — Passed

Temporary priority changes moved the VLAN 192 active role from HQ-CORE-SW1 to HQ-CORE-SW2.

| Check | Observed Result |
|---|---|
| HQ-CORE-SW2 VLAN 192 state | Active |
| HQ-CORE-SW1 VLAN 192 state | Standby |
| HQ-PC1 → virtual gateway | 4/4 replies on repeat |
| HQ-PC1 → simulated internet | 4/4 replies |
| Simulation inspection | HQ-CORE-SW2 handled the virtual-gateway ICMP request |

Original priorities were restored. HQ-CORE-SW1 returned to Active for VLAN 192.

**Status: Passed.** Priority-based role transfer and client connectivity were demonstrated.

### VLAN Interface Shutdown — Unresolved

A separate test temporarily shut down VLAN 192 on HQ-CORE-SW1.

During one attempt, HQ-CORE-SW2 reported Active, but HQ-PC1 could not reach the virtual gateway. The physical Core 2 address remained reachable.

| Check | Observed Result |
|---|---|
| HQ-PC1 → 192.168.10.2 | 4/4 replies |
| HQ-PC1 → 192.168.10.100 | 0/4 replies |
| HQ-PC1 → 8.8.8.8 during failed attempt | 0/4 replies |

Clearing ARP and dynamic MAC entries did not resolve that attempt. Later attempts also produced inconsistent interface and HSRP state output.

**Status: Unresolved.** Reliable forwarding during VLAN-interface shutdown was not demonstrated. The cause was not established.

### Restoration

VLAN 192 was restored, temporary priorities were removed, and the intended roles were re-established.

- Virtual gateway: 4/4 replies on repeat.
- Simulated internet: 4/4 replies on repeat.
- Device configurations were saved.

This testing does not establish recovery from a complete core-switch failure.

## EtherChannel

Both HQ core switches reported:

```text
Po1(SU)    Gig0/1(P) Gig0/2(P)
```

- `S`: Layer 2 port-channel.
- `U`: Port-channel in use.
- `P`: Interface bundled in the port-channel.
- The configuration used static EtherChannel with `channel-group 1 mode on`.

### Member-Link Shutdown Test

GigabitEthernet0/1 on HQ-CORE-SW1 was temporarily shut down.

| Check | Observed Result |
|---|---|
| Port-channel state | Po1 remained SU |
| GigabitEthernet0/1 | Down |
| GigabitEthernet0/2 | Remained bundled |
| Core 1 → 192.168.10.2 | 4/5 initially; 5/5 on repeat |

GigabitEthernet0/1 was restored. Both members returned to `(P)`.

**Status: Passed.** The port-channel remained operational with one member down, and inter-core connectivity continued.

Other Layer 2 paths were not isolated during this test.

## Spanning Tree

VLAN 192 output showed:

| Device / Interface | Observed State |
|---|---|
| HQ-CORE-SW1 | Root bridge |
| HQ-ASW1 Fa0/23 toward Core 1 | Root / Forwarding |
| HQ-ASW1 Fa0/24 toward Core 2 | Alternate / Blocking |

**Status: Passed.** The expected baseline root bridge and redundant-uplink states were observed.

A separate access-switch uplink failure test was not completed.

## Voice Call Validation

| Source Extension | Destination Extension | Observed Result | Status |
|---|---|---|---|
| 1001 | 1002 | Rang and connected | Passed |
| 1001 | 2001 | Rang and connected | Passed |
| 1001 | 3001 | Rang and connected | Passed |
| 2001 | 3001 | Rang and connected | Passed |

These tests verified simulated call setup within HQ and across sites. Audio quality and QoS performance were not measured.

## SSH Management Access

### Allowed Management Access

Successful SSH logins were tested from NOC-PC, 192.168.110.50.

| Device | Management Address | Result |
|---|---|---|
| HQ-CORE-SW1 | 192.168.110.1 | Successful login |
| HQ-CORE-SW2 | 192.168.110.2 | Successful login |
| HQ-INET-RTR | 192.168.110.253 | Successful login |
| HQ-WAN-RTR | 192.168.110.254 | Successful login |
| HQ-VOICE-RTR | 192.168.110.10 | Successful login |
| HQ-ASW1 | 192.168.110.241 | Successful login |
| HQ-ASW2 | 192.168.110.242 | Successful login |
| HQ-ASW3 | 192.168.110.243 | Successful login |
| B1-RTR | 192.168.120.1 | Successful login |
| B1-SW1 | 192.168.120.254 | Successful login |
| B2-RTR | 192.168.130.1 | Successful login after VPN initiation |
| B2-SW1 | 192.168.130.254 | Successful login |

### Restricted Data-Client Access

| Source | Tested Targets | Observed Result |
|---|---|---|
| HQ-PC1 | HQ core switches, routers, and access switches | Connections closed or refused |
| B1-PC1 | B1-RTR and B1-SW1 | Connections closed or refused |
| B2-PC1 | B2-RTR and B2-SW1 | Connections closed or refused |

**Status: Passed for the tested flows.** Management access succeeded from the NOC PC, while the listed data-client attempts were denied.

A complete audit of all VTY lines and source networks was not performed.

## Branch 2 IPsec VPN

### Initial Issue

HQ-to-Branch-2 management pings and SSH attempts initially timed out.

The management-subnet security association initially showed no active ESP entries and zero encryption/decryption counters.

### Traffic Initiation

An extended ping was initiated from B2-RTR:

| Parameter | Value |
|---|---|
| Source address | 192.168.130.1 |
| Destination address | 192.168.110.50 |
| Result | 4/5 replies |

After Branch 2 initiated traffic:

- NOC-PC ping to 192.168.130.1 returned 4/4 replies.
- NOC-PC successfully logged in to B2-RTR through SSH.
- Management-subnet ESP security associations showed ACTIVE on both peers.
- Corresponding inbound and outbound SPI values matched between the peers.

### Status and Limitations

**Status: Connectivity passed after Branch 2 initiated traffic.**

The cause of the initial HQ-initiated failure remains unresolved. No VPN configuration change was made during this troubleshooting sequence.

Displayed management-subnet decapsulation counters remained zero despite successful connectivity. Bidirectional counter validation and VPN initiation after a fresh start were not completed.

## Evidence

Results reflect manually observed CLI output, endpoint ping responses, and Packet Tracer GUI behavior.

Screenshots are included in the repository where available. Not every test has a separate screenshot.

## Final State

- Temporary test shutdowns were reversed.
- Original HSRP priorities were restored.
- EtherChannel members returned to the bundled state.
- Device configurations were saved.
- Corporate and guest SSIDs were personalized.
- Tested connectivity was working after restoration.

## Remaining Work

- Investigate and repeat the VLAN 192 interface-shutdown failover test.
- Verify Branch 2 VPN initiation from HQ after a fresh start.
- Validate VPN encryption and decryption counter increases in both directions.
- Optionally test access-switch uplink failure and recovery.
- Review all VTY lines and replace starter credentials before any production use.

## Scope

This is a fictional portfolio simulation adapted from a supplied starter lab.

The results demonstrate the listed connectivity, access-control, wireless, voice, and redundancy tests. They do not establish production readiness, a complete security audit, measured QoS performance, or full-device failure recovery.
