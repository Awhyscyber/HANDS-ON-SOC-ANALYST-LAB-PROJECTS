SOC Project 1 — Windows Authentication Monitoring & Investigation with Splunk

Project Overview

This project is a hands-on Security Operations Center (SOC) lab focused on monitoring and investigating Windows authentication activity using Splunk Enterprise.

The project simulates a basic SOC workflow:

	Log Collection → Detection → Investigation → Correlation → Analysis → Incident Documentation

A Windows Server was configured as the monitored endpoint. Windows Security Event Logs were collected using the Splunk Universal Forwarder and sent to Splunk Enterprise 10.4.3 running on Ubuntu Server.

The investigation focused on repeated Windows Event ID 4625 — Failed Logon events.

Objectives

The main objectives were to:
	•	Build a small SOC monitoring environment.
	•	Configure Splunk Enterprise as the SIEM.
	•	Configure Windows Server as a log source.
	•	Install and configure Splunk Universal Forwarder.
	•	Forward Windows Security Event Logs to Splunk.
	•	Detect failed authentication events.
	•	Investigate Event ID 4625.
	•	Identify the affected account.
	•	Identify the recorded source address.
	•	Analyze the logon type and failure reason.
	•	Investigate successful authentication using Event ID 4624.
	•	Build an evidence-based timeline.
	•	Produce a professional SOC incident report.

Lab Architecture

                  SOC Analyst
                      |
                      v
              Splunk Web Interface
                Ubuntu Server
              Splunk Enterprise
                  10.4.3
                      |
                 TCP 9997
                      |
                      v
             Splunk Universal
                Forwarder
                      |
                      v
               Windows Server
                      |
             Windows Security
                  Event Log

Lab Components

Component	Role
Windows Server	Endpoint / log source
Ubuntu Server	SIEM server
Splunk Enterprise 10.4.3	SIEM
Splunk Universal Forwarder	Log forwarding
VirtualBox	Virtualization
Windows Security Event Log	Authentication telemetry

Network Configuration

Ubuntu Server

Host-only IP:

192.168.56.105

Splunk Web:

http://192.168.56.105:8000

Splunk receiving port:

192.168.56.105:9997

Windows Server

The Windows Server was configured to communicate with the Ubuntu Server through the lab network.

The Forwarder destination was:

192.168.56.105:9997

Step 1 — Install Splunk Enterprise

Splunk Enterprise 10.4.3 was installed on Ubuntu Server.

The Splunk Web interface was accessed through:

http://192.168.56.105:8000

The SIEM was then configured to receive forwarded data on TCP port:

9997

Step 2 — Install Splunk Universal Forwarder

The Splunk Universal Forwarder was installed on Windows Server.

Installation directory:

C:\Program Files\SplunkUniversalForwarder

The Forwarder was configured to send data to:

192.168.56.105:9997

The configuration was verified using:

& "C:\Program Files\SplunkUniversalForwarder\bin\splunk.exe" list forward-server

The final result showed the destination as an active forward:

Active forwards: 192.168.56.105:9997

Step 3 — Configure Windows Security Log Collection

The Windows Security Event Log was configured through the Forwarder’s inputs.conf.

Configuration:

