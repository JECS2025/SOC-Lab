# pfSense Installation – IronGate Solutions Lab

## VM Setup (VMware Workstation Pro)
- **Name**: pfSense-FW
- **CPU**: 1 vCPU, 1 core
- **RAM**: 1–2 GB
- **Disk**: 10 GB, single file
- **Network Adapters**:
  - NIC1 → **WAN** → VMnet8 (NAT)
  - NIC2 → **Transit** → VMnet2 (/31 link to OPNsense)

![VM Settings](../../assets/pfSense/vm_settings.png)
*Figure 1 – pfSense VM setup in VMware Workstation.*

---

## ISO Boot
1. Boot VM from `pfSense-CE-<version>-amd64.iso`.
2. Select **Install pfSense**.

![Installer Start](../../assets/pfSense/installer_start.png)
*Figure 2 – Installer start screen.*

![Installer Progress](../../assets/pfSense/installer_progress1.png)
![Installer Progress](../../assets/pfSense/installer_progress2.png)

---

## Interface Assignments

![LAN Assignment](../../assets/pfSense/interface_assignment.png)
*Figure 4 – LAN (em1) assigned to transit.*

![LAN Config](../../assets/pfSense/lan_assignment.png)
*Figure 5 – LAN configured as 172.16.0.2/31, DHCP disabled.*

![Install CE](../../assets/pfSense/cont_install.png)

![Console Assignments](../../assets/pfSense/console_assignments.png)
*Figure 6 – Console showing final assignments.*

![Console IP Config](../../assets/pfSense/console_ip_config.png)
*Figure 7 – Reconfiguring LAN IP.*

![Console DHCP Disable](../../assets/pfSense/console_dhcp_disable.png)
*Figure 8 – DHCP disabled on LAN interface.*

![Console Summary](../../assets/pfSense/console_summary.png)
*Figure 9 – Post-install summary: WAN (DHCP), LAN 172.16.0.2/31.*

---

## Final State
- pfSense acts as **ISP edge** only.
- **WAN (em0)** → DHCP from VMnet8/NAT.
- **LAN (em1)** → 172.16.0.2/31 (transit to OPNsense).
- Default rules left in place (LAN allow all, WAN block inbound).
