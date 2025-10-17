## Resources
- **Exploring Splunk e-book** (esp. Chapter 4).  
- [Splunk Search Documentation](https://docs.splunk.com/Documentation/Splunk/7.2.4/Search/GetstartedwithSearch)  
- Lab: 
![[Pasted image 20251004152755.png]]
#### **Scenario**: 
The organization you work for (Wayne Enterprises) is using Splunk as a SIEM solution to enhance its intrusion detection capabilities. Wayne Enterprises went through a red team exercise and the red team provided you with technical details about some of their exploitation activities (a.k.a Tactical Threat Intelligence). Your SOC manager tasked you with first trying to identify successful exploitation attempts on your own through Splunk. He then tasked you with translating the provided TTPs into Splunk searches, once the initial investigation is complete.


#### **Objectives**
The learning objective of this lab, is to learn effective Splunk search writing and how to translate attacker TTPs into Splunk searches. 

Specifically, you will learn how to use Splunk’s capabilities in order to: 
- Have better visibility over a network 
- Respond to incidents timely and effectively 
- Proactively hunt for threats

![[Pasted image 20250930143332.png]]

#### **Network Configuration**
- Incident Responder’s Subnet: 172.16.72.0/24
- Splunk: 172.16.72.100:8000
- Connection Type: Web-based
Use a Chrome or Firefox browser to connect to Splunk's web interface
(http://172.16.72.100:8000), as follows
- Username: admin
- Password: els@nalyst

#### TASK 1: TRY TO IDENTIFY A SUCCESSFUL EXPLOITATION ATTEMPT WITHOUT CONSULTING WITH THE PROVIDED TTPS

##### Hints: 
- Start your investigation by focusing on the stream:dns sourcetype. Then, keep following leads until you identify what actually happened. Curious-looking domain names are always of interest.


[!] NOTE: Before starting your investigation change the time range picker to All time [!]

Always identify the available sourcetypes before you begin your investigation. You can do that as follows.

``SPL> | metadata type=sourcetypes index="botsv1"``

![[Pasted image 20251004163431.png]]

If you want better granularity regarding the available sourcetypes, submit the search below.
``SPL> | metadata type=sources index="botsv1"``
![[Pasted image 20251004165428.png]]

The results between the last two searches are the same. The second search will provide you with a little more detail about the available sourcetypes.

As you can see, Splunk has ingested Windows event logs, Sysmon logs, Fortigate UTM logs, Suricata logs etc.

[!] NOTE: If you look carefully enough you will notice that the firstTime, lastTime and recentTime entries follow the epoch time representation. To convert epoch time to a human understandable representation submit the following search [!]

``SPL> | metadata type=sources index="botsv1" | convert ctime(firstTime) as firstTime | convert ctime(lastTime) as lastTime | convert ctime(recentTime) as recentTime
![[Pasted image 20251004165708.png]]

Let's now identify all the available hosts in the dataset before you start your investigation, you can do that through the following search
``SPL> | metadata type=hosts index="botsv1" | convert ctime(firstTime) as firstTime | convert ctime(lastTime) as lastTime | convert ctime(recentTime) as recentTime
![[Pasted image 20251004170400.png]]

You can sort the above by total count to gain a better understanding.
![[Pasted image 20251004170451.png]]

A great ``sourcetype`` to start with is ``stream:dns``: 
``SPL> index=botsv1 sourcetype=stream:dns | fieldsummary
The results of the search above may be difficult to read, so create a table that will contain field and values entries only. You can that by submitting the following search
``SPL> index=botsv1 sourcetype=stream:dns | fieldsummary | table field values
![[Pasted image 20251004170706.png]]

You now need to determine which of the available fields is more important. 

**``dest``** could provide you with useful information, but the most interesting field in these results is **``query{}``**, as it reveals which websites users have queried, and can provide you with information related to interactions with remote (and possibly malicious) servers.
![[Pasted image 20251004171054.png]]


To better analyze DNS query information, submit the following search.
``SPL> index=botsv1 sourcetype=stream:dns record_type=A | stats count by query{} | sort count
![[Pasted image 20251004171232.png]]

As you review the queries, you’ll notice some suspicious-looking domain names. One such example is **cerberhhyed5frqa.xmfir0.win**, which strongly suggests the use of a **Domain Generation Algorithm (DGA)**
![[Pasted image 20251004171348.png]]

``SPL> index=botsv1 sourcetype=stream:dns record_type=A query{}=cerberhhyed5frqa.xmfir0.win | table _time src dest query{}

In the results above, you can see the **192.168.250.100** host making a DNS query to **192.168.250.20**. **192.168.250.20** in turn makes a number of external DNS queries. 
![[Pasted image 20251004171925.png]]
From this behavior you can assume that **192.168.250.20** is a DNS server
and **192.168.250.100** is probably a compromised machine.


Based on the time included in the results above, you can give 192.168.250.100 a look as follows.
``SPL> index=botsv1 sourcetype=stream:dns record_type=A src=192.168.250.100 earliest=08/24/2016:0:0:0 | table _time src dest query{} | dedup query{}

[!] NOTE: Notice that the earliest events are located at the bottom of the table [!]
![[Pasted image 20251004172527.png]]
The possibly compromised 192.168.250.100 system is looking for isatap and wpad right after visiting the curious-looking cerberhhyed5frqa.xmfir0.win domain.

**isatap** is related to IPv6 tunneling and **wpad** to proxying. This is quite suspicious…


What you should do next is investigate the behavior of the possibly compromised 192.168.250.100 system, by analyzing other logs for approximately the same period of time as above. 

**Sysmon logs are perfect for this**. 

First, change the time range picker as follows and click Apply.
![[Pasted image 20251004172801.png]]
Then, submit the following search. 
``SPL>index=botsv1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational" SourceIp="192.168.250.100"``
![[Pasted image 20251004174332.png]]
Hundreds of spikes appeared, an important field to check is ``app``.
![[Pasted image 20251004174417.png]]


- The presence of **osk.exe** in approximately **83%** of the entries is highly suspicious.
    
- Normally, **osk.exe** is associated with the On-Screen Keyboard; however, the legitimate version does **not** reside in the `C:\Users\<user>\AppData\Roaming\` directory — which further raises suspicion.

![[Pasted image 20251004174814.png]]
- Additionally, note the user **Bob Smith**, who appears to be a potential **victim of the attack**.

You could have also identified this application, as follows. 
``SPL> index=botsv1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational" SourceIp="192.168.250.100" | stats count by app 

**osk.exe definitely looks suspicious. **

So, give it a closer look by simply clicking on it. You should see something similar to the below.
By inspecting the **``dest_port``** field. You will be presented with the below
![[Pasted image 20251004191153.png]]
- That’s an awful lot of network traffic for an application like on screen keyboard. This is suspicious...
- Port **6892** corresponds to bit torrent and windows live messenger file transfer, something also suspicious

There is only one communication on port 80, click on it to learn more. You should see the below.
![[Pasted image 20251004191350.png]]

There’s a destination IP in the result **54.148.194.58**, which is worth checking.

But since user **Bob Smith** is most probably a victim of an attack, consult with the available Sysmon logs to identify what else is running on his machine. You can do that as follows

``SPL> index=botsv1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational" app="C:\\Users\\bob.smith.WAYNECORPINC*"
Inspect the ``app`` field once again. You should see the following.
![[Pasted image 20251004191516.png]]

Notice the existence of another curious looking application ``C:\Users\bob.smith.WAYNECORPINC\AppData\Roaming\121214.tmp``.Give it a look by clicking on it. You should see the following.
![[Pasted image 20251004191656.png]]
Nothing curious-looking in the results, but there are important fields that could be added to assist your investigation, such as the CommandLine or the ParentCommandLine one.

Submit the following search to see all the occurrences of 121214.tmp in the Sysmon logs and also any entry/log that contains **ParentCommandLine** or **CommandLine** entries.
``SPL> index=botsv1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational" "121214.tmp" AND (ParentCommandLine=* OR CommandLine=*) | table _time process process_id ParentProcessId ParentImage CommandLine ParentCommandLine
![[Pasted image 20251004191823.png]]

The earliest events are at the bottom of the table. If you start from the first (earliest) event you will see that wscript.exe (parent) called cmd.exe (child). In addition to that, you can see from ParentCommandLine that wscript.exe executed 20429.vbs.

Chain Of Commands: 
==``wscript.exe (Parent)
   ``└── cmd.exe (Child)
         ``└── 121214.tmp
               ``└── 20429.vbs (obfuscated script)==
               
![[Pasted image 20251004192144.png]]


You can identify more about 20429.vbs by submitting the following search.
``SPL> index=botsv1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational" "20429.vbs" AND (ParentCommandLine=* OR CommandLine=*) | table _time process process_id ParentProcessId ParentImage CommandLine ParentCommandLine
![[Pasted image 20251004194941.png]]
This is clearly obfuscated code. User Bob Smith is definitely victim of an attack.

Sysmon logs also contain MD5 hashes. If you would like to learn more about that 121214.tmp file you saw earlier, change time range picker to **All time**; submit the following search and inspect the **md5** field.

``SPL> index=botsv1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational" "121214.tmp"
![[Pasted image 20251004195044.png]]
If you submit the **``EE0828A4E4C195D97313BFC7D4B531F1``** hash on a search engine, you will identify that you are dealing with Cerber ransomware.

![[Pasted image 20251004195122.png]]

Processes Names that this ransomware takes in a windows system: 
![[Pasted image 20251004195237.png]]



#### TASK 2: TRANSLATE THE PROVIDED RED TEAM TTPS INTO SPLUNK SEARCHES

##### Hints: 
- Removable media can be identified by the existence of drive letters in Sysmon logs or the existence of the string friendlyname in Windows registry logs
- https://www.splunk.com/blog/2017/11/03/you-can-t-hyde-from-dr-levenshteinwhen-you-use-url-toolbox.html
- The CommandLine field of Sysmon logs can help you with that Mature ransomware in addition to attempting to disable system restore try to delete everything stored in the VSC using the Volume Shadow Copy Service (VSS)
- Obfuscated code usually involves the execution of an overly long command

##### Step 1: Identify a malicious USB

Inserted Removable media devices can be identified through the following SPL. 
``SPL> index=botsv1 sourcetype="xmlwineventlog:microsoft-windowssysmon/operational" "d:\\" | stats count by Computer,CommandLine

The idea here is to include **all possible drive letters**. The search above is to test the existence of a D: drive only.

**D: Drive: **
![[Pasted image 20251004195705.png]]
-> No Results

**C: Drive:**
![[Pasted image 20251004195810.png]]
**Findings:**
**Infected Computer:**
==``we8105desk.waynecorpinc.local

**CommandLines:**
1. ==``"C:\Program Files (x86)\Internet Explorer\iexplore.exe" -nohome
2. ==``"C:\Program Files (x86)\Microsoft Office\Office14\WINWORD. EXE" /n /f``"D:\Miranda_Tate_unveiled.dotm"

``SPL> index=botsv1 sourcetype=winregistry friendlyname | table host object data
![[Pasted image 20251004195631.png]]
##### Step 2: Identify DGA computer-generated domain names

The following search may uncover computer-generated domain names.

``SPL> index=botsv1 sourcetype=stream:dns record_type=A | table query{} | lookup ut_parse_extended_lookup url as query{} | search ut_domain!=None NOT (ut_domain_without_tld=microsoft OR ut_domain_without_tld=msn OR ut_domain_without_tld=akamaiedge OR ut_domain_without_tld=akadns OR ut_domain=nsatc.net OR ut_domain=quest.net OR ut_domain=windows.com OR ut_domain=arin.net) | `ut_shannon(ut_subdomain)` | stats count by query{} ut_subdomain ut_domain ut_domain_without_tld ut_tld ut_shannon | sort - ut_shannon

![[Pasted image 20251006141715.png]]

##### Step 3: Identify malicious VBS
The following search may identify malicious VBS files
``SPL> index=botsv1 source="WinEventLog:Microsoft-Windows-Sysmon/Operational" "*.vbs" AND (ParentCommandLine=* OR CommandLine=*) | table _time process process_id ParentProcessId ParentImage CommandLine ParentCommandLine
![[Pasted image 20251006142105.png]]

##### Step 4: Identify mature ransomware activity
The following search can possibly identify mature ransomware activity.
``SPL> index="botsv1" source="wineventlog:microsoft-windows-sysmon/operational" EventCode=1 process=*\\vssadmin.exe | search CommandLine="*vssadmin*" CommandLine="*Delete *" CommandLine="*Shadows*"
![[Pasted image 20251006141934.png]]

##### Step 5:  Identify code obfuscation
The following search can possibly identify attackers using code obfuscation.
``SPL> index="botsv1" source="wineventlog:microsoft-windows-sysmon/operational" | eval len=len(CommandLine) | table User, len, CommandLine | sort - len

![[Pasted image 20251006142029.png]]