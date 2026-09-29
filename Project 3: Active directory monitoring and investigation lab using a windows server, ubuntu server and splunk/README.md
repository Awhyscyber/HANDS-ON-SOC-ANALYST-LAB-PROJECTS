Project Overview

This project focused on building a small Active Directory monitoring and investigation lab using a Windows Server Domain Controller and Splunk.

The objective was to learn how a SOC analyst can collect Windows security and PowerShell activity, search the resulting events in Splunk, and investigate activity recorded on a Domain Controller.

The project particularly focused on PowerShell Script Block Logging (Event ID 4104) because PowerShell activity can provide useful evidence during security investigations.

Lab Environment

Component	Purpose
Windows Server / DC01	Active Directory Domain Controller and activity source
Splunk Enterprise	Log collection, searching and investigation
Splunk Universal Forwarder	Forwarded Windows event logs to Splunk
PowerShell	Generated and investigated PowerShell activity

Network
	*	Windows DC01: 192.168.56.108
	*	Ubuntu Splunk Server: 192.168.56.105

Objectives

The main objectives of this project were to:
	*	Monitor activity on an Active Directory Domain Controller.
	*	Forward Windows event logs to Splunk.
	*	Enable collection of PowerShell Operational events.
	*	Understand PowerShell Script Block Logging.
	*	Investigate Event ID 4104.
	*	Search and filter Windows events in Splunk.
	*	Identify the host generating the event.
	*	Examine PowerShell script block information.
	*	Understand how a SOC analyst could use these logs during an investigation.

1. Active Directory Monitoring

The first part of the project focused on monitoring activity occurring on the Windows Domain Controller.

The Windows Server was configured as the Domain Controller, referred to as DC01.

Windows security auditing was also configured to record successful and failed logon activity.

This provided a source of security telemetry that could later be forwarded to Splunk.

2. Splunk Universal Forwarder

The Splunk Universal Forwarder was installed on the Windows Domain Controller.Its purpose was to collect Windows event logs from DC01 and forward them to the Splunk server.

The Windows Security event log was already configured as a monitored input. The relevant configuration included:

