This project involved exploring file encryption and decryption, recovering FTP credentials, authenticating to an FTP server, transferring encrypted files, and decrypting confidential client information.
The lab demonstrated how encryption can protect sensitive information and how poor credential management and insecure file-transfer methods can create security risks.

Using the Cisco Cybersecurity Essentials lab environment, I completed four objectives involving two users, Mary and Bob, their FTP credentials, an encrypted client information file, and a decryption key communicated through email.
I used OpenSSL to decrypt files, Command Prompt to interact with an FTP server, and the Linux terminal to verify files and inspect decrypted information.
The final objective demonstrated the importance of protecting personally identifiable information (PII), financial information, credentials, and encryption keys.

Primary focus: Data confidentiality, encryption, decryption, authentication, file transfer, and encryption key management.

Project Objectives
The main objectives of this project were to: 1. Discover Mary’s FTP credentials by decrypting an encrypted file. 2. Authenticate to an FTP server using Mary’s credentials and upload an encrypted client information file. 3. Discover Bob’s FTP credentials, authenticate to the FTP server, and download the encrypted client information file. 4. Retrieve a decryption key from email and decrypt the downloaded file to access its contents. 5. Understand the security implications of exposed credentials, encryption keys, and insecure file transfers. 6. Relate the lab activities to real-world cybersecurity operations and data protection.

Lab Environment and Tools
The following tools and components were used during the lab.
Tool or Component Purpose Cisco Cybersecurity Essentials lab Provided the environment for performing the encryption and file-transfer exercises. Laptop BR1 Used to access Mary’s encrypted FTP credentials and perform the first FTP file-transfer activity. Laptop BR2 Used to access Bob’s encrypted FTP credentials and download the encrypted client information file. CSE lab VM Used to decrypt files and inspect the resulting information. Text Editor Used to open credential files and copy their encrypted contents. Pluma Text Editor Used to save the copied encrypted client information as a local file on the lab VM. Command Prompt Used to connect to the FTP server and transfer files. Linux terminal Used to run OpenSSL commands and verify and inspect files. OpenSSL Used to decrypt encrypted files. FTP Used to authenticate to a remote server and transfer files. Email application Used to retrieve the decryption key sent by Mary to Bob.

Files Used
The lab involved the following files: • maryftplogin.txt — contained encrypted information used to recover Mary’s FTP credentials. • bobftplogin.txt — contained encrypted information used to recover Bob’s FTP credentials. • clientinfo.enc — contained encrypted client information transferred between the lab machines and the FTP server. • clientinfo.txt — contained the readable client information produced after successful decryption.

Practical Implementation
Objective 1: Discover Mary’s FTP Credentials

Step 1: Access the encrypted credentials
I opened Laptop BR1 and navigated to:
Text Editor → File → Open
I selected maryftplogin.txt and copied its encrypted contents.

Step 2: Decrypt the file using OpenSSL
I switched to the Cybersecurity Essentials lab VM and used the echo and OpenSSL commands to decrypt the copied contents.
The lab supplied the necessary decryption password and encryption settings.
The decryption process revealed Mary’s FTP account information, which I recorded for the next objective.

What I Learned
This activity demonstrated that encrypted information may become readable when an authorized person has the correct decryption key and encryption parameters.
I learned the difference between encrypted data, commonly called ciphertext, and decrypted data, which is returned to a readable form.
I also learned that protecting credentials involves more than encrypting them. Access to the encryption key must also be controlled.
Security Significance
If an attacker obtains an encrypted credential file and the associated password or decryption key, the attacker may be able to recover the credentials and attempt to access protected systems.
Organizations should therefore protect credential files, restrict access to decryption keys, and monitor suspicious authentication attempts.

Objective 2: Upload Confidential Data Using FTP

Step 1: Connect to the FTP server
Using the recovered credentials, I opened Command Prompt on Laptop BR1 and connected to the FTP server.
The connection was established using the server address supplied by the lab.

Step 2: Authenticate
I supplied Mary’s FTP account information when prompted.
This demonstrated how an FTP server uses credentials to authenticate a user before granting access to available files and permitted operations.

Step 3: Upload the encrypted file
After authenticating, I used the FTP put command to upload the encrypted client information file.
The command was:
put clientinfo.enc
The put command uploads a local file to the remote FTP server.

What I Learned
I learned how to: • Establish an FTP connection. • Authenticate using a username and password. • Upload a file to a remote server. • Understand the role of authentication in controlling access to server resources.
Security Significance
Traditional FTP does not encrypt credentials or file contents while they are being transmitted. This can expose sensitive information to interception on an untrusted network.
Although encrypting clientinfo.enc helps protect its contents, it does not automatically protect FTP credentials transmitted during authentication.
Organizations should use secure transfer protocols such as SFTP or properly configured FTPS when transferring sensitive information.

