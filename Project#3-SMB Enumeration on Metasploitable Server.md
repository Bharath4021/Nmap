# Project 3: SMB Enumeration on Metasploitable Server
## Introduction 
In this project, I explored how to enumerate SMB (Server Message Block) services running on a Metasploitable 2 machine. SMB is a network file sharing protocol that allows systems to share files, printers, and other resources. It typically runs on port 445, and if not secured properly, it can expose sensitive data, allow unauthorized access, and reveal usernames.The goal was to simulate a real-world internal audit scenario where a legacy server might be exposing file shares, allowing guest access, or leaking user information. These are critical findings that can easily be missed in older environments.

## Objective
- Discover SMB shares exposed on the Metasploitable machine
- Check if guest or anonymous access is allowed
- Enumerate SMB users without credentials
- Identify the OS and version of the SMB service
- Analyze security configurations and risks

## Lab Setup
- **Attacker Machine:** Kali Linux
- **Target Machine:** Metasploitable 2 (192.168.1.74)
- **Virtualization:** VirtualBox (Host-Only Adapter)
- **Tool Used:** Nmap 

## Nmap Scans
### SMB OS Discovery
**nmap -p 445 --script smb-os-discovery 192.168.1.74*
### SMB OS Discovery Scan results and Output Summary:


- While scanning the target machine (Metasploitable 2) using the smb-os-discovery script, I was able to extract detailed information about the SMB service and the underlying operating system. The scan identified the system as running a Unix-based OS with Samba version 3.0.20-Debian. Additionally, it revealed NetBIOS details such as the computer name ("metasploitable"), the domain/workgroup ("localdomain"), and the fully qualified domain name (metasploitable.localdomain). The system time was also disclosed as 2025-04-15T13:43:11-04:00. This type of information is highly valuable during a penetration test, as it allows analysts or attackers to fingerprint the target system and align their exploitation efforts with known vulnerabilities associated with the specific OS and Samba version. The exposure of OS details and NetBIOS metadata through SMB contributes to the overall attack surface and reinforces the need to properly harden legacy services in production environments.

### SMB Security Mode Discovery
**nmap -p 445 --script smb-security-mode 192.168.1.74*

### SMB Security Mode Discovery Scan results and Output Summary:


- During the SMB security audit on the Metasploitable 2 server, the scan indicated that the "Guest" account was used for authentication, which often suggests that unauthenticated or low-privilege access is allowed. The authentication level was set to "User", meaning that access control is managed per individual account rather than per share. Additionally, the SMB server supports Challenge/Response authentication, a mechanism used to securely verify user credentials. However, a major concern observed was that message signing is disabled, which makes the system vulnerable to man-in-the-middle (MITM) attacks. This is a known risky default in older systems like Samba 3.x and should be addressed to prevent tampering with SMB communication.

### SMB User Enumeration
**nmap -p 445 --script smb-enum-users 192.168.1.74*

### SMB User Enumeration Scan results and Output Summary:


- The SMB user enumeration scan revealed a total of 33 user accounts configured on the Metasploitable 2 server. Among these, some notable accounts include "msfadmin", which appears to be an active, standard user account commonly used for testing purposes, and "user", described as "just a user," also active and not disabled. The remaining accounts consist mostly of system-related or service accounts such as daemon, ftp, and postgres, many of which are either disabled or have restricted access. This level of enumeration highlights the server's exposure, especially in legacy environments, and underscores the need to limit account visibility and tighten access controls to reduce attack surface.

### SMB Share Enumeration


- The SMB share enumeration scan against the Metasploitable 2 server uncovered several available shares, including ADMIN$, IPC$, opt, print$, and tmp. Most shares, such as ADMIN$, opt, and print$, restrict anonymous access, which aligns with standard security practices. However, both the IPC$ and tmp shares were found to allow anonymous READ/WRITE access, posing a significant security risk. The tmp share in particular, with the description "oh noes!", suggests it may have been intentionally left open, potentially for testing purposes. These open shares expose the system to threats such as unauthorized data access, malware uploads, and lateral movement within the network. Hardening these shares by disabling guest access and applying strict permissions is essential to mitigate these risks.


## Security Observations & Recommendations
From the findings during the SMB enumeration on the Metasploitable server, several security concerns were identified, particularly around guest access and open shares. To improve the server’s security posture, guest access should be disabled unless it is absolutely necessary, as it poses a risk of unauthorized access. Sensitive shares, such as ADMIN$ and tmp, should be restricted using Access Control Lists (ACLs) or by assigning permissions based on trusted user groups. Additionally, the SMB configuration should be hardened to prevent anonymous enumeration of user accounts, which can leak valuable information to potential attackers. It’s also critical to perform regular audits and remove unused or default shares, especially on legacy systems like Metasploitable, to reduce the overall attack surface and maintain a secure environment.


## Conclusion 
This project revealed critical SMB misconfigurations on Metasploitable 2, including exposed user accounts, writable anonymous shares, and disabled message signing. Such issues pose serious security risks like unauthorized access and malware upload. The exercise emphasized the importance of hardening SMB services, restricting guest access, and regularly auditing legacy systems to reduce the attack surface and prevent internal threats.


