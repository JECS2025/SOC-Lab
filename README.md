# IronGate Solutions
<p align="center">
  <img src="./assets/IronGate_Solutions_Logo_128.png" alt="IronGate Solutions Logo" width="128"/>
</p>

This project contains the full design and configuration of a replicated virtualized Security Operations Center (SOC) lab environment; simulating a real-world enterprise network environment, designed to test detection and defense capabilities against simulated attacks.

## ⚙️ Project Overview
- 🔐 **Segmented network architecture** using dual firewalls (pfSense as edge, OPNsense as internal)
- 🧨 **Red Team Adversary Zone** for simulating attacks with Kali Linux and MITRE Caldera
- 🌐 **DMZ environment** with a vulnerable web application (OWASP Juice Shop) for external threat emulation
- 📊 **Centralized logging and SIEM** setup with Wazuh, REMnux, and DFIR tooling
- 🔎 **Multiple log sources and detection paths** for end-to-end monitoring and response


## Diagram
![Lab Diagram](./assets/lab-diagram1.jpg)

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


⚠️ Disclaimer
This lab is for educational and authorized testing purposes only. All malware samples, attack tools, and techniques should only be used in controlled environments and/or with explicit permission.
