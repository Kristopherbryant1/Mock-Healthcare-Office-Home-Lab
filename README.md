# 🏥 Mock Healthcare Office Home Lab – Phases 1–3

**Purpose:**  
Build a secure, realistic healthcare IT lab environment at home using a pfSense firewall, managed VLANs, and segmented networking to simulate a production-grade healthcare office. This infrastructure supports future deployment of Windows Servers, EHR software, clinical PCs, and IoT devices (e.g., printers).

---

## 📦 Overview

| Component         | Description                                      |
|------------------|--------------------------------------------------|
| Firewall          | pfSense on Qotom Q10722C                         |
| Switch            | TP-Link TL-SG108E (Smart Managed)                |
| Network Backbone  | In-wall Cat6 Ethernet (5-foot wall run)          |
| VLANs             | Servers, Clinical, WiFi, IoT                     |
| Devices           | Not deployed yet — lab network ready for them    |

---

## 🔧 Phase 1 – In-Wall Ethernet Installation

### 🎯 Objective  
Establish a clean, stable Ethernet connection between the main router and the lab room using a short Cat6 in-wall cable run.

### 🛠 Materials Used

- 5 ft Cat6 solid copper, in-wall-rated cable  
- Keystone jacks (T568B wiring)  
- Single-gang wall plates  
- Punch-down tool  
- Fish tape  
- Drill & hole saw  
- Ethernet patch cables  
- Cable tester

### 🧰 Installation Steps

1. **Drill wall access holes** in the interior wall between router and lab.
2. **Run Cat6 cable** through wall cavity using fish tape.
3. **Terminate** each end to keystone jacks (T568B), mount in wall plates.
4. **Test cable** with tester to confirm continuity and pinout.
5. **Verify gigabit link** between router and lab.

### ✅ Result

- Stable, high-speed wired connection to lab.
- Wall-mounted jacks for a clean setup.
- Ready for firewall and switch deployment.

---

## 🔒 Phase 2 – pfSense Firewall Installation

### 🎯 Objective  
Install and configure pfSense on a dedicated Qotom Q10722C appliance to act as the lab's main firewall and router.

### 🛠 Hardware & Tools

- Qotom Q10722C (6x Intel NICs)  
- pfSense ISO image  
- USB stick (bootable)  
- Monitor + keyboard (for install)  
- Ethernet cables

### 🧰 Installation Process

1. **Connect monitor + keyboard** to Qotom.  
2. **Boot pfSense installer** from USB.  
3. Install to internal mSATA SSD using UFS guided install.  
4. Assign interfaces:
   - `igb0` → WAN (main router)
   - `igb1` → LAN (to TP-Link switch)
5. Set LAN IP temporarily to `192.168.1.1`.
6. Log into pfSense Web GUI at `http://192.168.1.1`.

### 🔧 DNS Resolver Setup

- Navigate: `Services > DNS Resolver`  
- Enable DNS Resolver  
- Enable DHCP registration  
- Disable Forwarding Mode for full resolver functionality

### ✅ Result

- pfSense installed and reachable via browser.
- WAN and LAN interfaces operational.
- DNS, DHCP, and routing ready for VLANs.

---

## 🌐 Phase 3 – VLAN Configuration & Segmented Networking

### 🎯 Objective  
Segment the lab network using VLANs to separate server traffic, clinical workstations, wireless devices, and IoT equipment.

---

### 🔁 VLAN Design

| VLAN Name  | VLAN ID | Subnet           | Description                    |
|------------|---------|------------------|--------------------------------|
| Mgmt       | 1       | 192.168.1.0/24   | Default VLAN (temporary use)   |
| Servers    | 10      | 192.168.10.0/24  | For future Windows servers     |
| Clinical   | 20      | 192.168.20.0/24  | For clinical PCs/workstations  |
| WiFi       | 30      | 192.168.30.0/24  | Wireless clients (AP/SSID)     |
| IoT        | 40      | 192.168.40.0/24  | Printers / IoT devices         |

---

### 🔧 pfSense VLAN Configuration

1. **Create VLANs** under `Interfaces > Assignments > VLANs`, using parent interface `igb1`.
2. **Assign Interfaces** and set static IPs:

```text
VLAN 10 → 192.168.10.1  
VLAN 20 → 192.168.20.1  
VLAN 30 → 192.168.30.1  
VLAN 40 → 192.168.40.1
