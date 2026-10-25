Project Overview
In this project, I used the Cisco Cybersecurity Essentials lab to learn how to verify file integrity using cryptographic hashing and HMAC (Hash-based Message Authentication Code).
The lab involved working with files from different systems, obtaining files from a headquarters environment, calculating file hashes, and verifying the integrity of a sensitive file called income.txt.
I also learned how security analysts can use file integrity verification to identify possible unauthorized modifications and establish whether a file matches a trusted reference.
This project helped me understand the importance of protecting sensitive information and verifying data before trusting it during a security investigation.

Project Objectives
The main objectives of this project were to: • Understand file integrity and why it matters in cybersecurity. • Obtain files from a trusted source. • Use MD5 hashing to check file integrity. • Generate an HMAC using OpenSSL. • Compare an HMAC against a trusted reference value. • Understand how hashing and HMAC can help identify file modifications. • Learn how file integrity verification supports security investigations and incident response.

Lab Environment and Tools
I used the Cisco Cybersecurity Essentials lab environment.
The tools and systems involved included: • CSE Lab VM: Used to perform hashing and HMAC verification. • Laptop BR1: Used to access file contents during the MD5 verification exercise. • Laptop BR2: Bob’s laptop at headquarters, used to access the original income.txt file and its reference HMAC. • FTP: Used to download the client files from the headquarters environment. • Command Prompt: Used to check for files on the lab computers. • Text Editor: Used to open files and copy their contents. • Pluma: Used to create the local income.txt file on the CSE lab VM. • Linux terminal: Used to calculate file hashes and generate an HMAC. • OpenSSL: Used to generate the HMAC-SHA-256 value.

Understanding File Integrity
File integrity means that a file’s contents have not changed unexpectedly or without authorization.
For example, imagine an organization stores customer information in a file. If an attacker modifies the file, the organization needs a way to determine whether the contents have changed.
Cryptographic hashing provides one method of checking file integrity.
A hash function processes data and produces a fixed-length digest. When the contents change, the resulting digest will generally change as well.

During this project, I learned about two related techniques:
MD5 hashing: A hashing algorithm that produces a 128-bit digest. It can help demonstrate file-integrity checking in a controlled lab, but MD5 is considered cryptographically broken and should not be relied upon for security-sensitive integrity verification.
HMAC-SHA-256: A message authentication technique that combines a cryptographic hash function with a secret key. It can help verify data integrity and authenticity when the key and reference value are trusted.

Practical Implementation
Part 1: Obtaining the Client Files
I started by accessing the headquarters environment to obtain the client files required for the integrity-checking exercise.
The lab provided FTP access using the following credentials: • Username: Mike • Password: cisco123

I used the FTP environment to download the required files to the CSE lab VM.
The files were: 1. NEclients.txt 2. NWclients.txt 3. Nclients.txt 4. SEclients.txt 5. SWclients.txt 6. Sclients.txt
After downloading the files, I used the Linux terminal to check the directory contents.

Command used:
dir
The dir command lists directory contents in Linux environments where it is available. The equivalent commonly used Linux command is:
ls
I used the directory listing to check that the downloaded files were present before proceeding with the integrity-verification exercise.

Security relevance:
Obtaining the correct files is an important part of the process. An analyst needs to know which files are being examined and where they came from before attempting to verify their integrity.

Part 2: Calculating MD5 Hashes for File Integrity Checking
Next, I began the MD5 hashing exercise.
I returned to Laptop BR1 and opened the Text Editor.
I followed these steps: 1. Opened Text Editor. 2. Selected File → Open. 3. Located NEclients.txt. 4. Opened the file and copied its contents. 5. Switched to the CSE lab VM. 6. Used the terminal to calculate a hash of the copied contents.
The command format used in the exercise was:
echo -n 'NEclients.txt contents' | md5sum

