# 🛡️ SOC Homelab – Build Tracker

This document tracks the progress of the SOC Homelab build across all phases.

---

## Phase 1 – Network & Segmentation
- [x] **Create VMnet Interfaces** – Set up VMnet switches (VMnet3–VMnet7) for Infra, Users, SecOps, Attack, DMZ, plus NAT (VMnet8) and Transit (VMnet2).
- [x] **Deploy pfSense** – Configure WAN (VMnet8), OPT1 (VMnet2), and assign `172.16.0.2/31` on OPT1.
- [x] **Deploy OPNsense** – Configure WAN (VMnet2 – `172.16.0.3/31`) and LAN interfaces (VMnet3–VMnet7).
- [ ] **Configure Static Routes** – Route LAN traffic from OPNsense through pfSense and enable outbound access.
- [ ] **Test Segmentation** – Confirm each segment (Infra, Users, etc.) is isolated and routed through firewalls.
- [x] **Document Diagram & IP Plan** – Finalize the network diagram and static IP assignments for each segment.

---

## Phase 2 – Core Infrastructure
- [ ] **Deploy Windows Server (Infra VLAN)** – Assign static IP (e.g., `192.168.20.101`) and promote to Domain Controller.
- [ ] **Configure DNS & DHCP** – Serve DNS for internal domains; configure DHCP or static mappings if needed.
- [ ] **Create OUs & User Accounts** – Build out your AD structure: users, computers, security groups, etc.
- [ ] **Deploy Syslog Server (Infra VLAN)** – Install Ubuntu with rsyslog/syslog-ng (e.g., `192.168.20.102`).
- [ ] **Domain Join Workstation (User VLAN)** – Join at least one Windows 11 client to the AD domain.
- [ ] **Baseline GPOs** – Apply logging, password policies, disable guest, etc.

---

## Phase 3 – Security Operations
- [ ] **Deploy Wazuh (SecOps VLAN)** – Use Wazuh Manager to collect and analyze security logs.
- [ ] **Deploy REMnux, Tsurugi (DFIR tools)** – Install tools for malware and forensic analysis.
- [ ] **Log Forwarding** – Configure Windows, firewalls, and syslog to forward logs to Wazuh.
- [ ] **Alerting & Detection** – Set up rules for failed logins, privilege use, lateral movement, etc.
- [ ] **Test Baseline Logging** – Simulate logins, GPO changes, user activity, and validate ingestion.

---

## Phase 4 – Threat Simulation
- [ ] **Deploy OWASP Juice Shop (DMZ VLAN)** – Host vulnerable web app in VMnet7 (e.g., `192.168.60.101`).
- [ ] **Port-Forward on pfSense** – NAT WAN traffic from VMnet8 to DMZ target.
- [ ] **Deploy Honeypot (optional)** – Add another VM to DMZ or separate Honeypot VLAN using T-Pot, Cowrie, etc.
- [ ] **SIEM Integration** – Ingest Juice Shop logs and alerts into Wazuh.
- [ ] **Trigger Attacks** – Begin simple attack paths from Red Team VLAN (adv-atk-01 / 02).

---

## Phase 5 – Red vs Blue
- [ ] **Launch Recon & Exploits (Red Team VLAN)** – Use Kali Linux, Caldera, BloodHound, CrackMapExec.
- [ ] **Simulate Attacks** – Kerberoasting, Pass-the-Hash, RDP brute force, etc.
- [ ] **Log Detection & Alerting** – Ensure all events are detected in SIEM and alerts trigger correctly.
- [ ] **Containment Measures** – Block IPs, disable accounts, simulate response playbooks.
- [ ] **Track MTTD / MTTR** – Measure time to detect and time to respond to incidents.
- [ ] **Final Documentation** – Write up outcomes, screenshots, and lessons learned.
