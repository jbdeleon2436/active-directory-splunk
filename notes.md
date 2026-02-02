# Active Directory Detection Lab with Splunk

This project documents my Active Directory home lab designed to practice **enterprise-style detection engineering** using Splunk, Sysmon, and simulated attacks from Kali Linux.  
The goal of this lab is to understand how Windows domain activity, endpoint telemetry, and attacker behavior appear in logs and how they can be detected in a SIEM.

---

## Lab Objective

I built this lab to:

- Deploy a basic **Active Directory domain**
- Centralize logs using **Splunk**
- Collect detailed endpoint telemetry using **Sysmon**
- Simulate real attack techniques from a Kali Linux attacker
- Practice log analysis, detection, and alerting

---

## Lab Architecture Overview

### Network Design

- **Domain Name:** `jared.local`
- **Network:** `192.168.10.0/24`

### Machines in the Lab

| Role | OS | IP Address | Tools Installed |
|----|----|----|----|
| Splunk Server | Windows | 192.168.10.10 | Splunk Enterprise |
| Domain Controller | Windows Server | 192.168.10.7 | Active Directory, Sysmon, Splunk Universal Forwarder |
| Target Machine | Windows 11 | DHCP | Sysmon, Splunk Universal Forwarder, Atomic Red Team |
| Attacker Machine | Kali Linux | 192.168.10.250 | nmap, ssh, attack tooling |

All machines are connected through a **virtual switch**, routed to the internet via a **router**.

---

## Lab Diagram

This diagram shows the full logical layout of my lab, including log flow from endpoints to Splunk.

```
![Active Directory Lab Architecture](screenshots/ad-lab-architecture.png)
```

---

## Step 1: Virtual Machine Setup

I used **Oracle VirtualBox** to create all virtual machines.

### Network Configuration

At first, I encountered IP conflicts when using default NAT networking.  
To fix this and allow all machines to communicate properly while still having internet access, I configured:

- **Bridged Adapter** networking for all VMs

This ensured:
- Unique IP addresses
- Proper domain communication
- Attacker-to-target reachability

---

## Step 2: Domain Controller Setup

I configured a Windows Server machine as my **Domain Controller**.

Actions performed:
- Installed **Active Directory Domain Services**
- Promoted the server to a Domain Controller
- Created the domain `jared.local`
- Joined the Windows 11 target machine to the domain

I also installed:
- **Sysmon** for detailed endpoint telemetry
- **Splunk Universal Forwarder** to send logs to the Splunk server

```
![Domain Controller Setup](screenshots/domain-controller-setup.png)
```

---

## Step 3: Splunk Server Setup

I installed **Splunk Enterprise** on a dedicated Windows machine.

Actions:
- Installed Splunk Enterprise
- Configured receiving port (9997)
- Verified forwarder connections from endpoints
- Created a dedicated index for this lab (e.g., `ad_lab`)

```
![Splunk Server Dashboard](screenshots/splunk-server-dashboard.png)
```

---

## Step 4: Endpoint Logging Configuration

### Windows Target Machine

On the Windows 11 target machine, I installed:

- **Sysmon** (with a detection-focused configuration)
- **Splunk Universal Forwarder**

This allowed me to collect:
- Process creation
- Network connections
- Authentication events
- File and registry activity

I verified logs were successfully reaching Splunk before launching any attacks.

```
![Sysmon Installed](screenshots/sysmon-installed.png)
```

---

## Step 5: Attacker Simulation (Kali Linux)

I used Kali Linux to simulate attacker behavior against the domain.

### Reconnaissance
- Network scanning using `nmap`
- Host discovery and port scanning

```
nmap -sS 192.168.10.0/24
```

### Authentication Attacks
- Failed SSH attempts
- Credential guessing
- Domain authentication failures

### Atomic Red Team (on Windows Target)
I used Atomic Red Team to simulate known attack techniques such as:
- Credential access
- Execution techniques
- Persistence behaviors

```
![Atomic Red Team Execution](screenshots/atomic-red-team.png)
```

---

## Step 6: Log Analysis in Splunk

I analyzed the following data sources in Splunk:

- Windows Security Event Logs
- Sysmon Event Logs
- Authentication failures
- Network connections
- Process execution

Example SPL searches I used:

```
index=ad_lab sourcetype=WinEventLog:Security | stats count by EventCode
```

```
index=ad_lab sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
| stats count by EventCode
```

```
![Splunk Detection Results](screenshots/splunk-detections.png)
```

---

## Step 7: What I Learned

Through this lab, I learned:

- How Active Directory authentication events appear in logs
- How Sysmon greatly enhances visibility compared to default Windows logs
- How attacker actions translate into detectable telemetry
- How to troubleshoot log ingestion issues in Splunk
- How enterprise-style detection pipelines are built end-to-end

---

## Future Improvements

Planned next steps for this lab:

- Create Splunk dashboards for:
  - Failed domain logons
  - Lateral movement detection
  - Suspicious process execution
- Build real-time alerts
- Simulate more advanced attacks (pass-the-hash, Kerberoasting)
- Add MITRE ATT&CK mapping to detections

---

## Screenshot Checklist

```
[ ] AD Lab Architecture Diagram
[ ] Domain Controller Setup
[ ] Splunk Server Dashboard
[ ] Sysmon Installed
[ ] Atomic Red Team Execution
[ ] Splunk Detection Results
```

---

## Tools Used

- Oracle VirtualBox
- Windows Server
- Windows 11
- Kali Linux
- Active Directory
- Splunk Enterprise
- Splunk Universal Forwarder
- Sysmon
- Atomic Red Team

