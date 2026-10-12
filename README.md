# Ozark Precision Manufacturing — Network Portfolio

A Cisco Packet Tracer networking portfolio for a fictional manufacturing company with headquarters and two branch offices.

**Portfolio owner:** Cody Phillips  
**Status:** Customized and tested; known issues documented  
**Last updated:** October 12, 2026

## Project Background

This project was adapted from a provided starter topology and configuration.

My work included personalizing the company scenario, updating device hostnames and banners, applying guest access controls, testing network services, troubleshooting connectivity, and documenting results.

The lab represents business IT networking. Industrial control systems are outside its scope.

## Network Topology

![Ozark Precision Manufacturing network topology](Network-topology-map.png)

| Location | Role |
|---|---|
| Headquarters | Shared services, core switching, management, corporate and guest wireless, and voice |
| Branch 1 — Production Office | Business IT clients, wireless tablets, and IP phones |
| Branch 2 — Distribution Office | Business IT clients and IP phones, with IPsec connectivity to HQ |

## Network Features

- Data, voice, management, and guest VLANs
- Inter-VLAN routing
- Redundant HQ core switches with HSRP
- PVST spanning tree
- Static EtherChannel between HQ core switches
- DHCP addressing
- WPA2-PSK/AES corporate and guest wireless
- Static routing and Branch 1 BGP routes
- Branch 2 site-to-site IPsec VPN
- NAT/PAT for simulated internet access
- Guest VLAN access controls
- SSH management access restrictions
- Simulated IP phone calls

All internet and provider networks are simulated inside Packet Tracer.

## IP Addressing

| Site | VLAN | Purpose | Subnet | Gateway |
|---|---|---|---|---|
| HQ | 10 | Voice | 10.10.10.0/24 | 10.10.10.100 |
| HQ | 100 | Management | 192.168.110.0/24 | 192.168.110.100 |
| HQ | 172 | Guest | 172.16.10.0/24 | 172.16.10.100 |
| HQ | 192 | Data | 192.168.10.0/24 | 192.168.10.100 |
| Branch 1 | 10 | Voice | 10.10.20.0/24 | 10.10.20.1 |
| Branch 1 | 100 | Management | 192.168.120.0/24 | 192.168.120.1 |
| Branch 1 | 192 | Data | 192.168.20.0/24 | 192.168.20.1 |
| Branch 2 | 10 | Voice | 10.10.30.0/24 | 10.10.30.1 |
| Branch 2 | 100 | Management | 192.168.130.0/24 | 192.168.130.1 |
| Branch 2 | 192 | Data | 192.168.30.0/24 | 192.168.30.1 |

HQ gateway addresses are HSRP virtual IPs.

## Validation Results

| Area | Observed Result |
|---|---|
| Wired connectivity | Tested HQ and branch clients reached their gateways, the HQ server, and simulated internet |
| Inter-branch connectivity | B2-PC1 reached B1-PC1 with 4/4 replies |
| NAT/PAT | HQ client translation observed on HQ-INET-RTR |
| Corporate wireless | HQ tablet and both Branch 1 tablets reached the HQ server and simulated internet |
| Guest isolation | Tested internal destinations blocked; simulated internet remained reachable |
| HSRP priority-based takeover | Core 2 assumed the data-gateway role, and client connectivity passed |
| EtherChannel member shutdown | Port-channel stayed up with one member down; inter-core connectivity continued |
| Spanning tree | Expected VLAN 192 root and alternate-uplink states observed |
| Voice calls | Tested HQ and inter-site calls rang and connected |
| SSH management | NOC logins succeeded; tested data-client attempts were denied |
| Branch 2 VPN | Management ping and SSH succeeded after Branch 2 initiated traffic |

[Read the complete test results and limitations](docs/test-results.md).

## Troubleshooting Highlights

### Guest Access Control

The INTERNET-ONLY ACL existed but was initially not applied to the guest VLAN interface.

Applying it inbound to VLAN 172 on both HQ core switches blocked the tested internal destinations while preserving simulated internet access.

### HSRP Gateway Redundancy

Priority-based takeover successfully moved the VLAN 192 active gateway role to Core 2.

A separate VLAN-interface shutdown test produced unsuccessful virtual-gateway forwarding and inconsistent state output. That test remains unresolved.

The intended HSRP roles and client connectivity were restored after testing.

### Branch 2 VPN

Initial HQ-to-Branch-2 management traffic timed out.

An extended ping initiated from Branch 2 established management-subnet security associations. Subsequent NOC ping and SSH access succeeded.

The cause of the initial failure and VPN behavior after a fresh start remain under investigation.

## Download and Open the Lab

[Download Ozark_Precision_Network.pkt](Ozark_Precision_Network.pkt)

1. Open the file link and use GitHub's download button.
2. Open the downloaded file in Cisco Packet Tracer.
3. Select Realtime mode and allow the network to converge.
4. Review the topology and follow the documented tests.

## Project Documents

- [Network Proposal](Ozark_Network_Proposal.pdf)
- [Network Topology Document](Ozark_Network_Topology.pdf)
- [Network Topology Image](Network-topology-map.png)
- [Detailed Test Results](docs/test-results.md)

The proposal describes the project design. The test-results document records observed implementation behavior and remaining issues.

## Screenshot Evidence

- [HQ Gateway Ping](hq-gateway-ping.png)
- [HQ Server Ping](hq-server-ping.png)
- [Simulated Internet Ping](hq-internet-ping.png)
- [NAT Translations](hq-nat-translations.png)

Screenshots cover selected tests. Additional results were recorded from manual CLI and GUI observations.

## Skills Demonstrated

- Cisco IOS configuration review and customization
- VLANs, access ports, and trunking
- Inter-VLAN and branch routing
- HSRP role verification and troubleshooting
- Spanning-tree verification
- Static EtherChannel validation
- NAT/PAT verification
- ACL application and connectivity testing
- SSH management access testing
- Wireless connectivity validation
- IPsec VPN troubleshooting
- Technical documentation and evidence collection

## Known Issues and Next Improvements

- Resolve and repeat VLAN 192 interface-shutdown failover testing.
- Verify HQ-initiated Branch 2 VPN connectivity after a fresh start.
- Validate VPN encryption and decryption counter increases in both directions.
- Expand access-switch uplink failure and recovery testing.
- Review all VTY lines and replace starter credentials.

Full-device failure recovery, audio quality, and QoS performance were not validated.

## Contact

**Cody Phillips**

- Email: [chphillips40@gmail.com](mailto:chphillips40@gmail.com)
- GitHub: [CPhillips40](https://github.com/CPhillips40)

## Attribution and Scope

The original topology and baseline configuration were supplied as a starter lab. This repository documents my adaptation, configuration changes, testing, and troubleshooting.

Ozark Precision Manufacturing is fictional. This portfolio does not represent company employment or a production deployment.

Results apply to the documented Packet Tracer tests and do not establish production readiness or a complete security audit.
