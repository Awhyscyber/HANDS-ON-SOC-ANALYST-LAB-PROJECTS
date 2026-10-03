
Project 8 — Web Attack Detection & Investigation

Project Overview

This project focused on detecting and investigating suspicious web activity against an Apache web server.

I built a small isolated lab using Kali Linux as the testing/attacking machine and Ubuntu Server as the web server. I configured Apache on Ubuntu, generated controlled web attack traffic from Kali, and investigated the resulting activity using Apache access logs and TShark.

The main goal was to understand how a SOC analyst can identify suspicious web requests, determine the source of the activity, examine HTTP response codes, and distinguish between an attempted attack and a successful compromise.

Objectives

The objectives of this project were to:
	*	Deploy and verify an Apache web server.
	*	Establish a normal HTTP traffic baseline.
	*	Generate controlled suspicious web requests from Kali Linux.
	*	Investigate web requests using Apache access logs.
	*	Capture HTTP traffic using TShark.
	*	Identify the source IP address responsible for the activity.
	*	Analyze HTTP response codes.
	*	Determine whether the simulated attacks were successful.
	*	Practice documenting findings from a SOC analyst perspective.

Lab Environment

Kali Linux — Testing/Attacking Machine
	*	IP address: 192.168.56.109
	*	Used to generate controlled HTTP requests.
	*	Used curl to send requests to the Apache server.

Ubuntu Server — Web Server
	*	IP address: 192.168.56.105
	*	Apache HTTP Server
	*	Apache access log: /var/log/apache2/access.log
	*	Network interface used for the lab: enp0s8

Tools Used
	*	Apache HTTP Server
	*	Kali Linux
	*	Ubuntu Server
	*	curl
	*	Apache access logs
	*	TShark
	*	Linux command-line tools such as grep, tail, and awk

1. Network Connectivity and Web Server Setup

I configured the Ubuntu server with the host-only IP address:
192.168.56.105

Kali Linux was configured with:
192.168.56.109

Both systems were placed on the same 192.168.56.0/24 network.

I verified connectivity between the machines using:
ping -c 4 192.168.56.105

The ping test returned successful replies.

I then installed and started Apache on Ubuntu.

To verify that Apache was functioning locally, I used:
curl http://127.0.0.1

Apache returned HTML content, confirming that the web server was running. I also verified that Kali could access the server:
curl http://192.168.56.105

After allowing HTTP traffic through the Ubuntu firewall, Kali successfully received the Apache HTML page.

2. Establishing a Normal HTTP Baseline

Before generating suspicious activity, I tested normal HTTP communication. I used:
curl -I http://192.168.56.105

The server returned:
HTTP/1.1 200 OK

This established a normal baseline. A baseline is important during security investigations because an analyst needs to understand what normal traffic looks like before identifying abnormal or suspicious activity.

3. Directory Traversal-Style Request

I generated a controlled request attempting to access:
/etc/passwd

The request was sent from Kali to the Ubuntu web server. Apache recorded the request in its access log as:
GET /etc/passwd HTTP/1.1

The server returned:
404

Investigation Finding

The request was suspicious because /etc/passwd is a sensitive Linux system file and is not normally requested through a public web server.However, the HTTP 404 response showed that Apache did not serve the requested resource.

Therefore, this investigation provides evidence of an attempted suspicious request, not evidence of successful file disclosure.

4. SQL Injection-Style Request

I then generated a controlled SQL-injection-style request using a URL parameter.The request contained an encoded version of:
?id=1' OR '1'='1

Apache recorded the request in its access log. The request returned:
200

Investigation Finding

Although the request returned HTTP 200, this does not mean that SQL injection was successful.The Ubuntu server was running a basic Apache installation and did not have a database-backed web application behind this endpoint.

Therefore, there was no evidence that a database was accessed or compromised.

This demonstrated an important SOC investigation principle:

	A suspicious request and a successful compromise are not the same thing. An analyst must examine the surrounding application and server evidence before determining whether exploitation actually succeeded.

5. Web Reconnaissance Request

I simulated basic web reconnaissance by requesting:
/admin

The server returned:
404

Apache recorded the request in the access log. This represents the type of activity that can occur during web reconnaissance, where an attacker searches for potentially interesting administrative or application paths.Because the /admin resource did not exist, the request was unsuccessful.

6. Apache Log Investigation

After generating the controlled traffic, I investigated the Apache access log:
sudo tail -n 5 /var/log/apache2/access.log

I also searched specifically for the suspicious requests:
sudo grep -E 'GET /etc/passwd|GET /admin|id=1' /var/log/apache2/access.log

This allowed me to isolate the relevant events from the normal web traffic.

The Apache logs provided important investigation information including:
	* Source IP address
	*	Timestamp
	*	HTTP method
	*	Requested URI
	*	HTTP protocol
	*	HTTP response code
	*	Response size

7. Identifying the Source IP

I extracted the source IP addresses from the relevant Apache log entries using:
sudo grep -E 'GET /etc/passwd|GET /admin|id=1' /var/log/apache2/access.log | awk '{print $1}'

The source IP was: 192.168.56.109
This corresponded to my Kali Linux machine.

The target web server was: 192.168.56.105

Investigation Flow

Kali Linux
192.168.56.109
       |
       | Suspicious HTTP requests
       ↓
Ubuntu Apache Server
192.168.56.105
       |
       ↓
Apache access.log
       |
       ↓
SOC investigation

This allowed me to establish the source and destination of the simulated activity.

8. HTTP Response Code Analysis

I extracted the requested paths and HTTP response codes using:
sudo grep -E 'GET /etc/passwd|GET /admin|id=1' /var/log/apache2/access.log | awk '{print $7, $9}'

