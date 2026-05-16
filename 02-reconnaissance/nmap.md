Nmap is a popular scanning utility that is available for download from the Internet at no cost. It is a powerful tool that includes many functions. The Nmap utility can quickly and easily gather information about a network's hosts, including their availability, their IP addresses, and their names. This is useful information not only for a network administrator but for a hacker as well, prior to an attack. One of the first tasks a hacker will carry out is to perform a scan of the network for hosts that are running. Once the user knows what hosts are accessible, the user will then find a means to gather as much information about the hosts as possible.

1. nmap -sn 
![](../images/Pasted%20image%2020251126174014.png)

**`-sn` is a host discovery (ping) scan**. It tells Nmap to **only check which machines are online** without scanning any ports. It works like a normal ping, but is more advanced because Nmap can use ARP requests, ICMP pings, or even TCP probes to detect if a device is up, even when regular ping is blocked.

On a **LAN**, Nmap doesn't use ICMP like a normal ping.  
Instead, it uses **ARP requests** to discover hosts because ARP always works on local networks.

- **ARP = "Who has this IP? Tell me your MAC."**
    
- ARP requests are **broadcast** to everyone on the network (ff:ff:ff:ff:ff:ff).
    
- Any device with that IP replies with its MAC → Nmap marks it as **online**.
    

So during a ping scan (`-sn`), you see many ARP broadcasts in Wireshark because Nmap is checking every IP one by one.

On a **WAN**, ARP cannot be used, so Nmap switches to **ICMP, SYN, or ACK pings** instead.

**DNS part (why I still see DNS packets):**  

After Nmap figures out that an IP is **up**, by default it also tries to **resolve the IP to a hostname** using reverse DNS lookups. That's why in Wireshark you see **DNS queries** even though you only ran `nmap -sn` – Nmap is asking the DNS server: "Which hostname belongs to this IP?". This is just for nicer output and doesn't affect the actual host discovery. If we don't want this DNS stuff, we can add `-n` (no DNS resolution), e.g. `nmap -sn -n <target>`.

2. nmap -sT 
![](../images/Pasted%20image%2020251126180459.png)

This Nmap `-sT` scan shows all the open TCP ports on the target machine by performing a full TCP handshake, revealing the services running on each port (like FTP, SSH, Telnet, HTTP, MySQL, etc.).

![](../images/Pasted%20image%2020251126180731.png)

These packets show Nmap's full TCP connect scan (`-sT`), where your machine sends SYN packets to each port and the target replies with RST/ACK for closed ports (highlighted in red), meaning those ports are not open.


![](../images/Pasted%20image%2020251126181439.png)

on the targeted server metasploitable port 80 is opened because it launching from apache.
### These ports use **TCP**:

- **21** – FTP
    
- **22** – SSH
    
- **23** – Telnet
    
- **25** – SMTP
    
- **80** – HTTP
    
- **110** – POP3
    
- **139** – NetBIOS Session
    
- **445** – SMB
    
- **3306** – MySQL

UDP services:

- **53** DNS (UDP)
    
- **67/68** DHCP
    
- **69** TFTP
    
- **161** SNMP

4. nmap -sS 
![](../images/Pasted%20image%2020251204140652.png)

![](../images/Pasted%20image%2020251126181736.png)

A SYN stealth scan doesn't complete the TCP handshake, so it only sends SYN, receives SYN/ACK, and then sends RST, which allows Nmap to confirm a port is open without creating a full connection; this makes it much harder for older or poorly configured IDS/IPS systems to detect because many of them only alert on full, established connections, meaning that the scan often blends in as normal dropped or abandoned traffic, although a properly configured modern IDS can still detect it.
 that's why we have only 3 hanshake for http port 80
 
 ![](../images/Pasted%20image%2020251204173720.png)
 
 -p for port selection
 
 ![](../images/Pasted%20image%2020251126182806.png)
 
5. nmap -O
   
![](../images/Pasted%20image%2020251204174052.png)

Detection OS of the targeted machine. Nmap couldn't accurately detect the OS because all 1000 ports were filtered by a firewall, so it didn't receive enough responses to fingerprint the system and gave a broad, unreliable guess instead.


You are **manually connecting to the web server (HTTP service) on port 80** and sending a basic HTTP command (`get`).  
The server responds by sending you the **raw HTML of the webpage**, which reveals:

- The server is running
    
- Port 80 is open
    
- What webpage/content is hosted
    
- The banner information (Metasploitable2 warning message, links, etc.)
    

This technique is used in security to **identify services and gather information without using a browser**.

![](../images/Pasted%20image%2020251126190815.png)
