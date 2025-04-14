# Project 2: Web Server Recon + Enumeration
## Introduction
This project focuses on identifying exposed web directories, supported HTTP methods, and page titles of a web application using Nmap NSE (Nmap Scripting Engine) scripts. These scripts reveal critical insights that can help in understanding the attack surface and identifying possible misconfigurations or vulnerabilities.

## Objective
- Enumerate directories and files using http-enum
- Detect enabled HTTP methods via http-methods
- Capture web page titles using http-title
- Analyze and interpret findings from a security perspective

## Lab Setup
For this lab, we used Kali Linux as the attacker machine, equipped with Nmap. The target system is Metasploitable 2, a vulnerable virtual machine commonly used for penetration testing practice. Both virtual machines were configured in VirtualBox, using either the Host-Only Adapter or NAT network mode to ensure they are on the same network and can communicate securely without exposing the lab to the internet. This isolated environment allows for safe, controlled experimentation with web enumeration and vulnerability discovery tools.

## Nmap Scans
- **http-enum Output*
nmap --script http-enum 192.168.1.74

- **http-methods Output*
nmap --script http-methods 192.168.1.74


## Analysis & Security Observations

### During analysis, we reviewed the discovered paths and evaluated their security implications:

- The /phpinfo.php page poses a high risk, as it reveals sensitive server-side environment variables, PHP version details, and paths. This information can help attackers craft specific exploits or find misconfigurations.Similarly, the /phpMyAdmin/ interface is another high-risk element. It's a well-known administrative interface for MySQL that’s often targeted. If not protected by authentication and proper access restrictions, it becomes a major vulnerability.The /doc/ directory indicates that Apache documentation is available, and more importantly, it reveals that WebDAV is enabled, which can allow file uploads or manipulation if not properly secured. This makes it a medium-risk path. Directories like /test/ and /index/ might seem harmless, but they indicate leftover development artifacts or poor operational hygiene, marking them as low-risk but worth reviewing.

### HTTP Methods Observation

- From the scan results, the HTTP server only accepts standard methods like GET, HEAD, POST, and OPTIONS, which is considered safe. There was no indication of potentially dangerous methods such as PUT, DELETE, or TRACE, which are commonly abused by attackers to upload or delete content remotely.


## Recommendations

### The following steps are recommended based on the vulnerabilities discovered:

- The phpinfo.php file should be completely removed from production servers, as it exposes critical server configuration details that could be leveraged in an attack.
- Access to the phpMyAdmin interface should be restricted through firewall rules or VPN access, and strong authentication mechanisms should be enforced to prevent unauthorized access.
- If WebDAV is not explicitly needed, it should be disabled. If it is required, it must be secured with proper authentication and authorization controls to prevent unauthorized uploads or access.
- Lastly, for directories like /icons/, /test/, and /index/, directory listings should be disabled. This can be done by modifying the Apache configuration to remove Indexes, or by placing appropriate .htaccess rules to prevent public access.

## Conclusion 
Through the use of http-enum and http-methods, we successfully enumerated critical web directories and validated HTTP methods on the target server. This process helps identify potential entry points for further investigation or exploitation. The findings underline the importance of secure configurations and regular cleanup of leftover development artifacts. This simple yet effective reconnaissance method using Nmap gives security analysts and penetration testers a solid start in identifying surface-level misconfigurations in web environments.
