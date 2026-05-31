# Project 3: Security Monitoring & Incident Response

## Objective

Deploy a Security Information and Event Management (SIEM) system using Wazuh to collect, analyze, and alert on security events across the network — simulating a Security Operations Center (SOC) environment with real-time monitoring and incident response capabilities.

## Environment

| Component | Details |
|-----------|---------|
| Hypervisor | VirtualBox 7.0 |
| SIEM | Wazuh 4.x (Ubuntu Server VM) |
| Domain Controller | Windows Server 2022 (DC01) — from Phase 1 |
| Workstation | Windows 10 Enterprise (WS01) — from Phase 1 |
| Firewall | pfSense (from Phase 2) |
| Network Zone | Servers (10.0.2.0/24) |

## Network Diagram


          ┌─────────────────┐
          │    pfSense       │
          │  Firewall/Router │
          └─────────────────┘
               |     |     |
     ┌─────────┘     |     └─────────┐
     |               |               |


## What This Project Covers

| Skill Area | What You'll Do |
|------------|---------------|
| SIEM Deployment | Install and configure Wazuh server |
| Agent Management | Deploy agents to Windows Server and Workstation |
| Log Collection | Collect Windows Event Logs, Sysmon, and authentication logs |
| Alert Configuration | Set up rules for brute force, account lockout, privilege escalation |
| Incident Response | Detect, investigate, and document security events |
| Threat Simulation | Generate realistic attack scenarios to test detection |

---

## Step 1: Create Ubuntu Server VM for Wazuh

**Date completed:** ___________

### What I did:
- Created Ubuntu Server VM for Wazuh SIEM:

| Setting | Value |
|---------|-------|
| Name | WAZUH |
| OS Type | Ubuntu (64-bit) |
| RAM | 4096 MB (4GB) |
| CPUs | 2 |
| Disk | 50 GB (Dynamic) |
| Network | Internal Network - ServerNet |

### Screenshot:
!Wazuh VM Settings (copy and paste the URL into your browser): screenshots/01-wazuh-vm-settings.png

### Notes:


---

## Step 2: Install Ubuntu Server

**Date completed:** ___________

### What I did:
- Installed Ubuntu Server 22.04 LTS
- Set hostname: wazuh
- Created admin user
- Configured static IP: [IP_ADDRESS]/24, gateway 10.0.2.1, DNS 10.0.2.10

### Screenshot:
!Ubuntu Installed (copy and paste the URL into your browser): screenshots/02-ubuntu-installed.png

### Verification:


### Notes:


---

## Step 3: Install Wazuh Server

**Date completed:** ___________

### What I did:
- Downloaded and ran Wazuh installation script
- Installed Wazuh Manager, Indexer, and Dashboard (all-in-one)
- Saved initial admin credentials

### Screenshot:
!Wazuh Installation Complete (copy and paste the URL into your browser): screenshots/03-wazuh-install-complete.png

### Verification:


### Notes:


---

## Step 4: Access Wazuh Dashboard

**Date completed:** ___________

### What I did:
- Accessed Wazuh web interface from WS01 at [IP_ADDRESS]
- Logged in with initial admin credentials
- Verified dashboard loads correctly

### Screenshot:
!Wazuh Dashboard (copy and paste the URL into your browser): screenshots/04-wazuh-dashboard.png

### Notes:


---

## Step 5: Deploy Wazuh Agent on DC01 (Windows Server)

**Date completed:** ___________

### What I did:
- Downloaded Windows agent from Wazuh dashboard
- Installed agent on DC01 with manager IP: [IP_ADDRESS]
- Started the agent service
- Verified agent appears in Wazuh dashboard as "Active"

### Screenshot:
!DC01 Agent Active (copy and paste the URL into your browser): screenshots/05-dc01-agent-active.png

### Verification:


### Notes:


---

## Step 6: Deploy Wazuh Agent on WS01 (Workstation)

**Date completed:** ___________

### What I did:
- Downloaded Windows agent installer
- Installed agent on WS01 with manager IP: [IP_ADDRESS]
- Started the agent service
- Verified agent appears in Wazuh dashboard as "Active"

### Screenshot:
!WS01 Agent Active (copy and paste the URL into your browser): screenshots/06-ws01-agent-active.png

### Verification:


### Notes:


---

## Step 7: Install Sysmon on DC01 and WS01

**Date completed:** ___________

### What I did:
- Downloaded Sysmon from Microsoft Sysinternals
- Downloaded SwiftOnSecurity Sysmon config
- Installed Sysmon on both DC01 and WS01
- Configured Wazuh to collect Sysmon logs

### Screenshot:
!Sysmon Installed (copy and paste the URL into your browser): screenshots/07-sysmon-installed.png