Objective 3: Discover Bob’s FTP Credentials and Download the Encrypted File
Step 1: Retrieve Bob’s encrypted credentials
I opened Laptop BR2 and navigated to:
Text Editor → File → Open
I selected bobftplogin.txt and copied the encrypted contents.

Step 2: Decrypt Bob’s credentials
I switched to the CSE lab VM and used the echo and OpenSSL commands to decrypt the copied contents.
The operation successfully revealed Bob’s FTP account information.

Step 3: Connect to the FTP server
I returned to Laptop BR2 and opened Command Prompt.
I connected to the FTP server and authenticated using Bob’s recovered credentials.

Step 4: Download the encrypted client information
After authentication, I used the FTP get command to download the encrypted file from the server.
The command was:
get clientinfo.enc
The get command downloads a file from the remote FTP server to the local computer.

Step 5: Verify the download
I closed the FTP session using:
quit
I then used:
dir
The directory listing allowed me to verify that clientinfo.enc had been downloaded successfully.

What I Learned
This objective reinforced my understanding of: • Credential recovery through decryption. • Authentication using recovered credentials. • Downloading files from a remote server. • Using FTP commands to interact with remote resources. • Verifying file transfers rather than assuming they succeeded.
Security Significance
The exercise demonstrated how recovered credentials can be used to access a server and transfer files.
In a real environment, an attacker who obtains valid credentials may be able to access resources that the compromised account is authorized to use.
A security analyst investigating suspicious FTP activity would examine authentication records, source addresses, account permissions, and available file-transfer logs to determine whether access was authorized.

Objective 4: Decrypt the Sensitive Client Information File
Step 1: Retrieve the decryption key
After closing the terminal on Laptop BR2, I opened the email application.
I located the email with the subject:
Decryption key
The email contained the decryption key sent by Mary to Bob.
I recorded the key for use in decrypting the client information file.

Step 2: Copy the encrypted file
I opened Text Editor on Laptop BR2 and navigated to:
File → Open → clientinfo.enc
I copied the file’s encrypted contents.

Step 3: Create the local encrypted file
I opened Pluma Text Editor on the CSE lab VM, pasted the copied contents, and saved the file as:
clientinfo.enc
I closed Pluma after saving the file.

Step 4: Verify the encrypted file
I opened the terminal and ran:
ls
This command listed the files in the current working directory.
I used the output to confirm that clientinfo.enc was present before attempting decryption.

Step 5: Decrypt the file using OpenSSL
I ran the following command:
openssl aes-256-cbc -pbkdf2 -a -d -in clientinfo.enc -out clientinfo.txt
The terminal prompted me for the decryption password, and I entered the key recorded from Mary’s email.
The command completed the decryption process.
Understanding the OpenSSL Command
Option Explanation openssl Invokes the OpenSSL cryptographic utility. aes-256-cbc Specifies AES with a 256-bit key in Cipher Block Chaining mode. -pbkdf2 Specifies PBKDF2 for deriving encryption keys from passwords. -a Enables Base64 decoding during decryption. -d Selects decryption mode. -in clientinfo.enc Specifies the encrypted input file. -out clientinfo.txt Specifies the output file for the decrypted information.
The command reads the encrypted data from clientinfo.enc and writes the decrypted result to clientinfo.txt.

Step 6: Verify the decrypted file
I used the following command to check whether the output file existed:
ls -l clientinfo.txt
I then displayed its contents using:
cat clientinfo.txt
The output contained names, addresses, and credit information belonging to different people.
This confirmed that the encrypted file had been successfully decrypted and that the confidential client information was readable.
I did not need to share the actual personal or financial information to establish the result of the exercise.

What I Learned
This objective demonstrated that encryption can protect sensitive information from being read directly, but the protection depends on the security of the decryption key and the implementation.
I learned how to: • Retrieve a decryption key from an email. • Prepare an encrypted file for local processing. • Verify the presence of an input file. • Decrypt an AES-256-CBC encrypted file using OpenSSL. • Write the decrypted contents to a separate output file. • Inspect the output to confirm that decryption succeeded.

Security Significance
The decrypted file contained sensitive personal and financial information.
If such information were exposed in a real organization, it could create risks including identity theft, financial fraud, privacy violations, and reputational damage.
The exercise also highlighted the risks of transmitting decryption keys through email without appropriate security controls.
If an unauthorized person obtained both the encrypted file and its decryption key, the person could potentially recover the confidential information.
Organizations should protect encryption keys separately from the data they protect, restrict access to sensitive files, and use secure channels for transferring credentials and keys.

