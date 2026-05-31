```markdown
# Project 2: Network Segmentation & Firewall Configuration

## Objective

Implement network segmentation using pfSense as a firewall/router to separate a corporate network into distinct security zones (Corporate, Servers, Guest) with controlled access between them — simulating a real enterprise network architecture.

## Environment

| Component | Details |
|-----------|---------|
| Hypervisor | VirtualBox 7.0 |
| Firewall | pfSense CE (FreeBSD-based) |
| Server | Windows Server 2022 (DC01) — from Phase 1 |
| Workstation | Windows 10 Enterprise (WS01) — from Phase 1 |
| Guest Test PC | Windows 10 (GuestPC) |
| Networks | 3 zones — Corporate, Servers, Guest |

## Network Diagram


                [Internet]
                    |
          ┌─────────────────┐
          │    pfSense       │
          │  WAN: NAT        │
          │  LAN: [IP_ADDRESS]   │
          │  OPT1: 10.0.2.1  │
          │  OPT2: 10.0.3.1  │
          └─────────────────┘
               |     |     |
     ┌─────────┘     |     └─────────┐
     |               |               |


## Firewall Rule Matrix

| Source | Destination | Action | Reason |
|--------|-------------|--------|--------|
| Corporate | Servers | ✅ Allow | Users need AD, file shares |
| Corporate | Internet | ✅ Allow | Users need web access |
| Servers | Internet | ✅ Allow | Updates, patches |
| Guest | Internet | ✅ Allow | Web browsing only |
| Guest | Servers | ❌ Block | Guests can't touch internal resources |
| Guest | Corporate | ❌ Block | Guests isolated from corporate |

---

## Step 1: VirtualBox Network Setup

**Date completed:** ___________

### What I did:
- Created three Internal Networks in VirtualBox:
  - CorpNet (Corporate zone)
  - ServerNet (Server zone)
  - GuestNet (Guest zone)

### Notes:

---

## Step 2: pfSense VM Creation

**Date completed:** ___________

### What I did:
- Created pfSense VM (FreeBSD 64-bit, 1GB RAM, 10GB disk)
- Configured 4 network adapters:

| Adapter | Type | Network | Role |
|---------|------|---------|------|
| 1 | NAT | — | WAN (internet) |
| 2 | Internal Network | CorpNet | LAN (corporate) |
| 3 | Internal Network | ServerNet | OPT1 (servers) |
| 4 | Internal Network | GuestNet | OPT2 (guest) |

### Screenshot:
!pfSense VM Network Settings(screenshots/01-pfsense-vm-settings.png)

### Notes:

---

## Step 3: pfSense Installation

**Date completed:** ___________

### What I did:
- Booted from pfSense ISO
- Installed using Auto (ZFS) partitioning
- Removed ISO and rebooted

### Screenshot:
!pfSense First Boot(screenshots/02-pfsense-first-boot.png)

### Notes:

---

## Step 4: Interface Assignment & IP Configuration

**Date completed:** ___________

### What I did:
- Assigned interfaces:

| Interface | Adapter | IP Address | DHCP Range |
|-----------|---------|------------|------------|
| WAN | em0 | DHCP (from NAT) | — |
| LAN | em1 | [IP_ADDRESS]/24 | [IP_ADDRESS]00 - 10.0.1.200 |
| OPT1 (Servers) | em2 | 10.0.2.1/24 | [IP_ADDRESS]0 - 10.0.2.200 |
| OPT2 (Guest) | em3 | 10.0.3.1/24 | 10.0.3.100 - 10.0.3.200 |

### Screenshot:
!pfSense Interface Summary(screenshots/03-interface-summary.png)

### Verification:


### Notes:

---

## Step 5: Migrate Existing VMs to New Networks

**Date completed:** ___________

### What I did:
- Moved DC01 from "LabNetwork" to "ServerNet"
- Updated DC01 IP configuration:

| Setting | Old Value | New Value |
|---------|-----------|-----------|
| IP Address | [IP_ADDRESS]0 | [IP_ADDRESS] |
| Subnet | 255.255.255.0 | 255.255.255.0 |
| Gateway | (none) | 10.0.2.1 |
| DNS | 127.0.0.1 | 127.0.0.1 |

- Moved WS01 from "LabNetwork" to "CorpNet"
- Updated WS01 IP configuration:

| Setting | Old Value | New Value |
|---------|-----------|-----------|
| IP Address | 10.0.1.20 | 10.0.1.20 (same) |
| Subnet | 255.255.255.0 | 255.255.255.0 |
| Gateway | (none) | [IP_ADDRESS] |
| DNS | [IP_ADDRESS]0 | [IP_ADDRESS] |

### Screenshots:
!DC01 New IP Config(screenshots/04-dc01-new-ip.png)
!WS01 New IP Config(screenshots/05-ws01-new-ip.png)

### Notes:

---

## Step 6: DNS Forwarding Configuration

**Date completed:** ___________

### What I did:
- On DC01: DNS Manager → Forwarders → added 10.0.2.1 and 8.8.8.8
- Verified external DNS resolution from both DC01 and WS01

### Screenshot:
!DNS Forwarders(screenshots/06-dns-forwarders.png)

### Verification:


### Notes:

---

## Step 7: pfSense Web Interface Setup

**Date completed:** ___________

### What I did:
- Accessed pfSense GUI from WS01 at [IP_ADDRESS]
- Completed setup wizard
- Changed default admin password
- Renamed OPT1 to "Servers" and OPT2 to "Guest"

### Screenshots:
!pfSense Dashboard(screenshots/07-pfsense-dashboard.png)
!Interface Renamed(screenshots/08-interfaces-renamed.png)

### Notes:

---

## Step 8: Firewall Rules — Corporate (LAN)

**Date completed:** ___________

### What I did:
- Created rules allowing Corporate to reach Servers and Internet:

| # | Action | Source | Destination | Port | Description |
|---|--------|--------|-------------|------|-------------|
| 1 | Pass | LAN net | Servers net | Any | Allow Corporate to Servers |
| 2 | Pass | LAN net | Any | Any | Allow Corporate to Internet |

### Screenshot:
!LAN Firewall Rules(screenshots/09-lan-rules.png)

### Notes:

---

## Step 9: Firewall Rules — Servers (OPT1)

**Date completed:** ___________

### What I did:
- Created rules for server zone:

| # | Action | Source | Destination | Port | Description |
|---|--------|--------|-------------|------|-------------|
| 1 | Pass | Servers net | Any | 80, 443 | Allow Servers HTTP/HTTPS out |
| 2 | Pass | Servers net | LAN net | Any | Allow Servers to respond to Corporate |

### Screenshot:
!Servers Firewall Rules(screenshots/10-servers-rules.png)

### Notes:

---

## Step 10: Firewall Rules — Guest (OPT2)

**Date completed:** ___________

### What I did:
- Created rules isolating Guest from internal networks:

| # | Action | Source | Destination | Port | Description |
|---|--------|--------|-------------|------|-------------|
| 1 | Block | Guest net | LAN net | Any | Block Guest to Corporate |
| 2 | Block | Guest net | Servers net | Any | Block Guest to Servers |
| 3 | Pass | Guest net | Any | 80, 443, 53 | Allow Guest Internet + DNS |

**Rule order matters — blocks ABOVE the pass rule.**

### Screenshot:
!Guest Firewall Rules(screenshots/11-guest-rules.png)

### Notes:

---

## Step 11: Connectivity Testing

**Date completed:** ___________

### Test Results:

#### From WS01 (Corporate — [IP_ADDRESS]):

| Test | Command | Expected | Actual | Pass/Fail |
|------|---------|----------|--------|-----------|
| Reach DC01 | `ping [IP_ADDRESS]` | Reply | | |
| Reach Internet | `ping 8.8.8.8` | Reply | | |
| Reach Gateway | `ping [IP_ADDRESS]` | Reply | | |
| DNS Resolution | `nslookup google.com` | Resolves | | |
| AD Domain | `nslookup andersonlab.local` | Returns [IP_ADDRESS] | | |

#### From DC01 (Servers — [IP_ADDRESS]):

| Test | Command | Expected | Actual | Pass/Fail |
|------|---------|----------|--------|-----------|
| Reach Internet | `ping 8.8.8.8` | Reply | | |
| Reach Gateway | `ping 10.0.2.1` | Reply | | |

#### From GuestPC (Guest — [IP_ADDRESS]):

| Test | Command | Expected | Actual | Pass/Fail |
|------|---------|----------|--------|-----------|
| Reach Internet | `ping 8.8.8.8` | Reply | | |
| Reach DC01 | `ping [IP_ADDRESS]` | **BLOCKED** | | |
| Reach WS01 | `ping 10.0.1.20` | **BLOCKED** | | |
| DNS Resolution | `nslookup google.com` | Resolves | | |

### Screenshots:
!Corporate Connectivity Tests(screenshots/12-corporate-tests.png)
!Guest Blocked Tests(screenshots/13-guest-blocked.png)

### Notes:

---

## Step 12: Verify Active Directory Still Works

**Date completed:** ___________

### Test Results:

| Test | Action | Expected | Actual | Pass/Fail |
|------|--------|----------|--------|-----------|
| Domain Login | Log in as j.smith on WS01 | Success | | |
| GPO Application | `gpupdate /force` | Completes | | |
| GPO Verification | `gpresult /r` | Shows GPOs | | |
| Shared Drive | Open S:\ | Accessible | | |
| Control Panel | Open Control Panel as j.smith | Blocked | | |

### Screenshot:
!AD Working After Migration(screenshots/14-ad-still-works.png)

### Notes:

---

## Step 13: Firewall Log Review

**Date completed:** ___________

### What I did:
- Reviewed pfSense logs at Status → System Logs → Firewall
- Confirmed blocked and allowed traffic matches my rules

### Observations:
| Log Entry | Source | Destination | Action | Expected? |
|-----------|--------|-------------|--------|-----------|
| | | | | |
| | | | | |
| | | | | |

### Screenshot:
!Firewall Logs(screenshots/15-firewall-logs.png)

### Notes:

---

## Issues Encountered & Resolutions

### Issue 1:
- **Symptom:**
- **What I tried first:**
- **Root cause:**
- **Resolution:**
- **What I learned:**

### Issue 2:
- **Symptom:**
- **What I tried first:**
- **Root cause:**
- **Resolution:**
- **What I learned:**

### Issue 3:
- **Symptom:**
- **What I tried first:**
- **Root cause:**
- **Resolution:**
- **What I learned:**

---

## Skills Demonstrated

- ✅ pfSense firewall deployment and configuration
- ✅ Network segmentation design (Corporate / Servers / Guest zones)
- ✅ DHCP server configuration across multiple subnets
- ✅ Firewall rule creation and ordering (allow/deny)
- ✅ DNS forwarding and resolution chain configuration
- ✅ Inter-VLAN routing and access control
- ✅ Port forwarding / NAT configuration
- ✅ Firewall log analysis and traffic verification
- ✅ Network migration with zero downtime to Active Directory
- ✅ Systematic connectivity testing methodology

## Key Commands Used

| Command | Purpose |
|---------|---------|
| `ping [IP]` | Test basic connectivity between zones |
| `tracert [IP]` | Verify traffic path through pfSense |
| `ipconfig /all` | Confirm IP, gateway, DNS after migration |
| `nslookup [domain]` | Verify DNS resolution chain |
| `gpupdate /force` | Confirm GPOs still apply after network change |
| `gpresult /r` | View applied policies post-migration |
| `arp -a` | View MAC-to-IP mappings on local segment |

---

## Next Steps

- [ ] Phase 3: Install Wazuh SIEM on Servers network
- [ ] Phase 3: Configure log collection from DC01 and WS01
- [ ] Phase 3: Simulate security incidents
