### Vulnerability Finding: Suspicious `rundll32.exe` Persistence and Unauthorized Network Activity

**Endpoint:** `win10-server.els-child.ELS.local`  
**Date Identified:** 11/09/2025  
**Tester:** Mr. Yassine SAHLI

---

## Description

During the assessment of the `win10-server.els-child.eLS.local` endpoint, the GRR framework was used to gather system and network information. Listing active network connections revealed that the Windows process `rundll32.exe` had established a connection on **Port 81** to the intranet host `10.100.11.250`.  

`rundll32.exe` is a legitimate Windows utility designed to load and execute DLLs locally. Under normal circumstances, it does not initiate persistent outbound network connections. Continuous execution of `rundll32.exe` combined with unexpected network activity is a common attacker technique to **execute malicious payloads while blending in with trusted system processes**.  

Further investigation confirmed that the process was running under **PID 5276** and displayed persistent activity, which is not typical behavior for `rundll32.exe`. This indicates that the process may have been hijacked to establish a covert communication channel with a remote host.

Registry inspection using GRR flows revealed suspicious entries under the following keys:

- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion`
- `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\*`

Analysis of these entries uncovered a reference to `calc.exe`, suggesting the presence of a **persistence mechanism**. In the kill chain methodology, this indicates that the attacker may have configured the system to automatically execute malicious code on startup.

---

## Impact

- **Remote Command and Control (C2):** Unauthorized network connections allow attackers to control the host remotely.  
- **Persistence:** Registry modifications enable the attacker to maintain access even after a system reboot.  
- **Execution of Malicious Payloads:** Hijacking `rundll32.exe` allows attackers to execute arbitrary DLLs while remaining stealthy.  
- **Internal Network Risk:** Outbound connections to intranet hosts may allow lateral movement within the corporate network.

This represents a **high-risk scenario**, as it combines covert process execution, network communication, and persistence, all of which are foundational to attacker footholds.

---

## Evidence

1. **Suspicious process and network activity:**
   - `rundll32.exe` connecting to `10.100.11.250:81`
   - Active PID: 5276
![[Pasted image 20250911144507.png]]

2. **Persistence indicators in Registry:**
   - `calc.exe` entries found under `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion` and `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\*`
![[Pasted image 20250911144610.png]]

---

## MITRE ATT&CK Mapping

- **Persistence**
  - **T1547.001 – Registry Run Keys / Startup Folder:** Registry entries were observed that could trigger execution of `calc.exe` as part of a persistence mechanism.
- **Execution**
  - **T1059.001 – Command and Scripting Interpreter: PowerShell:** Potential use of hijacked process to execute scripts remotely.
- **Command and Control**
  - **T1071.001 – Application Layer Protocol: Web Protocols:** Outbound connection on port 81 to intranet host.

---

## Recommendation

**Immediate Response:**
- Isolate the affected endpoint to prevent further communication with intranet hosts.  
- Terminate the suspicious `rundll32.exe` process (PID 5276).  
- Collect memory and forensic evidence before remediation.

**Remediation:**
- Remove malicious or unauthorized DLLs executed by `rundll32.exe`.  
- Clean or remove suspicious Registry entries that provide persistence.  
- Scan the endpoint with endpoint detection and response (EDR) tools for additional IOCs.

**Preventive Measures:**
- Implement application whitelisting to restrict execution of system binaries like `rundll32.exe` from unauthorized locations.  
- Monitor network activity for unusual connections initiated by trusted Windows processes.  
- Educate users on safe execution practices and the risks of running unknown scripts or DLLs.

**Monitoring:**
- Enable advanced endpoint detection to alert on persistent abnormal process activity.  
- Periodically review Registry keys for unauthorized modifications.  
- Log and analyze all outbound connections from critical endpoints for unusual destinations.

---

**Severity:** High  
**CVSSv3.1 Score:** 8.7 (High – Persistent malicious activity with network communication)