Key Cybersecurity Concepts Learned
5.1 Encryption and Decryption
Encryption transforms readable information, known as plaintext, into ciphertext using a cryptographic algorithm and key.
Decryption reverses the process for someone who has the appropriate key and parameters.
In this project, I used OpenSSL to decrypt credential files and recover the contents of an encrypted client information file.
I learned that encryption helps protect confidentiality, but it does not eliminate every security risk.

5.2 AES-256-CBC
AES is a widely used symmetric encryption algorithm.
In symmetric encryption, the same secret key is used to encrypt and decrypt data.
AES-256 uses a 256-bit encryption key. CBC, or Cipher Block Chaining, is one mode in which AES can operate.
The lab helped me understand how an encrypted file can be decrypted when the correct password and encryption settings are available.
Additional security lesson: CBC encryption alone does not provide authentication or integrity protection. Modern systems should generally use authenticated encryption, such as AES-GCM, or combine encryption with an appropriate authentication mechanism.

5.3 PBKDF2
PBKDF2 is a password-based key derivation function.
It processes a password with a salt and repeated computational operations to derive key material for cryptographic use.
Its purpose is to make password-derived keys more resistant to password-guessing attacks than a simple, direct password-to-key conversion.
Its effectiveness still depends on appropriate parameters and the strength of the original password.

5.4 Base64 Encoding
Base64 represents binary data using printable characters.
It is an encoding scheme, not an encryption algorithm.
Base64 does not make sensitive information confidential because it can be decoded without a secret key.
In the lab, the -a option enabled Base64 processing as part of the OpenSSL decryption operation.

5.5 Authentication and Authorization
Authentication verifies a user’s identity, commonly through credentials.
Authorization determines which resources and actions an authenticated user is permitted to access.
During this project, I authenticated to the FTP server using Mary’s and Bob’s account information.
This demonstrated the importance of protecting credentials and limiting account permissions to the resources required for legitimate tasks.

5.6 FTP and Secure File Transfers
FTP is a protocol for transferring files between systems.
Traditional FTP does not encrypt credentials or transferred data in transit.
SFTP and FTPS provide different approaches to secure file transfer: • SFTP: Transfers files over the SSH protocol. • FTPS: Adds TLS protection to FTP connections.
The project taught me to distinguish between encrypting a file’s contents and protecting the entire file-transfer session.

5.7 Sensitive Data and Personally Identifiable Information
The final decrypted file contained names, addresses, and credit information.
This demonstrated why organizations must identify and protect sensitive information throughout its lifecycle.
Appropriate safeguards include access controls, encryption, secure transfer mechanisms, key management, monitoring, and secure deletion when data is no longer required.

5.8 Encryption Key Management
Encryption is only as effective as the overall system used to protect the data and its keys.
If an unauthorized person obtains the encrypted file and the necessary key, the person may be able to decrypt the data.
Encryption keys should therefore be protected against unauthorized access and should not be carelessly transmitted or stored alongside the data they protect.

Security Risks Identified
The lab demonstrated several potential security risks.
Risk Why It Matters Recommended Control Exposed credentials Recovered credentials may enable unauthorized access to an account. Protect credential stores, use strong unique passwords, and apply multifactor authentication where supported. Insecure FTP authentication Traditional FTP can expose usernames and passwords in transit. Use SFTP or properly configured FTPS.
Insecure key distribution Unauthorized access to a decryption key may compromise encrypted information. Use a secure key-distribution method and restrict key access. Sensitive data exposure Names, addresses, and credit information can be misused if disclosed. Apply least privilege, encryption, monitoring, and data-loss prevention controls where appropriate. Excessive account permissions A compromised account may expose more information than necessary. Apply least privilege and review account permissions regularly. Weak passwords Password-derived encryption may be vulnerable to guessing if passwords or parameters are weak. Use strong passwords and appropriate key-derivation parameters. Insufficient monitoring Unauthorized access or transfers may go undetected. Monitor authentication events, file transfers, and unusual access patterns.
These are risks demonstrated by the lab scenario or associated with the technologies used. They should not be interpreted as proof that a real organization or production system was compromised.
How This Project Relates to SOC Analyst Responsibilities

This project strengthened my understanding of security events involving credentials, authentication, file transfers, encryption, and sensitive information.
These concepts are relevant to SOC analysts because suspicious file access and credential misuse can form part of a larger security incident.
For example, if a security team suspects that confidential files have been accessed without authorization, an analyst may follow this workflow.

Step 1: Identify the alert or report
Determine whether the investigation began with a user report, an endpoint alert, a suspicious authentication event, or an unusual file transfer.

Step 2: Establish the scope
Identify the potentially affected accounts, systems, files, and time period.

Step 3: Review authentication evidence
Examine available logs for successful and failed logins, unfamiliar source addresses, unusual login times, and unexpected account activity.

