Project 7 — Endpoint Threat Detection & Investigation

Overview

This project focused on investigating suspicious endpoint activity on a Windows Server domain controller using native Windows Security and PowerShell event logs.

The objective was to practice a SOC analyst workflow:

Detect → Investigate → Correlate → Assess → Document

The investigation focused on authentication activity, PowerShell execution, and Windows process-related evidence.

Lab Environment
	*	Endpoint: Windows Server / DC01
	*	Role: Domain Controller
	*	Investigation platform: Windows Event Logs and PowerShell
	*	Primary tools: Windows Event Viewer / PowerShell
	*	Event sources: Windows Security log and Microsoft-Windows-PowerShell/Operational
	*	Wazuh: Not used

Investigation Objectives
The investigation was designed to answer:
	1.	Was there evidence of failed authentication?
	2.	Which account was involved?
	3.	What type of logon occurred?
	4.	Was PowerShell activity recorded?
	5.	When did the PowerShell activity occur?
	6.	Was there evidence of successful authentication afterward?
	7.	Could the endpoint activity be correlated into a useful SOC timeline?

Investigation 1 — Failed Authentication

Event ID 4625
The first investigation focused on Windows Security Event ID 4625, which records failed logon attempts.

Observed evidence
	*	Account Name: Administrator
	*	Logon Type: 2
	*	Failure reason: Unknown username or bad password
	*	Source IP: 127.0.0.1
	*	Timestamp: 10/06/2026 1:13:26 AM

Analysis
The event confirmed a failed authentication attempt involving the Administrator account.

The Logon Type 2 indicates an interactive logon attempt.The source address was 127.0.0.1, which is the local loopback address. Therefore, the evidence did not indicate that this particular failed authentication originated from a remote external host.
This was treated as suspicious authentication activity requiring investigation rather than automatically being classified as a confirmed attack.

Investigation 2 — PowerShell Activity

Event ID 4104
The investigation was then expanded to PowerShell Script Block Logging.

Event ID 4104 records PowerShell script block activity and can provide visibility into commands or scripts executed through PowerShell.

Observed evidence
	*	Event ID: 4104
	*	Timestamp: 10/06/2026 1:41:16 AM
	*	Activity: PowerShell Script Block Logging

The investigation confirmed that PowerShell activity was recorded on the endpoint.

This was important because PowerShell is commonly used by administrators but can also be abused by attackers for execution, discovery, persistence, or other post-compromise activities.

Therefore, the presence of PowerShell activity alone was not treated as proof of malicious behavior.

Investigation 3 — PowerShell Process
During the investigation, the PowerShell process associated with the activity was identified.

Recorded process information
	*	Process ID: 4868
	*	Process start time: 10/06/2026 1:23:44 AM

The PID provided a useful correlation point for process-level investigation. A later attempt to retrieve the current process information did not return the Parent Process ID because the PowerShell process was no longer running.

Rather than treating the missing PPID as evidence of malicious behavior, the investigation was documented using the evidence that was actually available.

Investigation 4 — Successful Authentication

Event ID 4624
The investigation also examined successful authentication activity.

Observed evidence
	*	Event ID: 4624
	*	Account: DC01$
	*	Timestamp: 10/06/2026 3:29:01 AM

The account name DC01$ represents the computer account for the domain controller. This demonstrated the importance of examining both failed and successful authentication events when constructing an endpoint timeline.

Investigation Timeline

Time	Event	Evidence
1:13:26 AM	4625	Failed authentication involving Administrator
1:23:44 AM	Process activity	PowerShell process PID 4868
1:41:16 AM	4104	PowerShell Script Block Logging
3:29:01 AM	4624	Successful authentication involving DC01$

The timeline helped demonstrate how multiple Windows telemetry sources can be correlated during a SOC investigation.

SOC Analysis

The investigation produced several indicators that required examination:

1. Failed Administrator authentication
The Administrator account generated a failed interactive authentication event.

2. PowerShell execution
PowerShell Script Block Logging showed activity on the same endpoint.

3. Process correlation
A PowerShell process with PID 4868 was identified.

4. Successful authentication
A later successful authentication event involving the domain controller computer account was observed.

However, the collected evidence does not by itself prove that the endpoint was compromised.A SOC analyst should avoid declaring an incident based solely on isolated suspicious-looking events.

Additional evidence such as command-line content, process ancestry, network connections, EDR telemetry, persistence mechanisms, or additional authentication events could provide stronger confirmation.

Key SOC Skills Practiced
	*	Windows Security Event Log analysis
	*	Event ID 4625 investigation
	*	Event ID 4624 investigation
	*	PowerShell Event ID 4104 analysis
	*	Authentication investigation
	*	Process identification
	*	Process ID correlation
	*	Timeline construction
	*	Endpoint investigation
	*	Suspicious activity assessment
	*	Evidence-based incident analysis
	*	Distinguishing suspicious activity from confirmed compromise

Important Event IDs Learned

Event ID	Meaning	SOC Relevance
4624	Successful logon	Identify successful authentication
4625	Failed logon	Investigate failed authentication and possible brute-force activity
4104	PowerShell Script Block Logging	Investigate PowerShell commands/scripts
4688	Process creation	Correlate processes and process ancestry

What I Learned

This project helped me understand that effective SOC investigation is not simply about finding one suspicious event.

The analyst needs to:
	1.	Identify the suspicious event.
	2.	Examine the account involved.
	3.	Check the logon type and source.
	4.	Look for related PowerShell activity.
	5.	Identify relevant processes.
	6.	Build a timeline.
	7.	Correlate multiple pieces of evidence.
	8.	Determine whether the evidence supports suspicious activity or a confirmed incident.
	9.	Document the findings accurately.

One of the most important lessons was not to assume that every suspicious event is malicious.

For example, PowerShell is a legitimate administrative tool, and a failed Administrator login does not automatically mean an attacker successfully compromised the system.

The analyst must investigate the surrounding evidence before reaching a conclusion.

Final Assessment

Finding
Suspicious endpoint activity was identified and investigated, but the collected evidence was insufficient to confirm a compromise.

The investigation identified:
	*	A failed Administrator authentication attempt.
	*	PowerShell Script Block Logging activity.
	*	A PowerShell process with PID 4868.
	*	A later successful authentication involving the DC01 computer account.

The evidence was therefore classified as activity requiring investigation rather than confirmed malicious compromise.

Portfolio Takeaway

This project demonstrates practical experience investigating Windows endpoint telemetry and correlating authentication, PowerShell, and process-related events.

It represents a basic SOC investigation workflow using native Windows telemetry and demonstrates the ability to move from an individual alert/event toward a broader timeline and evidence-based assessment