### Verification:


### Notes:


---

## Step 8: Configure Log Collection Rules

**Date completed:** ___________

### What I did:
- Configured Wazuh agent on DC01 and WS01 to collect:
  - Windows Security Event Log
  - Windows System Event Log
  - Sysmon Operational Log
  - PowerShell Script Block Logging

### Logs being collected:

| Log Source | Event Channel | What It Captures |
|------------|--------------|-----------------|
| Security | Security | Logins, logoffs, account changes, privilege use |
| System | System | Service starts/stops, errors, warnings |
| Sysmon | Microsoft-Windows-Sysmon/Operational | Process creation, network connections, file changes |
| PowerShell | Microsoft-Windows-PowerShell/Operational | Script execution, command history |

### Screenshot:
!Log Collection Config (copy and paste the URL into your browser): screenshots/08-log-collection-config.png

### Notes:


---

## Step 9: Verify Logs Flowing to Wazuh

**Date completed:** ___________

### What I did:
- Checked Wazuh dashboard for incoming events from both agents
- Confirmed Security, System, and Sysmon events appearing
- Verified event count increasing over time

### Screenshot:
!Events Flowing (copy and paste the URL into your browser): screenshots/09-events-flowing.png

### Event counts (first 24 hours):

| Agent | Events Received | Top Event Types |
|-------|----------------|-----------------|
| DC01 | | |
| WS01 | | |

### Notes:


---

## Step 10: Simulate Attack — Brute Force Login

**Date completed:** ___________

### What I did:
- Simulated a brute force attack by attempting multiple failed logins
- Monitored Wazuh for detection and alerting

### Attack simulation:
- Target: DC01
- Method: Multiple failed login attempts with wrong passwords
- Account targeted: j.smith

### Wazuh detection:

| Alert | Rule ID | Level | Description |
|-------|---------|-------|-------------|
| | | | |

### Screenshots:
!Failed Login Attempts (copy and paste the URL into your browser): screenshots/10-failed-logins.png

!Wazuh Brute Force Alert (copy and paste the URL into your browser): screenshots/11-brute-force-alert.png

### Notes:


---

## Step 11: Simulate Attack — Account Lockout

**Date completed:** ___________

### What I did:
- Triggered account lockout by exceeding failed login threshold
- Verified Wazuh detected the lockout event
- Performed remediation (unlocked account in AD)

### Attack simulation:
- Attempted 5+ failed logins for j.smith
- Account locked out by GPO (from Phase 1 password policy)

### Wazuh detection:

| Alert | Rule ID | Level | Description |
|-------|---------|-------|-------------|
| | | | |

### Remediation steps:
1.
2.
3.

### Screenshots:
!Account Lockout Event (copy and paste the URL into your browser): screenshots/12-account-lockout.png

!Wazuh Lockout Alert (copy and paste the URL into your browser): screenshots/13-lockout-alert.png

### Notes:


---

## Step 12: Simulate Attack — Unauthorized Group Change

**Date completed:** ___________

### What I did:
- Added a user to the IT_Admins group (simulating privilege escalation)
- Verified Wazuh detected the group membership change
- Performed remediation (removed user from group)

### Attack simulation:
- Added j.smith to IT_Admins group (unauthorized privilege escalation)

### Wazuh detection:

| Alert | Rule ID | Level | Description |
|-------|---------|-------|-------------|
| | | | |

### Remediation steps:
1.
2.
3.

### Screenshots:
!Group Change Event (copy and paste the URL into your browser): screenshots/14-group-change.png

!Wazuh Privilege Alert (copy and paste the URL into your browser): screenshots/15-privilege-alert.png

### Notes:


---

## Step 13: Simulate Attack — Suspicious Process Execution

**Date completed:** ___________

### What I did:
- Ran suspicious commands that Sysmon and Wazuh should flag
- Verified detection in Wazuh dashboard

### Commands executed (on WS01 as j.smith):


### Wazuh detection:

| Alert | Rule ID | Level | Description |
|-------|---------|-------|-------------|
| | | | |

### Screenshot:
!Suspicious Process Alert (copy and paste the URL into your browser): screenshots/16-suspicious-process.png

### Notes:


---

## Step 14: Create Custom Alert Rule

**Date completed:** ___________

### What I did:
- Created a custom Wazuh rule to detect specific activity in my environment
- Tested the rule by triggering the condition
- Verified alert fires correctly

### Custom rule:
```xml
Rule XML here

sudo systemctl status wazuh-manager
sudo systemctl restart wazuh-manager
Get-Service WazuhSvc
Restart-Service WazuhSvc
sysmon -c
wevtutil qe Security /c:10 /f:text
Get-EventLog -LogName Security -Newest 20