Step 4: Investigate file-transfer activity
Review available FTP, SFTP, FTPS, firewall, endpoint, or network logs to determine which files were transferred, which accounts were involved, and whether the activity was authorized.
The visibility available depends on the protocols, logging configuration, and monitoring tools deployed.

Step 5: Assess credential and key exposure
Determine whether credentials or decryption keys may have been exposed and whether additional systems or files could be affected.
Step 6: Assess the potential impact
Identify the type of information involved and determine whether sensitive personal or financial data may have been accessed.

Step 7: Escalate and recommend containment
Follow the organization’s incident-response procedures. Depending on the evidence and authorization, actions may include disabling compromised credentials, restricting access, isolating affected systems, and securing exposed keys.

Step 8: Document findings
Record the timeline, affected assets, available evidence, actions taken, unresolved questions, and recommendations.
Important: This workflow describes how the concepts from the lab can be applied to a SOC investigation. I did not perform a separate investigation of production FTP logs as part of this project.

Challenges and Lessons Learned
Challenge 1: Working with encrypted file contents
The encrypted credential files had to be copied into the lab VM before I could decrypt them.
Lesson learned: File preparation and correct input handling are important parts of a successful cryptographic operation.

Challenge 2: Understanding OpenSSL options
The decryption command combined the AES-256-CBC algorithm, PBKDF2, Base64 processing, and input and output file options.
Lesson learned: Understanding each command-line option makes it easier to use cryptographic tools correctly and troubleshoot failures.

Challenge 3: Working with FTP authentication and file transfer
I used two sets of FTP credentials to authenticate to the server and perform file transfers.
Lesson learned: Valid credentials can provide access to remote resources, which makes credential protection, account permissions, and authentication monitoring important security controls.

Challenge 4: Protecting confidential information
The final decrypted file contained personal and financial information.

Lesson learned: Successfully decrypting a file demonstrates access to its contents, but it also highlights the responsibility to protect the information and avoid unnecessary disclosure.
Recommendations
Based on the concepts demonstrated in this lab, I would recommend the following security controls for an organization handling confidential client information: 1. Use secure file-transfer protocols: Prefer SFTP or properly configured FTPS over traditional FTP. 2. Protect credentials: Store passwords securely and avoid exposing credentials in plaintext files. 3. Secure encryption keys: Restrict key access, use secure key-distribution methods, and rotate or revoke keys when necessary. 4. Apply least privilege: Ensure that users can access only the files and services needed for their responsibilities. 5. Use strong cryptographic practices: Choose appropriate algorithms, secure modes, and well-configured key-derivation parameters. 6. Protect sensitive data: Encrypt confidential information at rest and in transit where appropriate. 7. Monitor authentication and file transfers: Investigate unusual login patterns, unexpected downloads, and suspicious account activity. 8. Maintain audit records: Preserve relevant logs to support investigations and incident response. 9. Follow data-handling policies: Avoid unnecessary copies of sensitive information and securely remove temporary files when appropriate. 10. Practice incident response: Establish procedures for responding to compromised credentials, exposed keys, and suspected data breaches.

Key Takeaways
By completing this project, I gained practical experience with: • Decrypting credential files using OpenSSL. • Understanding AES-256-CBC encryption and PBKDF2. • Recognizing the difference between encryption and Base64 encoding. • Authenticating to an FTP server using recovered credentials. • Uploading and downloading files using FTP commands. • Verifying files using Linux terminal commands. • Decrypting an encrypted file into a readable text file. • Recognizing the security implications of exposing encryption keys. • Understanding the confidentiality risks associated with insecure file transfers. • Identifying safeguards for protecting personal and financial information. • Connecting encryption and authentication concepts to SOC investigation workflows.
This project strengthened my cybersecurity foundation by demonstrating how cryptographic tools, account credentials, file-transfer protocols, and access controls affect the confidentiality of sensitive information.

Conclusion
The Cisco Cybersecurity Essentials Explore File and Data Encryption lab provided practical experience with encryption, decryption, authentication, and file transfer.
I successfully recovered Mary’s and Bob’s FTP credentials from encrypted files, used those credentials to authenticate to an FTP server, uploaded and downloaded an encrypted client information file, and used a decryption key obtained from email to recover the file’s contents.
The final decryption revealed names, addresses, and credit information, demonstrating the importance of protecting sensitive data and controlling access to encryption keys.
The most important lesson I learned is that encryption alone is not enough. Effective data protection also requires secure key management, protected credentials, secure transfer protocols, appropriate access controls, and monitoring.
These concepts are relevant to SOC operations because security analysts need to understand how credentials are misused, how sensitive information can become exposed, and what evidence can help establish the scope and potential impact of a security incident.


Skills practised: OpenSSL, AES-256-CBC, PBKDF2, FTP authentication, file transfer, Linux terminal commands, data confidentiality, encryption key management, and security risk assessment. :::
