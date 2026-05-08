# SOC Lab: SSH Brute Force Attack Detection & Automated Response

## Overview

This project simulates a real-world Security Operations Center (SOC) scenario by performing a brute-force attack against an SSH service in a controlled lab environment and implementing automated defensive mechanisms to detect and mitigate malicious activity.

The lab was designed to provide hands-on experience with:
- Attack simulation
- Authentication log analysis
- Intrusion prevention systems
- Linux firewall enforcement
- Incident response concepts

The environment was built entirely using virtual machines and internal networking.

---

# Lab Architecture

| System | Role | IP Address |
|--------|------|------------|
| Kali Linux | Attacker | 192.168.100.20 |
| Ubuntu Server | Target | 192.168.100.10 |

Network Type:
- Isolated Internal Network (VirtualBox)

Target Service:
- SSH (Port 22)

---

## Technologies & Tools

- Kali Linux
- Ubuntu Server
- Hydra
- OpenSSH
- Fail2ban
- iptables
- journalctl
- VirtualBox

---

## Environment Setup

### 1. Network Configuration

Configured both virtual machines within the same isolated network and assigned static IP addresses.

Verification:

bash
ip a
ping 192.168.100.10

---

## SSH Deployment

sudo apt update
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh 

### Validation
sudo systemctl status ssh  
```ss -tuln | grep :22 ```

### Attack Simulation

A brute-force attack was simulated from Kali Linux using Hydra.  
```hydra -t 2 -l vboxuser -P /usr/share/wordlists/rockyou.txt ssh://192.168.100.10```

### Attack Characteristics
Automated password attempts
Repeated SSH authentication requests
Simulated credential attack behavior
Real-time authentication failures generated on target system

---


## Detection & Log Analysis

Authentication logs were monitored in real time on the Ubuntu server.  
```sudo journalctl -u ssh -f ```

## Observed Indicators
Multiple failed password attempts
Repeated requests from same IP address
Authentication patterns consistent with brute-force activity
Session initiation attempts

## Example log behavior:
"Failed password for vboxuser
Connection closed by authenticating user
error: maximum authentication attempts exceeded"

## Intrusion Prevention (Fail2ban)

Fail2ban was deployed to automatically detect and block malicious login activity.

```[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
maxretry = 3
findtime = 60
bantime = 600
backend = systemd
banaction = iptables-multiport
```
---

## Automated Response

Once the attack threshold was reached:

Fail2ban detected suspicious behavior
The attacker IP was automatically banned
Firewall rules were dynamically updated

### *Validation:*
sudo fail2ban-client status sshd
Observed behavior:
Total banned: 1
Banned IP list: 192.168.100.20

--- 

## Firewall Enforcement

Fail2ban dynamically created firewall rules using iptables.

```sudo iptables -L```

Example result:

Chain f2b-sshd
REJECT all -- 192.168.100.20 anywhere

This prevented additional SSH access attempts from the attacker machine.

--- 

## Validation

After the automated response was triggered, SSH access attempts from the attacker machine were tested again. 
 
```ssh vboxuser@192.168.100.10```

Result
- Connection restrictions observed
- Temporary blocking behavior confirmed
- Automated defense validated successfully

---

## Troubleshooting & Challenges

Several real-world issues were encountered and resolved during the project:
- SSH service validation issues
- Internal network communication problems
- Incorrect interface configuration
- Wordlist extraction and path validation
- Fail2ban firewall integration troubleshooting
- Authentication limit behavior during brute-force attempts

These troubleshooting steps provided valuable hands-on experience with Linux administration and security tooling.

## Screenshots
Network Configuration

SSH Service Running

Hydra Attack Simulation

SSH Log Analysis

Fail2ban Detection

Firewall Enforcement

## Key Takeaways

- Brute-force attacks generate identifiable log patterns
- Automated intrusion prevention significantly improves defensive response
- Firewall integration is critical for blocking malicious activity
- Real-time monitoring enables faster incident detection
- Linux-based security tooling provides effective layered defense mechanisms
  
--- 

## Skills Demonstrated
Linux System Administration
Network Troubleshooting
SSH Configuration
Security Monitoring
Log Analysis
Intrusion Prevention Systems
Firewall Management
Cybersecurity Lab Development

## Future Improvements
Potential future enhancements include:

SIEM integration
Centralized log aggregation
Dashboard visualization
Email alerting
Advanced attack simulations
Multi-host monitoring

---

## Conclusion

This project provided practical experience simulating, detecting, and mitigating SSH brute-force attacks within a controlled SOC-style lab environment.
The implementation combined offensive and defensive security concepts while reinforcing hands-on Linux administration, monitoring, and incident response skills commonly required in cybersecurity and SOC analyst roles.



