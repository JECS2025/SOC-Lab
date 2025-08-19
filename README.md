# SOC-Lab

This project contains the full design and configuration of a replicated virtualized Security Operations Center (SOC) lab environment.

## Lab Features
- Segmented networks via pfSense + OPNsense
- Red team adversary zone
- DMZ with OWASP Juice Shop
- Centralized SIEM with Wazuh
- Multiple attack detection paths and log flows

## Diagram
![Lab Diagram](C:\Users\J\Documents\GitHub\SOC-Lab\assets)

## 🧱 Network Segmentation

| Segment Name            | Purpose                        | VMnet  | Subnet            | Gateway IP     |
|-------------------------|--------------------------------|--------|-------------------|----------------|
| External (WAN)          | NAT to Internet                | VMnet8 | N/A (DHCP/NAT)    | Provided by host |
| Transit Link            | pfSense ↔ OPNsense link        | VMnet2 | 172.16.0.0/30     | 172.16.0.1 (pfSense) / 172.16.0.2 (OPNsense) |
| Server Infrastructure   | AD, Syslog servers             | VMnet3 | 192.168.20.0/24   | 192.168.20.1   |
| User Endpoints          | Workstations                   | VMnet4 | 192.168.30.0/24   | 192.168.30.1   |
| Security Operations     | SIEM, DFIR tools               | VMnet5 | 192.168.40.0/24   | 192.168.40.1   |
| Red Team Adversary Zone | Kali, Caldera (Isolated Ingress) | VMnet6 | 192.168.50.0/24 | 192.168.50.1   |
| DMZ                     | OWASP Juice Shop               | VMnet7 | 192.168.60.0/24   | 192.168.60.1   |


## Goals
- Practice SOC detection and response
- Simulate real-world attack scenarios
- Test firewall rules, SIEM, endpoint logging