[WinEventLog://security]
disabled = 0
index = main

The Universal Forwarder service was verified as running.

3. PowerShell Operational Logging

A major part of the project was monitoring PowerShell activity. The PowerShell Operational log was added to the Universal Forwarder’s inputs:

[WinEventLog://Microsoft-Windows-PowerShell/Operational]
disabled = 0
index = main

The Universal Forwarder was then restarted so that the configuration would take effect.

4. Generating PowerShell Activity

A controlled PowerShell test was performed on DC01:

Write-Host "SOC PowerShell monitoring test"

The command produced:

SOC PowerShell monitoring test

This generated PowerShell activity that could be examined through Windows event logging.

5. Investigating Event ID 4104

The most important event investigated during this project was:

Event ID 4104 — PowerShell Script Block Logging

A Splunk search was used to locate these events:

index=main EventCode=4104

The search returned 56 events, confirming that PowerShell Operational events were successfully reaching Splunk.

This was an important milestone because it demonstrated the complete logging pipeline:

DC01
   ↓
PowerShell Operational Log
   ↓
Splunk Universal Forwarder
   ↓
Splunk
   ↓
SOC Investigation

6. Understanding Script Blocks

A PowerShell script block is a section of PowerShell code that PowerShell processes as a unit.

Event ID 4104 records information about these script blocks when Script Block Logging is enabled.

Important fields observed during the investigation included:
	•	Host
	•	ScriptBlock ID
	•	Message
	•	ScriptBlockText

The event showed:

Host: DC01

The message included: Creating ScriptBlock text 2 of 2

The ScriptBlockText field also produced a recorded PowerShell value: SilentlyContinue

7. Splunk Investigation Search

To narrow the investigation to a single event, the following Splunk search was used:

index=main EventCode=4104 | head 1 | table _time host EventCode Message ScriptBlockText

This made it easier to examine an individual PowerShell event instead of reviewing a large number of events at once.

Another investigation query used was: index=main EventCode=4104 host=DC01 | stats count by ScriptBlockText

This approach can help an analyst identify recurring PowerShell script block activity on a particular Domain Controller.

8. SOC Analyst Perspective

From a SOC perspective, PowerShell activity is important because it can be used for both legitimate administration and malicious activity.

A 4104 event by itself does not automatically mean that an attack occurred.

An analyst should investigate the surrounding context, including:
	*	Which host generated the event?
	*	Which user account was involved?
	*	What PowerShell code was executed?
	*	When did the activity occur?
	*	Was the activity expected?
	*	Were there related authentication events?
	*	Were there Active Directory changes?
	*	Are there other suspicious events around the same time?

This demonstrates an important SOC principle:

	An alert or log event is evidence that needs context, not automatic proof of an attack.

9. What I Learned

Through this project, I learned how Windows and Active Directory telemetry can be collected and investigated using Splunk.

Key technical lessons
	*	How a Domain Controller can act as a source of security telemetry.
	*	How the Splunk Universal Forwarder collects Windows Event Logs.
	*	How Windows Security logs can be forwarded to Splunk.
	*	How PowerShell Operational logging works.
	*	What Event ID 4104 represents.
	*	What a PowerShell script block is.
	*	How to search Windows events in Splunk.
  *	How to narrow a large event set using SPL.
	*	How to examine fields such as host, Message, and ScriptBlockText.
	*	How PowerShell activity can become useful evidence during a SOC investigation.

SOC investigation lessons

I also learned that effective monitoring is not simply about collecting large numbers of logs.

A SOC analyst needs to:
	1.	Identify the relevant event.
	2.	Determine where it came from.
	3.	Examine the available evidence.
	4.	Establish context.
	5.	Determine whether the activity is expected or suspicious.
	6.	Correlate it with other events when necessary.
	7.	Document the investigation.

10. Practical Skills Demonstrated

This project demonstrates hands-on experience with:
	*	Active Directory monitoring
	* Windows Event Logs
	*	PowerShell logging
	*	Event ID 4104
	*	Splunk Enterprise
	*	Splunk Universal Forwarder
	*	SPL searches
	*	Security event investigation
	*	Log analysis
	*	SOC monitoring concepts
	*	Basic incident investigation methodology

The extracted PowerShell script block information, including:

SilentlyContinue

12. Interview Questions and Answers

Q1. What is Event ID 4104?

Event ID 4104 is generated by PowerShell Script Block Logging and records information about PowerShell script blocks.

Q2. Why is PowerShell monitoring useful to a SOC analyst?

PowerShell is widely used for legitimate administration but can also be used to perform malicious actions. Monitoring its activity can therefore provide useful investigation evidence.

Q3. What is a script block?

A script block is a section of PowerShell code that PowerShell processes as a unit.

Q4. What is the purpose of the Splunk Universal Forwarder?

It collects data from a system and forwards that data to a Splunk server for indexing and analysis.

Q5. Does every 4104 event indicate an attack?

No. Legitimate administrators, applications and system processes can generate PowerShell activity. The analyst needs additional context before determining whether activity is suspicious.

Q6. What does ScriptBlockText provide?

It can provide the actual PowerShell code or script content associated with the recorded script block.

Q7. Why is the host field useful?

It identifies the system that generated the event. In this investigation, the host was DC01.

13. SOC Investigation Scenarios

Scenario 1

A 4104 event appears on the Domain Controller.

Question: What should the analyst do first?

Answer: Examine the event details and determine what PowerShell activity occurred, when it occurred, and which host generated it.

Scenario 2

The ScriptBlockText contains an unfamiliar command.

Question: What should the analyst do?

Answer: Investigate the command and correlate it with the user, timestamp, authentication events and other relevant security telemetry before deciding whether it is suspicious.

Scenario 3

Multiple 4104 events appear within a short period.

Question: Does this automatically mean an attacker is present?

Answer: No. The analyst should establish the context and determine whether the activity corresponds to legitimate administration or another expected process.

14. Final Project Takeaway

This project gave me practical experience moving from Windows event generation to centralized SIEM investigation.

The most important workflow I learned was:

Generate Activity
       ↓
Windows Logging
       ↓
Universal Forwarder
       ↓
Splunk
       ↓
Search
       ↓
Investigate
       ↓
Establish Context
       ↓
Document Findings

This project strengthened my understanding of how a SOC analyst uses centralized logging to monitor Windows and Active Directory environments and investigate PowerShell activity.

Tools Used
	*	Windows Server / Active Directory
	*	PowerShell
	*	Splunk Enterprise
	*	Splunk Universal Forwarder
	*	Windows Event Viewer / Event Logs
	*	SPL

Project Status

Core monitoring and investigation workflow completed.
