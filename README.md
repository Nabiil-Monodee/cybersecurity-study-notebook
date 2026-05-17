# Cybersecurity Study Notebook

Personal study notes and lab walkthroughs I wrote while preparing for my BSc Cybersecurity practical hacking exam at Middlesex University. These document offensive security techniques across network reconnaissance, web exploitation, MITM attacks, password cracking, wireless attacks, and the Metasploit framework — written in my own style with troubleshooting steps and deeper explanations where I went beyond the original lab scope.

## Labs

### 01 — Network Fundamentals
- [Network Workstation Configuration](01-network-fundamentals/network-workstation-configuration.md) — TCP/IP, ARP, DNS, IPv6, hosts file, ping/netstat commands on Windows and Linux

### 02 — Reconnaissance
- [Nmap](02-reconnaissance/nmap.md) — host discovery, TCP connect scan, SYN stealth scan, OS detection, Wireshark analysis

### 03 — Network Attacks
- [Ettercap — Man-in-the-Middle](03-network-attacks/ettercap-mitm.md) — ARP poisoning, DNS spoofing, FTP credential capture

### 04 — Web Attacks
- [SQL Injection with sqlmap](04-web-attacks/sql-injection.md) — full attack chain from injection point → database enumeration → data dump → hash cracking

### 05 — Password Attacks
- [John the Ripper](05-password-attacks/john-the-ripper.md) — dictionary attack, hybrid attack with rules, default cracking mode

### 06 — Protocol Analysis
- [FTP, HTTP, SMTP, POP3](06-protocol-analysis/ftp-http-smtp-pop3.md) — manual interaction with cleartext protocols via Telnet, security implications

### 07 — Exploitation Framework
- [Metasploit](07-exploitation-framework/metasploit.md) — vsftpd 2.3.4 backdoor, UnrealIRCd backdoor, Samba usermap_script, Windows 7 java_rhino browser exploit

### 08 — Wireless Attacks
- [WiFi WPA Handshake Cracking](08-wireless-attacks/wifi-wpa-cracking.md) — monitor mode, airodump-ng capture, deauth injection with aireplay-ng, offline cracking with aircrack-ng

## Tools used

**Reconnaissance:** Nmap · Wireshark
**Network attacks:** Ettercap · Aircrack-ng suite (airodump, aireplay, aircrack)
**Web attacks:** sqlmap
**Password attacks:** John the Ripper
**Exploitation:** Metasploit Framework
**Targets used in labs:** Metasploitable 2 · FreeBSD · Windows 7 · vulnweb.com (Acunetix test site)
**OS:** Kali Linux

## About me

Final-year BSc Cybersecurity & Digital Forensics student at Middlesex University, based in Mauritius. Currently completing my Cisco CCNA through IT Academia. Building portfolio pieces in SOC operations (Wazuh SIEM), network engineering, and offensive security.