Breaking down the command: • echo prints the supplied text. • -n prevents echo from adding a newline character at the end. • 'NEclients.txt contents' represents the file contents copied into the command. In the actual exercise, this placeholder must be replaced with the real file contents. • | sends the output of the first command to the next command. • md5sum calculates the MD5 digest of the supplied data.
Important technical note: This command hashes the literal text entered between the quotation marks. It does not automatically read the contents of NEclients.txt.
If a file is saved locally and its contents need to be hashed directly, the more appropriate command is:
md5sum NEclients.txt
This reads the file and calculates its MD5 digest.
For the lab’s copy-and-compare procedure, the hash calculated from the copied contents needs to be compared with the corresponding reference hash.
I intended to repeat the integrity-checking process for the other client files.
Files involved: • NEclients.txt • NWclients.txt • Nclients.txt • SEclients.txt • SWclients.txt • Sclients.txt

Security relevance:
If the calculated hash differs from a trusted reference hash, the analyst should investigate the discrepancy. Possible explanations include modified file contents, copying errors, incorrect reference values, or differences in how the hash was calculated.
A matching MD5 digest can help identify accidental changes in a controlled exercise, but MD5 is vulnerable to collision attacks. For real security investigations, I would prefer SHA-256 or another appropriate modern cryptographic method.
Evidence note: The exact MD5 values and individual comparison results were not recorded in my project notes. I therefore do not claim that every client file was successfully verified.

Part 3: Locating the Original income.txt File
The final objective of the lab focused on verifying file integrity using HMAC.
I started by accessing Bob’s laptop at headquarters, identified as Laptop BR2.
I opened Command Prompt and entered:
dir
This allowed me to inspect the current directory and check whether income.txt was present.
Once I located the file, I opened Text Editor and followed these steps: 1. Selected File → Open. 2. Located income.txt. 3. Opened the file. 4. Copied its contents.
The purpose of this step was to obtain the reference file contents for the HMAC-verification exercise.

Part 4: Creating income.txt on the CSE Lab VM
After copying the original file contents from Bob’s laptop, I switched to the CSE lab VM.
I opened Pluma, pasted the copied contents, and saved the file as:
income.txt
This created the local copy that I would use for HMAC generation.
At this stage, I had a local copy of the file and the reference HMAC value obtained from Bob’s laptop

Security relevance:
When transferring files between systems, an analyst should consider whether the transfer method is secure and whether the reference data can be trusted.
Copying file contents manually is sufficient for demonstrating the concept in this lab, but it is not ideal for transferring sensitive data in a production environment because manual copying can introduce errors.

Part 5: Generating an HMAC-SHA-256 Value
I opened the terminal on the CSE lab VM and executed the following command:
openssl dgst -sha256 -hmac cisco123 income.txt
This command generated an HMAC-SHA-256 value for the local income.txt file.
Explanation of the command
openssl
Runs the OpenSSL cryptographic utility.
dgst
Invokes the digest functionality used to calculate a digest or keyed authentication value.
-sha256
Specifies SHA-256 as the underlying hash function.
-hmac cisco123
Specifies HMAC mode and uses cisco123 as the secret key.
income.txt
Specifies the file whose contents are used to calculate the HMAC.
The command processes the file contents and produces an HMAC value.
The HMAC depends on both the file contents and the secret key. If the file contents change, the HMAC will generally change as well.
The secret key must be kept confidential in a real environment.
Security note: The key cisco123 was supplied by the lab exercise. It is a demonstration key, not a suitable production secret. A real organization should use a strong, securely generated key and protect it from unauthorized access.

Part 6: Comparing the Generated HMAC with the Reference HMAC
After generating the HMAC on the CSE lab VM, I compared it with the HMAC value I had copied from Bob’s laptop at headquarters.
Result: The two HMAC values matched.

This was the key result of the final objective.
The matching values indicated that the local income.txt contents were consistent with the reference used in the lab, assuming both HMAC values were generated with the same secret key and algorithm and that the reference value was trustworthy.
If the HMAC values had differed, I would have investigated possible causes, including: • Changes to the file contents. • Errors introduced while copying the file. • Use of an incorrect secret key. • Comparison against the wrong reference value. • Differences in the files being compared.
What Is the Difference Between Hashing and HMAC?
This project helped me understand the difference between an ordinary hash and a keyed authentication code.
Feature MD5 Hash HMAC-SHA-256 Uses a secret key No Yes Main purpose in this lab Demonstrate file-integrity checking Verify integrity and authenticity Detects changes to file contents Can help, when compared with a trusted reference Yes, when compared with a trusted reference and the correct key Provides encryption No No Suitable for modern security-sensitive verification No Yes, when implemented with appropriate key management
An ordinary hash does not prove who created the hash or whether the reference hash itself can be trusted.
HMAC adds authentication based on a secret key. However, anyone who obtains the secret key may be able to generate a valid HMAC for modified content.
Neither hashing nor HMAC encrypts the file contents.