[WinEventLog://Security]
disabled = 0
index = main

This enabled collection of Windows Security events and sent them to the Splunk main index.

Step 4 — Verify Log Ingestion

The initial Splunk search was:

index=main

Windows events were successfully received.

This confirmed that the basic pipeline was functioning:

Windows
    ↓
Universal Forwarder
    ↓
TCP 9997
    ↓
Splunk

Step 5 — Detect Failed Authentication

The primary detection search was:

index=main EventCode=4625

Event ID 4625 represents a failed Windows logon.

The investigation identified:

10 failed authentication events.

Step 6 — Investigate the Failed Authentication

An individual Event ID 4625 event was expanded in Splunk.

The important fields were documented.

Account

Administrator

Source

127.0.0.1

Logon Type

2

Failure Reason

Unknown username or bad password

Step 7 — Understand the Source

The source address was:

127.0.0.1

This is the localhost/loopback address.

This was an important finding.

The event therefore did not provide evidence that the failed authentication came from the Ubuntu Server or another remote machine.

Because of this, the investigation avoided incorrectly labelling the activity as a confirmed remote brute-force attack.

Step 8 — Investigate Successful Authentication

Event ID 4624 was investigated using:

index=main EventCode=4624

Event ID 4624 represents a successful Windows logon.

A successful authentication event was recorded at:

09/23/2026 07:19:14 AM

The successful event occurred significantly later than the observed failed authentication periods.

Therefore, the investigation did not establish that the successful login immediately followed the failed attempts.

Investigation Timeline

Observed failed authentication activity occurred during at least two periods:

09/20/2026 ~03:29 AM
        |
        | 4625 failed authentication
        |
09/21/2026 ~05:32 PM
        |
        | 4625 failed authentication
        |
09/23/2026 07:19:14 AM
        |
        | 4624 successful authentication

Investigation Findings

Investigation Item	Result
Failed event	4625
Number of failures identified	10
Account	Administrator
Source	127.0.0.1
Logon Type	2 — Interactive
Failure reason	Unknown username or bad password
Failed activity period 1	~09/20/2026 03:29 AM
Failed activity period 2	~09/21/2026 05:32 PM
Successful event	4624
Successful event observed	09/23/2026 07:19:14 AM

SOC Interpretation

The investigation identified repeated failed authentication attempts against the Administrator account.

Repeated failed authentication can have several explanations, including:
	•	Incorrect credentials
	•	User error
	•	Password guessing
	•	Automated authentication attempts
	•	Misconfigured software or services

The available evidence does not establish which explanation is responsible.

The source address 127.0.0.1 is particularly important because it represents localhost.

Therefore, this project does not claim that a remote attacker or Ubuntu Server performed the failed authentication attempts.

The evidence supports the following finding:

	Repeated failed local authentication attempts were detected against the Administrator account. The activity is security-relevant and warrants investigation, but the available evidence does not establish a confirmed remote brute-force attack.

Splunk Searches Used

View all events

index=main

Find failed logons

index=main EventCode=4625

Count failed logons

index=main EventCode=4625 | stats count

Find successful logons

index=main EventCode=4624

Evidence Collected

The following evidence was reviewed during the investigation:
	•	Splunk Security Event Log ingestion
	•	Event ID 4625
	•	Administrator account
	•	Source address
	•	Logon Type
	•	Failure reason
	•	Failed-login timestamps
	•	Event ID 4624
	•	Successful-login timestamp

SOC Investigation Workflow Demonstrated

1. Collect logs
       ↓
2. Verify ingestion
       ↓
3. Search for suspicious events
       ↓
4. Identify Event ID
       ↓
5. Examine event fields
       ↓
6. Identify account
       ↓
7. Identify source
       ↓
8. Examine logon type
       ↓
9. Examine failure reason
       ↓
10. Compare successful authentication
       ↓
11. Build timeline
       ↓
12. Interpret evidence
       ↓
13. Document findings

Skills Demonstrated

SIEM
	•	Splunk Enterprise
	•	SPL searches
	•	Log ingestion
	•	Event investigation

Windows Security
	•	Windows Security Event Logs
	•	Event ID 4624
	•	Event ID 4625
	•	Authentication investigation
	•	Logon Types

SOC Analysis
	•	Alert investigation
	•	Evidence collection
	•	Timeline analysis
	•	Authentication monitoring
	•	Event correlation
	•	Incident documentation
	•	Avoiding unsupported conclusions

Infrastructure
	•	VirtualBox
	•	Windows Server
	•	Ubuntu Server
	•	Network communication
	•	Splunk Universal Forwarder

What I Learned

One of the most important lessons from this project was that a security alert must be investigated using context.

For example, finding multiple Event ID 4625 events does not automatically prove a brute-force attack.

I learned to examine:
	•	How many failures occurred?
	•	Which account was targeted?
	•	Where did the event originate?
	•	What type of logon was involved?
	•	Why did authentication fail?
	•	When did the events occur?
	•	Were successful logons observed?
	•	Does the evidence support the suspected attack scenario?

This helped me understand the difference between detecting suspicious activity and proving what actually happened.

Challenges Encountered

During the build, several configuration and connectivity issues were encountered while establishing communication between Windows Server and the Ubuntu Splunk server.

The troubleshooting process included:
	•	Verifying the Splunk receiving configuration.
	•	Configuring the Forwarder destination.
	•	Resolving TCP connectivity to port 9997.
	•	Configuring the Windows Security Event Log input.
	•	Restarting the Forwarder after configuration changes.
	•	Verifying that the forwarder became active.
	•	Confirming that Windows events appeared in Splunk.

The final result was a working Windows-to-Splunk log pipeline.
The original investigation scenario was designed around possible brute force attack detection
but because i use an intel pentium 8gb ram laptop,i couldn't get wazuh running on my lab VMs

Portfolio Evidence

Recommended screenshots for this repository:

/screenshots/
    splunk-web.png
    active-forwarder.png
    security-events.png
    event-4625.png
    event-4625-fields.png
    event-4624.png
    investigation-results.png


Rebuild Guide

If I want to recreate this project later, the basic process is:

1. Start the VMs

Start:
	•	Ubuntu Server
	•	Windows Server

2. Confirm Splunk Server

Ubuntu Server:

192.168.56.105

Splunk Web:

http://192.168.56.105:8000

3. Start the Windows Forwarder

PowerShell:

& "C:\Program Files\SplunkUniversalForwarder\bin\splunk.exe" start

4. Verify forwarding

& "C:\Program Files\SplunkUniversalForwarder\bin\splunk.exe" list forward-server

Expected destination:

192.168.56.105:9997

5. Verify Security Event collection

The Forwarder should have:

[WinEventLog://Security]
disabled = 0
index = main

6. Open Splunk

http://192.168.56.105:8000

7. Search Windows events

index=main

8. Investigate failed authentication

index=main EventCode=4625

9. Investigate successful authentication

index=main EventCode=4624

10. Expand individual events

Record:
	•	Timestamp
	•	Account
	•	Source
	•	Logon Type
	•	Failure reason
	•	Other relevant fields

11. Build the investigation

Compare:

4625 = failed authentication
4624 = successful authentication

Then determine whether the events are related based on their timestamps, account information, source and other available evidence.

Future Improvements

If this project is expanded later, possible improvements include:
	•	Create a dedicated Splunk dashboard.
	•	Create saved searches for Event ID 4625.
	•	Create threshold-based alerts.
	•	Monitor multiple Windows accounts.
	•	Add more Windows Event IDs.
	•	Add endpoint telemetry.
	•	Correlate authentication activity with network logs.
	•	Add Suricata or Zeek telemetry.
	•	Create a formal incident-response workflow.
	•	Add automated alerting.
	•	Develop additional SOC investigation scenarios.

Interview Talking Point

A concise way to explain this project during an interview:

	“I built a small SOC lab using Windows Server, Splunk Universal Forwarder and Splunk Enterprise running on Ubuntu. I configured Windows Security Event Logs to be forwarded into Splunk, then investigated Event ID 4625 failed-logon events. I identified the targeted Administrator account, source address, logon type and failure reason, and correlated the activity with Event ID 4624 successful logons. An important part of the investigation was recognizing that the source was 127.0.0.1, so I didn’t incorrectly classify it as a confirmed remote brute-force attack. I documented the evidence and investigation as a SOC incident report.”

Project Outcome

Project completed successfully.

The lab demonstrates the complete basic SOC investigation lifecycle:

Windows Security Logs
        ↓
Log Forwarding
        ↓
Splunk SIEM
        ↓
Detection
        ↓
Event Investigation
        ↓
Timeline Analysis
        ↓
Evidence-Based Assessment
        ↓
Incident Documentation

This project forms part of my practical cybersecurity portfolio and demonstrates hands-on experience with SIEM monitoring, Windows authentication logs and SOC investigation methodology.
