# OPNsense Installation – IronGate Solutions Lab (virt-manager / libvirt)

This guide installs and configures **OPNsense** as the internal firewall for the IronGate lab using **QEMU/KVM + virt-manager (libvirt)**.

OPNsense routes the internal segments (Infra/Users/SecOps/Red/DMZ) and sends upstream traffic over a transit link to **pfSense**, which provides internet access.

---

## Prereqs

You should already have these libvirt networks created and active:

- `default` (NAT) — **pfSense WAN only**
- `transit-net` (isolated)
- `infra-net` (isolated)
- `users-net` (isolated)
- `secops-net` (isolated)
- `red-net` (isolated)
- `dmz-net` (isolated)

> OPNsense should **not** have a NIC on `default`. Only pfSense uses `default` for WAN.

---

## VM Build (virt-manager)

**Name:** `OPNsense-FW`  
**CPU/RAM:** 2 vCPU, 2–4 GB RAM recommended  
**Disk:** 20 GB qcow2 (SATA bus recommended for easiest detection)

### NICs (order matters)

Add **six** NICs (Model: `virtio`) in this order:

1. NIC1 → `transit-net`  *(OPNsense WAN / transit to pfSense)*
2. NIC2 → `infra-net`    *(LAN / Server Infrastructure)*
3. NIC3 → `users-net`    *(OPT1 / User Endpoints)*
4. NIC4 → `secops-net`   *(OPT2 / Security Ops)*
5. NIC5 → `red-net`      *(OPT3 / Red Team)*
6. NIC6 → `dmz-net`      *(OPT4 / DMZ)*

---

## Install OPNsense

1. Boot the VM from the OPNsense ISO.
2. Login to the live environment:
   - **user:** `installer`
   - **pass:** `opnsense`
3. Run the installer:
   - `opnsense-installer`
4. Choose:
   - **Install:** UFS (recommended in VMs)
   - **Target disk:** `ada0` (your 20GB disk) — **not** `cd0`
   - Partitioning: defaults are fine
5. Set a root password (avoid special characters if you suspect keyboard-layout issues).
6. Reboot when complete.
7. In virt-manager, **disconnect the ISO** so the VM boots from disk.

---

## Interface Assignment (Console)

From the OPNsense console menu:

### 1) Assign interfaces
Menu: **1) Assign interfaces**

- **LAGGs?** `n`
- **VLANs?** `n`  
  (We are not using 802.1Q tagging. Each segment is a separate libvirt “switch”.)

Then assign:

- **WAN** = the interface connected to `transit-net`
- **LAN** = the interface connected to `infra-net`
- **OPT1** = `users-net`
- **OPT2** = `secops-net`
- **OPT3** = `red-net`
- **OPT4** = `dmz-net`

### How to identify which `vtnetX` is which
On KVM, interfaces appear as `vtnet0`, `vtnet1`, etc. The fastest accurate method is MAC matching:

- In virt-manager → VM Details → click each NIC → note its **Network source** and **MAC**
- In OPNsense console, match the MAC to the `vtnetX` list

---

## IP Configuration (Console)

Menu: **2) Set interface IP address**

### Transit (WAN → pfSense)

**We are using /30 transit in this build.**

- pfSense transit IP: `172.16.0.2/30`
- OPNsense transit IP: `172.16.0.1/30`
- Gateway for OPNsense WAN: `172.16.0.2`

Configure WAN:

- IPv4: Static
- **OPNsense WAN IP:** `172.16.0.1`
- **CIDR:** `30`
- **Upstream gateway:** `172.16.0.2`
- “Use gateway as DNS?” → `n` (set DNS later in GUI)
- IPv6: none (skip)

> Why not `172.16.0.3/30`?  
> In `172.16.0.0/30`, `.3` is the broadcast address. Usable hosts are `.1` and `.2`.

### Internal segments

Only WAN gets a gateway. For LAN/OPTs, leave gateway blank.

- **LAN (infra-net):** `192.168.20.1/24` (gateway: none)
- **OPT1 (users-net):** `192.168.30.1/24` (gateway: none)
- **OPT2 (secops-net):** `192.168.40.1/24` (gateway: none)
- **OPT3 (red-net):** `192.168.50.1/24` (gateway: none)
- **OPT4 (dmz-net):** `192.168.60.1/24` (gateway: none)

DHCP on LAN/OPTs: optional (you can enable later per segment in the GUI).

---

## Validation (Phase 1 Success)

From the OPNsense console:

- Menu **7) Ping host** → `172.16.0.2` ✅ (pfSense transit)
- Menu **7) Ping host** → `1.1.1.1` ✅ (internet via pfSense)

If `172.16.0.2` works but `1.1.1.1` fails:
- pfSense outbound NAT/rules are the usual culprit
- or missing return routes (later) if you change routing design

---

## Web GUI Access (Important)

Your internal networks (`infra-net`, `users-net`, etc.) are **isolated** in libvirt, so your **host OS** can’t automatically browse to `https://192.168.20.1`.

Recommended options:

### Option A (lab-pure)
Use a **SecOps VM** on `secops-net` and browse internally to:
- `https://192.168.20.1` (or whichever interface you want to manage from)

### Option B (temporary convenience only)
Add a temporary management NIC to OPNsense on libvirt `default` (NAT), configure, then remove it.

---

## Final State

- OPNsense routes internal segments:
  - Infra `192.168.20.0/24` (gw `.1`)
  - Users `192.168.30.0/24` (gw `.1`)
  - SecOps `192.168.40.0/24` (gw `.1`)
  - Red `192.168.50.0/24` (gw `.1`)
  - DMZ `192.168.60.0/24` (gw `.1`)
- OPNsense WAN transit:
  - `172.16.0.1/30` → gateway `172.16.0.2` (pfSense)
- Upstream internet access flows:
  - Internal segments → OPNsense → pfSense → NAT (`default`) → Internet
