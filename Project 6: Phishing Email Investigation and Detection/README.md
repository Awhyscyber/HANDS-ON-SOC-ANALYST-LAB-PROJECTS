Phishing Email Investigation & Detection”}

SOC Project 4 — Phishing Email Investigation & Detection

Project Overview

This project focused on investigating a simulated phishing email from the perspective of a Security Operations Center (SOC) analyst.

The objective was to identify phishing indicators, analyze the sender and embedded URL, examine social-engineering techniques, perform a reputation/blacklist check, and analyze simulated email-header authentication results.

This project helped me practice a structured process for investigating suspicious emails while preserving evidence and documenting findings clearly.

	Note: The email used in this project was created specifically for cybersecurity training. The email headers were also simulated for educational purposes and do not represent a real malicious email.

Objectives

The main objectives of this project were to:
	*	Investigate a suspicious phishing email.
	*	Identify indicators commonly associated with phishing.
	*	Analyze the sender address and domain.
	*	Examine a suspicious URL without opening it.
	*	Perform a domain reputation/blacklist check.
	*	Identify social-engineering techniques such as urgency and threats.
	*	Analyze simulated email authentication results.
	*	Examine the Return-Path, Received header, and Message-ID.
	*	Document findings using a SOC investigation approach.

Tools Used
	•	Text editor — Used to create and examine the simulated phishing email.
	•	MXToolbox — Used for a domain blacklist/reputation check.
	•	URLhaus — Used as an additional threat-intelligence reference during the reputation investigation.
	•	Email headers — Used to practice technical email analysis.
	•	GitHub — Used to document the project and preserve investigation evidence.

Investigation Scenario

The simulated email was designed to imitate a Microsoft 365 security notification.

Email details

Displayed sender:
Microsoft 365 security

Sender address:
security@micr0soft-support.example

Subject:
URGENT: Your Microsoft 365 account will be disabled

Embedded URL:
https://micr0soft-support.example/verify

The message attempted to persuade the recipient to verify their account by creating a sense of urgency and threatening account suspension.

Investigation Process

1. Initial Email Review
I first examined the email content without interacting with the embedded link.The email immediately contained several characteristics that required further investigation.The sender claimed to represent Microsoft 365 Security, but the actual email address used a suspicious look-alike domain.

Initial observation

The domain contained:
micr0soft

instead of:
microsoft

The letter o had been replaced with the number 0.

This is an example of look-alike domain impersonation, a technique that can make a malicious address appear legitimate at first glance.

2. Sender Address Analysis

Sender
Microsoft 365 security <security@micr0soft-support.example>

Finding

The displayed sender name attempted to create the appearance of a Microsoft security notification, while the actual domain was not Microsoft’s legitimate domain.

SOC observation
A SOC analyst should not rely only on the displayed sender name. The complete email address and domain should be examined.

3. URL Analysis

The email contained the following URL:
https://micr0soft-support.example/verify

I did not open the link. Instead, I analyzed the URL as text.

Findings
The URL contained the same look-alike domain:
micr0soft-support.example

The use of /verify was also consistent with an attempt to make the recipient perform an account-verification action.

SOC lesson

A suspicious link should be analyzed without visiting it directly. A URL can appear convincing while leading to infrastructure controlled by an attacker.

4. Domain Reputation / Blacklist Check
I performed a reputation check using MXToolbox.

Result

MXToolbox returned:
OK

This demonstrated an important investigation lesson:

	A domain not appearing on a particular blacklist does not automatically mean that it is legitimate or safe.

Different security services maintain different datasets and detection criteria.

An additional threat-intelligence reference was used during the investigation to demonstrate why multiple sources may be necessary when evaluating an indicator.

SOC lesson

Reputation checks should be treated as one source of evidence, not as the only factor used to classify an email.

5. Social Engineering Analysis
The email used several social-engineering techniques to pressure the recipient.

Urgency

The subject line began with:
URGENT:

Threat

The message stated:
Your Microsoft 365 account will be disabled

It also stated that the account would be disabled within 24 hours unless the recipient acted immediately.

Why this matters

The message attempts to create fear and urgency so that the recipient acts before carefully verifying the sender and destination.

This is a common characteristic
