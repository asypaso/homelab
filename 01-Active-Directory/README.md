```markdown
# Project 1: Active Directory Domain Setup

## Objective

Build a fully functional Active Directory environment simulating a small corporate network with centralized user management, group policies, shared resources, and domain-joined workstations.

## Environment

| Component | Details |
|-----------|---------|
| Hypervisor | VirtualBox 7.0 |
| Host OS | Windows 11 |
| Server | Windows Server 2022 Standard Evaluation (DC01) |
| Workstation | Windows 10 Enterprise Evaluation (WS01) |
| Network | Internal Network "LabNetwork", 10.0.1.0/24 |
| Domain | andersonlab.local |

## Network Diagram



---

## Step 1: VirtualBox Installation

**Date completed:** ___________

### What I did:
- Downloaded and installed VirtualBox 7.0 from virtualbox.org
- Confirmed successful launch of VirtualBox Manager

### Screenshot:
!VirtualBox Manager (copy and paste the URL into your browser): screenshots/01-virtualbox-installed.png

### Notes:


---

## Step 2: Windows Server 2022 VM Creation

**Date completed:** ___________

### What I did:
- Created new VM named "DC01"
- Allocated 4GB RAM, 2 CPUs, 60GB dynamic disk
- Configured Adapter 1: Internal Network "LabNetwork"
- Configured Adapter 2: NAT (for internet access)
- Attached Windows Server 2022 ISO

### Screenshot:
!DC01 VM Settings (copy and paste the URL into your browser): screenshots/02-dc01-vm-settings.png

### VM Configuration:

| Setting | Value |
|---------|-------|
| Name | DC01 |
| OS Type | Windows 2022 (64-bit) |
| RAM | 4096 MB |
| CPUs | 2 |
| Disk | 60 GB (Dynamic) |
| Network 1 | Internal Network - LabNetwork |
| Network 2 | NAT |

### Notes:


---

## Step 3: Windows Server Installation

**Date completed:** ___________

### What I did:
- Booted VM from ISO
- Selected "Windows Server 2022 Standard Evaluation (Desktop Experience)"
- Completed installation and set Administrator password

### Screenshots:
!Edition Selection (copy and paste the URL into your browser): screenshots/03-edition-selection.png

!Server Desktop (copy and paste the URL into your browser): screenshots/04-server-first-login.png

### Notes:


---

## Step 4: Static IP Configuration

**Date completed:** ___________

### What I did:
- Opened Network Adapter properties
- Configured static IPv4 settings:
  - IP:[IP_ADDRESS]0
  - Subnet:[IP_ADDRESS]0
  - DNS: 127.0.0.1 (loopback — this server will be the DNS server)
- Verified with `ipconfig /all`

### Screenshots:
!Static IP Config (copy and paste the URL into your browser): screenshots/05-static-ip-config.png

!ipconfig verification (copy and paste the URL into your browser): screenshots/06-ipconfig-verify.png

### Verification:


### Notes:


---

## Step 5: Server Rename

**Date completed:** ___________

### What I did:
- Renamed server from default to "DC01"
- Restarted to apply changes

### Screenshot:
!Server Renamed (copy and paste the URL into your browser): screenshots/07-server-renamed.png

### Notes:


---

## Step 6: Active Directory Domain Services Installation

**Date completed:** ___________

### What I did:
- Opened Server Manager → Add Roles and Features
- Installed "Active Directory Domain Services" role
- Installed "DNS Server" role
- Installation completed successfully

### Screenshots:
!AD DS Role Selection (copy and paste the URL into your browser): screenshots/08-adds-role-selection.png

!Installation Complete (copy and paste the URL into your browser): screenshots/09-adds-install-complete.png

### Notes:


---

## Step 7: Domain Controller Promotion

**Date completed:** ___________

### What I did:
- Clicked notification flag → "Promote this server to a domain controller"
- Selected "Add a new forest"
- Root domain name: andersonlab.local
- Set DSRM recovery password
- Completed promotion and server restarted
- Login screen now shows ANDERSONLAB\Administrator

### Screenshots:
!New Forest Config (copy and paste the URL into your browser): screenshots/10-new-forest-config.png

!Promotion Success (copy and paste the URL into your browser): screenshots/11-promotion-prerequisites.png

!Domain Login Screen (copy and paste the URL into your browser): screenshots/12-domain-login.png

### Verification:


### Notes:


---

## Step 8: Organizational Unit (OU) Structure

**Date completed:** ___________

### What I did:
- Opened Active Directory Users and Computers
- Created the following OU structure under andersonlab.local:



### Screenshot:
!OU Structure (copy and paste the URL into your browser): screenshots/13-ou-structure.png

### Design decisions:
- Separated users by department for targeted GPO application
- Dedicated Workstations OU for computer-specific policies
- Service_Accounts OU to isolate non-human accounts for security

### Notes:


---

## Step 9: User Account Creation

**Date completed:** ___________

### What I did:
- Created user accounts in appropriate OUs:

| User | Login | OU | Purpose |
|------|-------|----|---------|
| Anderson Admin | a.admin | IT_Department | Admin account |
| Help Desk | h.desk | IT_Department | Help desk user |
| John Smith | j.smith | Warehouse_Staff | Standard user |
| Jane Doe | j.doe | Warehouse_Staff | Standard user |
| Bob Jones | b.jones | Warehouse_Staff | Standard user |

### Screenshots:
!User Creation (copy and paste the URL into your browser): screenshots/14-user-creation.png

!Users in OUs (copy and paste the URL into your browser): screenshots/15-users-in-ous.png

### Notes:


---

## Step 10: Security Group Creation

**Date completed:** ___________

### What I did:
- Created security groups and assigned members:

| Group | OU | Members |
|-------|-----|---------|
| IT_Admins | IT_Department | a.admin, h.desk |
| Warehouse_Users | Warehouse_Staff | j.smith, j.doe, b.jones |
| VPN_Access | IT_Department | a.admin |

### Screenshot:
!Group Members (copy and paste the URL into your browser): screenshots/16-group-members.png

### Notes:


---

## Step 11: Group Policy Objects (GPOs)

**Date completed:** ___________

### What I did:
- Created and linked the following GPOs:

| GPO Name | Linked To | What It Does |
|----------|-----------|--------------|
| Default Domain Policy | andersonlab.local | Password policy: [PASSWORD], complexity, 90-day max age |
| Restrict Control Panel | Warehouse_Staff OU | Blocks access to Control Panel for warehouse users |
| Map Drives | Warehouse_Staff OU | Maps S:\ drive to \\DC01\SharedFiles |

### Screenshots:
!GPO Management (copy and paste the URL into your browser): screenshots/17-gpo-management.png

!Password Policy (copy and paste the URL into your browser): screenshots/18-password-policy.png

!Control Panel GPO (copy and paste the URL into your browser): screenshots/19-control-panel-gpo.png

!Drive Map GPO (copy and paste the URL into your browser): screenshots/20-drive-map-gpo.png

### Notes:


---

## Step 12: Shared Folder Setup

**Date completed:** ___________

### What I did:
- Created C:\SharedFiles on DC01
- Shared as "SharedFiles"
- Set share permissions: Warehouse_Users → Read & Change
- Configured NTFS permissions to match

### Screenshots:
!Share Permissions (copy and paste the URL into your browser): screenshots/21-share-permissions.png

!NTFS Permissions (copy and paste the URL into your browser): screenshots/22-ntfs-permissions.png

### Notes:


---

## Step 13: Windows 10 Workstation VM Setup

**Date completed:** ___________

### What I did:
- Created WS01 VM (2GB RAM, 50GB disk, Internal Network)
- Installed Windows 10 Enterprise
- Configured static IP:
  - IP: 10.0.1.20
  - Subnet:[IP_ADDRESS]0
  - DNS:[IP_ADDRESS]0 (points to DC01)

### Screenshots:
!WS01 Settings (copy and paste the URL into your browser): screenshots/23-ws01-settings.png

!WS01 IP Config (copy and paste the URL into your browser): screenshots/24-ws01-ip-config.png

### VM Configuration:

| Setting | Value |
|---------|-------|
| Name | WS01 |
| OS Type | Windows 10 (64-bit) |
| RAM | 2048 MB |
| CPUs | 2 |
| Disk | 50 GB (Dynamic) |
| Network | Internal Network - LabNetwork |

### Notes:


---

## Step 14: Domain Join

**Date completed:** ___________

### What I did:
- On WS01: System → Rename this PC (advanced) → Change → Domain
- Entered: andersonlab.local
- Authenticated with ANDERSONLAB\Administrator
- Received "Welcome to the andersonlab.local domain" message
- Restarted WS01

### Screenshots:
!Domain Join (copy and paste the URL into your browser): screenshots/25-domain-join.png

!Domain Join Success (copy and paste the URL into your browser): screenshots/26-domain-join-success.png

### Verification:


### Notes:


---

## Step 15: GPO Verification & Testing

**Date completed:** ___________

### What I did:
- Logged into WS01 as ANDERSONLAB\j.smith
- Ran `gpupdate /force` and `gpresult /r`
- Tested each GPO:

| Test | Expected Result | Actual Result | Pass/Fail |
|------|----------------|---------------|-----------|
| Drive mapping | S:\ mapped to \\DC01\SharedFiles | | |
| Control Panel blocked | "Access Denied" when opening | | |
| Password policy | Weak password rejected on reset | | |
| Domain login | j.smith can log in on WS01 | | |
| File access | j.smith can read/write to S:\ | | |

### Screenshots:
!GPResult Output (copy and paste the URL into your browser): screenshots/27-gpresult.png

!Mapped Drive (copy and paste the URL into your browser): screenshots/28-mapped-drive.png

!Control Panel Blocked (copy and paste the URL into your browser): screenshots/29-control-panel-blocked.png

### Verification Commands:


### Notes:


---

## Step 16: IT Scenarios Practiced

**Date completed:** ___________

### Scenario A: New Employee Onboarding
**Task:** Onboard a new warehouse employee named "Mike Wilson"

**Steps taken:**
1.
2.
3.

**Result:**

**Screenshot:**
!New User Onboarding (copy and paste the URL into your browser): screenshots/30-new-user-onboard.png

---

### Scenario B: Password Reset
**Task:** User j.smith forgot their password

**Steps taken:**
1.
2.
3.

**Result:**

**Screenshot:**
!Password Reset (copy and paste the URL into your browser): screenshots/31-password-reset.png

---

### Scenario C: Employee Offboarding
**Task:** Employee b.jones is leaving the company

**Steps taken:**
1.
2.
3.

**Result:**

**Screenshot:**
!User Offboarding (copy and paste the URL into your browser): screenshots/32-user-offboard.png

---

### Scenario D: GPO Troubleshooting
**Task:** A user reports they can still access Control Panel

**Steps taken:**
1.
2.
3.

**Result:**

**Screenshot:**
!GPO Troubleshooting (copy and paste the URL into your browser): screenshots/33-gpo-troubleshoot.png

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

- ✅ Windows Server 2022 deployment and configuration
- ✅ Active Directory Domain Services (AD DS) installation and promotion
- ✅ DNS server configuration
- ✅ Organizational Unit (OU) design and implementation
- ✅ User account lifecycle management (create, modify, disable, reset)
- ✅ Security group creation and membership management
- ✅ Group Policy Object (GPO) creation, linking, and troubleshooting
- ✅ Shared folder configuration with NTFS and share permissions
- ✅ Windows 10 workstation domain join
- ✅ Network troubleshooting using ipconfig, nslookup, gpresult
- ✅ Systematic documentation and change management

## Key Commands Used

| Command | Purpose |
|---------|---------|
| `ipconfig /all` | Verify IP and DNS configuration |
| `nslookup andersonlab.local` | Confirm DNS resolution to DC |
| `ping[IP_ADDRESS]0` | Test connectivity to DC01 |
| `gpupdate /force` | Force Group Policy refresh |
| `gpresult /r` | View applied GPOs on a machine |
| `whoami` | Confirm logged-in user and domain |
| `systeminfo \| findstr /i "domain"` | Verify domain membership |

---

## Next Steps

- [ ] Phase 2: Add pfSense firewall and configure VLANs
- [ ] Phase 2: Set up DHCP on pfSense for multiple subnets
- [ ] Phase 3: Install Wazuh SIEM and configure log collection
- [ ] Phase 3: Simulate security incidents and document response
- [ ] Phase 4: Deploy cloud resources on AWS/Azure fr
