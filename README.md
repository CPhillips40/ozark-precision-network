# Ozark Precision Manufacturing — Network Portfolio

A Cisco Packet Tracer lab for a fictional manufacturing company with three business locations.

**Portfolio owner:** Cody Phillips  
**Status:** Customization and testing in progress

## Project background

This project is adapted from a provided starter topology and configuration. My work focuses on personalizing the company scenario, reviewing the network, making configuration changes, and documenting test results.

## Network Topology

![Ozark Precision Manufacturing network topology](Network-topology-map.png)

## Locations

- Headquarters
- Production Office — Branch 1
- Distribution Office — Branch 2

## Network scope

The lab covers business IT networking, including:

- Data, voice, management, and guest VLANs
- Inter-VLAN routing
- Redundant HQ core switches
- HSRP, spanning tree, and EtherChannel
- DHCP and wireless access
- Branch connectivity and simulated provider networks
- IP phones and access controls

Feature operation will be documented as testing is completed.

## Opening the lab

[Download the Packet Tracer lab](Ozark_Precision_Network.pkt)

## Planned additions

- Customized Packet Tracer project
- Network proposal and topology documentation
- Configuration change log
- Screenshots and test results
  
## Verified test results

Tests performed from HQ-PC1 (192.168.10.102).

| Test | Destination | Result |
|---|---|---|
| HQ gateway connectivity | 192.168.10.100 | 4/4 replies, 0% loss |
| HQ server connectivity | 192.168.10.50 | 4/4 replies, 0% loss |
| Simulated Internet connectivity | 8.8.8.8 | 4/4 replies, 0% loss |
| NAT translation | 192.168.10.102 → 201.45.66.98 | Confirmed in router translation table |

### Test evidence

- [Gateway Ping](hq-gateway-ping.png)
- [Server Ping](hq-server-ping.png)
- [Simulated Internet Ping](hq-internet-ping.png)
- [NAT Translations](hq-nat-translations.png)

Branch connectivity, wireless security, guest isolation, voice calls,
and failover testing remain pending.
## Contact

Cody Phillips  
chphillips40@gmail.com

This is a fictional company portfolio lab. It does not represent a production deployment.