Findings and Results
The following table summarizes the results I recorded during the project.
Activity Result Downloaded client files from headquarters Completed Checked for downloaded files Completed Began the MD5 integrity-checking exercise Completed Recorded all six client-file MD5 values Not recorded Located income.txt on Laptop BR2 Completed Copied the file contents to the CSE lab VM Completed Generated an HMAC-SHA-256 value using OpenSSL Completed Compared the generated HMAC with the reference HMAC Successful match Completed the final lab objective Yes
The most important confirmed result was that the HMAC generated for my local income.txt file matched the reference HMAC from Bob’s laptop.
This demonstrated the practical use of a keyed authentication value for checking file integrity.
How This Applies to SOC Analyst Work
File-integrity verification is relevant to security operations because analysts sometimes need to establish whether files have been modified unexpectedly.
For example, a SOC Analyst may investigate: • Suspicious changes to sensitive documents. • Unexpected modifications to configuration files. • Possible tampering with collected evidence. • Files that may have been altered following a security incident. • Differences between a potentially compromised file and a trusted copy.
During an investigation, I would follow a structured workflow.

Step 1: Identify the affected file
Record the filename, location, system, and reason the file is being investigated.

Step 2: Preserve the evidence
Avoid modifying the original file. Where appropriate, collect a forensic copy and document how it was obtained.

Step 3: Establish a trusted reference
Obtain a known-good copy or trusted reference digest from an appropriate source.
For sensitive verification, I would use a modern cryptographic hash such as SHA-256.

Step 4: Calculate the file hash
For example:
sha256sum income.txt
This calculates the SHA-256 hash of the local file.

Step 5: Compare the results
Compare the calculated hash against the trusted reference hash.
A mismatch indicates that the files differ or that another aspect of the comparison needs investigation. A match supports the conclusion that the contents are identical, subject to the trustworthiness of the reference and the properties of the algorithm.

Step 6: Use HMAC when appropriate
If the organization uses HMAC to authenticate files, calculate the HMAC using the approved secret key and compare it with the trusted reference value.
The key should be obtained through an authorized process rather than embedded in scripts or exposed in terminal history.

Step 7: Investigate discrepancies
If the values differ, investigate the source of the discrepancy rather than immediately assuming malicious activity.
Possible causes include legitimate file updates, transfer errors, incorrect reference data, unauthorized modifications, or key-management problems.

Step 8: Document the findings
Record the evidence, commands used, reference values, calculated values, timestamps, and conclusions.
Escalate suspicious findings according to the organization’s incident response procedures.

Lessons Learned
From this project, I learned that: 1. File integrity is an important part of protecting organizational information. 2. Hashing can help identify differences between files. 3. MD5 is useful for understanding the concept of hashing but is not appropriate for security-sensitive integrity verification. 4. HMAC uses a secret key to provide integrity and authentication. 5. A matching HMAC is meaningful only when the reference value, algorithm, and key are trustworthy. 6. Secret keys must be protected because their exposure can undermine HMAC-based authentication. 7. File integrity verification can support incident response, evidence validation, and recovery. 8. Security findings should be documented accurately without claiming results that have not been verified.

Conclusion
This project gave me practical experience with file integrity verification using MD5 hashing and HMAC-SHA-256.
I downloaded client files from the headquarters environment, worked with copied file contents, and learned how to calculate and compare cryptographic values.
The most important outcome was successfully generating an HMAC for income.txt on the CSE lab VM and confirming that it matched the reference HMAC obtained from Bob’s laptop at headquarters.
This exercise improved my understanding of how cryptographic integrity checks can help security teams determine whether data is consistent with a trusted reference.
As I continue developing my cybersecurity skills, I intend to apply these concepts to security monitoring, incident investigation, evidence handling, and file-integrity analysis.

Tools used: Cisco Cybersecurity Essentials Lab, Linux Terminal, OpenSSL, MD5 hashing, HMAC-SHA-256, FTP, Text Editor, and Pluma
