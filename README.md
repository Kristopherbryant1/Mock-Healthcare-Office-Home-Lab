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

## 🔧 pfSense VLAN Setup

1. **Create VLANs**
   - Navigate to: `Interfaces > Assignments > VLANs`
   - Create the following VLANs on the parent interface `igb1` (LAN):
     - VLAN 10 – Servers
     - VLAN 20 – Clinical
     - VLAN 30 – WiFi
     - VLAN 40 – IoT

2. **Assign VLAN Interfaces**
   - Go to: `Interfaces > Assignments`
   - Add and enable each VLAN interface
   - Assign the following static IP addresses:

   | VLAN | Interface Name      | IP Address       |
   |------|---------------------|------------------|
   | 10   | VLAN10_Servers      | 192.168.10.1     |
   | 20   | VLAN20_Clinical     | 192.168.20.1     |
   | 30   | VLAN30_WiFi         | 192.168.30.1     |
   | 40   | VLAN40_IoT          | 192.168.40.1     |

3. **Enable DHCP**
   - Navigate to: `Services > DHCP Server`
   - Enable DHCP for each VLAN interface with limited ranges  
     Example:
     - VLAN 10: `192.168.10.100 – 192.168.10.199`
     - VLAN 20: `192.168.20.100 – 192.168.20.199`
     - VLAN 30: `192.168.30.100 – 192.168.30.199`
     - VLAN 40: `192.168.40.100 – 192.168.40.199`

4. **Add Basic Firewall Rules**
   - Go to: `Firewall > Rules`, select each VLAN interface
   - Add the following rules:
     - Allow DNS (UDP/53) to pfSense interface
     - Allow DHCP (UDP/67,68)
     - Allow HTTP/HTTPS (TCP/80,443) to WAN
   - Block inter-VLAN traffic by default to enforce segmentation

---

## 🧰 TP-Link TL-SG108E VLAN Configuration

> ⚠️ Ensure the switch firmware is up to date to support full 802.1Q VLAN tagging.

1. **Access the Switch Management Interface**
   - Use TP-Link Easy Smart Configuration Utility **or** browser (`http://192.168.0.1`)
   - Log in with admin credentials

2. **Create VLANs**
   - Navigate to `802.1Q VLAN > VLAN Config`
   - Add VLANs 10, 20, 30, and 40

3. **Configure Port VLAN Membership**

   | Port | Connected To           | Tagged VLANs   | Untagged VLAN | PVID |
   |------|------------------------|----------------|----------------|------|
   | 1    | pfSense LAN (igb1)     | 10,20,30,40     | —              | 1    |
   | 2    | Future Server 1        | —               | 10             | 10   |
   | 3    | Future Server 2        | —               | 10             | 10   |
   | 4    | Clinical Workstation   | —               | 20             | 20   |
   | 5    | Wireless Access Point  | 30 (Tagged)     | —              | 1    |
   | 6    | Printer / IoT Device   | —               | 40             | 40   |
   | 7–8  | Spare / future use     | As needed       | —              | —    |

- ✅ **Port 1** is the **trunk port** carrying all VLANs to/from pfSense  
- 📌 Each device port is an **access port** allowing only one untagged VLAN

---

## 📌 Result Summary

- ✅ pfSense is routing VLANs 10, 20, 30, and 40
- ✅ DHCP and internet access confirmed per VLAN
- ✅ TP-Link switch pre-configured for expected devices
- 🔒 Inter-VLAN traffic is blocked (default deny)
- 🧱 Network is now ready for:
  - Server deployment (VLAN 10)
  - Clinical workstation(s) (VLAN 20)
  - WiFi access point(s) (VLAN 30)
  - IoT printers/devices (VLAN 40)

---

## 📅 What's Next – Phase 4: Windows Server & EHR

In the next phase:

- ⚙️ Install **Windows Server** on VLAN 10
- 🧱 Set up **Active Directory**, **DNS**, and domain structure
- 💻 Configure workstations to join the domain (VLAN 20)
- 🏥 Deploy **EHR software** for testing/learning
- 🔐 Implement Group Policy and security practices

Stay tuned for full server builds and EHR deployment in Phase 4.