The investigation produced results including:
/etc/passwd    404
/admin         404
/admin         404
/?id=...       200

Interpretation
404 Not Found

The requested resource was not available. This occurred with the /etc/passwd and /admin requests.

200 OK
The server successfully returned a response for the SQL-injection-style request.

However, the 200 response only indicates that the HTTP request received a successful response. It does not prove that SQL injection occurred.

9. Network Traffic Investigation with TShark

I also captured HTTP traffic at the network level using TShark. The capture was performed on the Ubuntu server’s enp0s8 interface using:
sudo tshark -i enp0s8 -f "tcp port 80" -c 10

Command breakdown
	*	sudo — runs the command with administrative privileges.
	*	tshark — command-line version of Wireshark.
	*	-i enp0s8 — captures traffic on the enp0s8 interface.
	*	-f "tcp port 80" — filters traffic associated with HTTP port 80.
	*	-c 10 — stops the capture after 10 packets.

The capture provided network-level evidence that HTTP traffic was traveling between Kali and the Ubuntu web server.

10. Investigation Timeline

Activity	Source	Target	Result
Normal HTTP request	Kali 192.168.56.109	Ubuntu 192.168.56.105	200 OK
/etc/passwd request	Kali	Ubuntu Apache	404 Not Found
SQL-injection-style request	Kali	Ubuntu Apache	200 OK
/admin reconnaissance	Kali	Ubuntu Apache	404 Not Found
HTTP packet capture	Kali → Ubuntu	Apache	Captured by TShark

11. Key Findings

Finding 1 — Suspicious File Access Attempt

A request for: /etc/passwd was observed. This is suspicious because it targets a sensitive operating-system file.The request returned 404, so there was no evidence from this test that the file was successfully disclosed.

Finding 2 — SQL Injection-Style Activity

A request containing: ?id=1' OR '1'='1 was observed. The server returned 200 OK, but this did not demonstrate successful SQL injection because the lab did not contain a database-backed application.

Finding 3 — Web Reconnaissance

A request for: /admin was observed. The server returned 404, indicating that the requested path was not found.

Finding 4 — Source Identification

All of the simulated suspicious activity originated from: 192.168.56.109 which was the Kali Linux testing machine.

12. SOC Analyst Perspective

This project helped me practice the basic workflow of a web-security investigation:
Detect
   ↓
Collect Evidence
   ↓
Identify Source
   ↓
Analyze Request
   ↓
Analyze Response
   ↓
Determine Impact
   ↓
Document Findings

One of the most important lessons was that not every suspicious request represents a successful attack.

For example:

Suspicious request
       ↓
HTTP 404
       ↓
Attempt observed
       ↓
No evidence of successful exploitation

Similarly:

Suspicious SQL-style request
       ↓
HTTP 200
       ↓
Successful HTTP response
       ↓
Not automatically successful SQL injection

An analyst needs additional evidence before declaring that a system has been compromised.

13. Defensive Recommendations


14. MITRE ATT&CK Relevance

The activity in this lab can be related to techniques involving:T1190 — Exploit Public-Facing Application

The simulated requests represented attempts to interact with a web-facing service in potentially malicious ways. However, this lab did not demonstrate successful exploitation of the Apache server.

Reconnaissance

The /admin request demonstrated basic discovery of potentially interesting web paths. The activity was performed inside an isolated lab and was intended for defensive investigation practice.

15. What I Learned

Through this project, I learned how to:
	*	Deploy and verify an Apache web server.
	*	Establish a normal HTTP baseline.
	*	Generate controlled suspicious web requests.
	*	Understand directory traversal-style activity.
	*	Understand SQL-injection-style requests.
	*	Recognize basic web reconnaissance.
	*	Read Apache access logs.
	*	Extract useful fields from logs.
	*	Identify a source IP from web-server logs.
	*	Interpret HTTP status codes.
	*	Capture HTTP traffic using TShark.
	*	Distinguish an attack attempt from confirmed successful exploitation.
	*	Document security findings from a SOC analyst perspective.

16. Skills Demonstrated

Security Operations
	*	Web attack detection
	*	Log analysis
	*	Event investigation
	*	IOC identification
	*	Source IP identification
	*	Evidence collection
	*	Incident documentation

Networking
	*	TCP/IP
	*	HTTP
	*	TCP port 80
	*	Host-only networking
	*	Network traffic capture


17. Final Conclusion

This project demonstrated a basic but realistic SOC investigation of suspicious web activity. I configured an Apache web server on Ubuntu, generated controlled web attack traffic from Kali Linux, captured the resulting events in Apache logs, identified the source IP, analyzed HTTP response codes, and captured network traffic using TShark.

The investigation identified multiple suspicious requests, including a request for /etc/passwd, a SQL-injection-style request, and an /admin reconnaissance request. The evidence showed attempted suspicious activity but no confirmed successful compromise in this lab.

The project reinforced the importance of investigating the complete attack chain rather than relying on a single alert or HTTP status code.

Evidence

Screenshots for this project include:
	1.	Ubuntu Apache installation and service status
	2.	Successful Apache local test
	3.	Kali-to-Ubuntu HTTP connectivity
	4.	HTTP/1.1 200 OK baseline
	5.	/etc/passwd request
	6.	Apache log showing /etc/passwd
	7.	SQL-injection-style request
	8.	Apache log showing the SQL-style request
	9.	/admin reconnaissance request
	10.	Apache log showing /admin
	11.	TShark HTTP traffic capture
	12.	Source IP identification
	13.	HTTP response-code analysis
	14.	Final Apache investigation evidence

Project Outcome

This project strengthened my understanding of how a SOC analyst can use web-server logs and network traffic together to detect, investigate, and document suspicious web activity.
