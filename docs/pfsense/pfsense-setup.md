# pfSense Installation – IronGate Solutions Lab (virt-manager / libvirt)

This guide installs and configures **pfSense CE** as the **edge firewall** for the IronGate lab using **QEMU/KVM + virt-manager (libvirt)**.

pfSense provides upstream internet access via libvirt **`default` (NAT)** and connects to OPNsense over a **transit link** on **`transit-net`**.

---

## Prereqs

Libvirt networks should already exist and be active:

- `default` (NAT) — pfSense **WAN**
- `transit-net` (isolated) — transit link to OPNsense

---

## VM Build (virt-manager)

**Name:** `pfSense-FW`  
**CPU/RAM:** 1–2 vCPU, 2 GB RAM recommended  
**Disk:** 10–20 GB qcow2

### NICs

Add **two** NICs (Model: `virtio`):

1. NIC1 → **WAN** → `default` (NAT)
2. NIC2 → **Transit** → `transit-net`

> On KVM, interfaces typically show as `vtnet0`, `vtnet1` (instead of `em0/em1`).  
> WAN is the NIC attached to `default`; Transit is the NIC attached to `transit-net`.

---

## Install pfSense CE

1. Boot the VM from the pfSense ISO.
2. Choose **Install pfSense** and follow defaults.
3. Reboot when installation finishes.
4. Disconnect the ISO so the VM boots from disk.

---

## Interface Assignment (Console)

From the pfSense console:

1) **Assign Interfaces**
- VLANs? **No** (we are not using 802.1Q tagging)
- **WAN** = interface attached to `default` (NAT)
- **LAN** = interface attached to `transit-net` (Transit to OPNsense)

If you’re unsure which is which:
- check the link status (WAN usually shows DHCP / upstream connectivity)
- or match MAC addresses shown in virt-manager to the console interface list

---

## IP Configuration (Console)

From the pfSense console:

2) **Set interface(s) IP address**

### WAN
- Configure IPv4 via DHCP: **Yes**
- (Leave WAN as DHCP from libvirt `default`)

### Transit (LAN)
This build uses **/30 transit**.

- **pfSense Transit IP:** `172.16.0.2/30`
- **OPNsense Transit IP:** `172.16.0.1/30`
- **Transit subnet:** `172.16.0.0/30`

Configure pfSense **LAN/Transit**:
- IPv4 address: `172.16.0.2`
- Subnet bit count: `30`
- Upstream gateway: **none** (press Enter)
- DHCP server on LAN: **No**
- IPv6: none (skip)

> Note: `/31` was attempted during rebuild and rejected by console validation, so `/30` is used to keep the transit simple and universally accepted.

---

## Validation

From pfSense console:

- **Ping 1.1.1.1** ✅ (confirms WAN/NAT works)
- After OPNsense is configured:
  - Ping **172.16.0.1** ✅ (OPNsense transit)

---

## Final State

- **Role:** pfSense operates as the upstream edge device.
- **WAN:** DHCP from libvirt `default` (external connectivity)
- **Transit (LAN):** `172.16.0.2/30` (link to OPNsense)
- **Default firewall behavior:**
  - WAN inbound blocked by default
  - LAN outbound allowed by default (stateful return traffic permitted)
