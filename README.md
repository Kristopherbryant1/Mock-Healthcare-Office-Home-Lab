# Mock-Healthcare-Office-Home-Lab
Home lab replicating a healthcare office setup with pfSense firewall, VLAN segmentation, Windows Server, and EHR. Focused on network security, server config, and real-world IT workflows.

---

## Phase 1 – Hardwired Network Installation (5-Foot Cat6 Wall Run)

### Objective
Create a clean, hardwired Ethernet connection between the main router and the home lab room using a short in-wall Cat6 cable run. This ensures a stable, high-speed link for all devices in the healthcare IT lab.

### Materials & Tools Used

| Item              | Description                            |
|-------------------|----------------------------------------|
| Cat6 Ethernet Cable | Solid copper, in-wall rated – 5 ft   |
| Cat6 Keystone Jacks | T568B wiring standard                |
| Wall Plates         | Single-gang                          |
| Punch-down Tool     | For cable termination                |
| Fish Tape           | For cable routing                    |
| Drill / Hole Saw    | For wall access holes                |
| Cable Tester        | For connectivity verification        |
| Patch Cables        | Router/lab device connection         |

### Installation Process

#### Wall Access Preparation
- Drilled clean access holes on both sides of the interior wall.

#### Cable Routing
- Used fish tape to guide 5' Cat6 cable through the wall cavity.
- Verified smooth pull with no sharp bends or snags.

#### Termination
- Terminated both ends with Cat6 keystone jacks using T568B wiring.
- Installed and secured wall plates.

#### Testing
- Used a cable tester to confirm pinout and continuity.
- Verified 1 Gbps link status from connected devices.

### Results
- Stable, high-speed connection between router and lab.
- Clean wall-mounted jacks, no exposed wiring.
- Ready for switch and workstation integration.

### Lessons Learned
- Even short cable runs benefit from clean wall installs.
- Re-terminated one pair due to punch-down misalignment.
- Always test every run before declaring success.

---

## Phase 2 – pfSense Firewall Installation (Qotom Q10722C)

### Objective
Deploy pfSense on a dedicated Qotom Q10722C appliance to serve as the primary firewall and router for the lab network. This enables VLAN segmentation, traffic control, and secure remote access.

### Materials & Tools Used

| Item             | Description                            |
|------------------|----------------------------------------|
| Qotom Q10722C     | Fanless PC with 6x Intel NICs         |
| pfSense ISO       | Official installer (Netgate)          |
| USB Drive         | Bootable install media                |
| Monitor & Keyboard| Temporary setup                       |
| Ethernet Cables   | For WAN and LAN connections           |

### Installation Process

#### 1. Hardware Setup
- Connected monitor, keyboard, and bootable USB.
- Plugged in:
  - **Port 1 (igb0)** – WAN (from main router)
  - **Port 2 (igb1)** – LAN (to lab switch)

#### 2. pfSense Installation
- Booted USB, selected **UFS Guided Install**.
- Installed to internal mSATA SSD.
- Rebooted and removed install media.

#### 3. Initial Configuration
- Assigned interfaces:
  - **WAN**: igb0
  - **LAN**: igb1 (IP: 192.168.1.1/24)
- Set admin password.
- Accessed pfSense web GUI from lab workstation.

#### 4. Post-Install Setup
- Applied basic outbound NAT/firewall rules.
- Updated system and packages.
- Enable DNS Resolver
1. Log in to the pfSense Web GUI

Open your browser and go to: http://192.168.1.1 (or your LAN IP)

Log in with the admin credentials

2. Go to Services > DNS Resolver



**Navigation:** `Services > DNS Resolver`

| Setting                          | Value                          | Notes                                                  |
|----------------------------------|--------------------------------|--------------------------------------------------------|
| **Enable DNS Resolver**          | ✅ Enabled                     | Turns on the resolver service                          |
| **Listen Port**                  | `53` (default)                 | Leave as default unless using alternate port           |
| **Network Interfaces**           | LAN + All VLANs                | Select interfaces where clients will query DNS         |
| **Outgoing Network Interfaces**  | WAN or default                 | Used for external lookups                              |
| **Enable DNSSEC Support**        | Optional                       | Can be enabled or disabled; not required               |
| **DHCP Registration**            | ✅ Enabled                     | Registers DHCP clients in DNS                          |
| **Static DHCP Mapping**          | ✅ Enabled                     | Registers static mappings (e.g., servers)              |
| **Enable Forwarding Mode**       | 🔁 Disabled                    | Disable to allow full resolver functionality           |

Click **Save** and then **Apply Changes** if prompted.



### Results
- pfSense operational and accessible via browser.
- WAN/LAN fully functional.
- Network ready for VLANs, VPNs, monitoring, etc.

### Lessons Learned
- NIC port identification required testing (label after setup).
- Adjusted BIOS to boot from USB first.
- Backups are critical before config changes.

---

## Next Phase: VLAN Setup & EHR Deployment

Stay tuned for Phase 3 where VLANs are configured on both pfSense and the managed switch, followed by EHR installation on a Windows Server.


