In this lab we will configure network settings for both Linux and windows Operating System.

Note:
1. For two computers to communicate in a TCP/IPv4 network, both computers must have a unique Internet Protocol IP address. An IP address has four octets. The IP address is divided into a network address and a host address. The subnet mask identifies which portion of the IP address is the network address and which portion is the host address. On a local area network (LAN), each computer must have the same network address and a different host address. To communicate outside the LAN, using different network IP addresses. a default gateway is required.

2. To connect to a TCP/IP network, normally four items are configured: the IP address (this is both the network portion and the host portion). the subnet mask, the IP address for a Domain Name System (DNS) server, and the IP address for the gateway machine. To communicate within a LAN only, you need the IP address and subnet mask. To communicate with other networks, you need the default gateway. If you want to be able to connect to different sites and networks using their domain names, then you need to have the address of a DNS server as well.

**Windows**
To view network card configuration(to view your IP address and others) use command ipconfig
Change IP address from GUI network and sharing on control panel , click on Local Area Connection and IPV4.

![](../images/Pasted%20image%2020251123134437.png)
 
Arp -a command 

![](../images/Pasted%20image%2020251123135216.png)

- **dynamic** = auto-learned via ARP, temporary 
- **static** = manually or system-set, more "permanent" mappings

a.   How does using the ping utility cause the machine's MAC address to be populated in the ARP cache?

When you ping an IP on your LAN, you PC has to find the destination MAC address of that targeted PC before it can send the ping.
if the computer doesn't already know the MAC Address, it uses ARP to learn it and then stores it in the ARP cache.

AI example:

![](../images/Pasted%20image%2020251123140437.png)

- **IP layer decides where to send it**  
    Your PC sees that `192.168.206.2` is in the **same subnet** as itself, so it should send packets **directly** to that host (not to the router).
    
- **It needs a MAC address**  
    To actually put the ping (ICMP Echo Request) on the Ethernet/Wi-Fi, it must build a **frame** with:
    
    - Source MAC = your NIC's MAC
        
    - Destination MAC = MAC of `192.168.206.2`
        
- **Checks ARP cache**  
    It looks in the ARP table:
    
    - If `192.168.206.2` is **already there**, great → use that MAC.
        
    - If it's **not there**, it must ask.
        
- **Sends an ARP request (broadcast)**  
    Your PC sends a Layer 2 broadcast frame:
    
    > "Who has IP 192.168.206.2? Tell 192.168.206.134"
    
    This goes to **all devices** in the LAN.
    
- **Target replies with ARP reply (unicast)**  
    The host with IP `192.168.206.2` answers:
    
    > "I am 192.168.206.2 and my MAC is 00-50-56-fb-55-c2"
    
- **Your PC stores this in the ARP cache**

![](../images/Pasted%20image%2020251123140509.png)

 How can you be sure that this is actually the correct MAC address for the computer?
 
You can manually check the correct MAC address on the target machine and compare. Ipconfig command is used to see both IP address and MAC address.


Linux
To view the network card configuration use ifconfig or ip addr in modern OS

To view DNS address :
cat /etc/resolv.conf

![](../images/Pasted%20image%2020251123141653.png)

AI Explantaion:
- **`nameserver 192.168.206.2`**  
    This is the **DNS server** your machine will ask whenever you type a **name** like `google.com`, `kali`, `server1`, etc.  
    Without this, your PC would only understand **IP addresses** and wouldn't know where to send `google.com`.
    
- **`search localdomain`**  
    This means: if you type a short hostname like `server1`, the system will try `server1.localdomain`.
    In hacking and ethical hacking, `/etc/resolv.conf` matters because it controls **how the machine resolves domain names to IP addresses** by telling it which **DNS server** to use. If an attacker can change or abuse DNS, they can redirect traffic to fake sites (phishing), hide command-and-control servers, or break tools that rely on names like `target.com` instead of raw IPs. Pentesters also care about it because using an internal DNS server can reveal **hidden hosts and services** (e.g. `db01.localdomain`, `intranet`, `backup-server`) during recon, and some techniques like DNS spoofing or DNS tunneling depend on understanding exactly where the system sends DNS queries. So, knowing `/etc/resolv.conf` = knowing **who your machine trusts to answer "who is this domain?"**, which is a big deal in security.

To know your machine gateway address use command **netstat -nr** 

![](../images/Pasted%20image%2020251123142520.png)

To change IP address on Linux 
 sudo ifconfig (interface) (ip adddress)
 **sudo  ifconfig eth0 192.168.206.130**

![](../images/Pasted%20image%2020251123142804.png)

To view arp table
arp-an (show all)

Delete arp cache
sudo arp -d (your host ip adddress)
- `ping -i 0.2 127.0.0.1`  
    Send a ping every **0.2 seconds** (shorter interval between packets).
    
- `ping localhost`
    
- `ping 127.0.0.1`  
    Both ping your **own machine** using the loopback interface (name vs IP).
    
- `ping -c 5 google.com`  
    Send **exactly 5** echo requests to `google.com` then stop. (`-c` = count)
    
- `ping -V`  
    Show the **version** of the ping program (works in Linux, not in Windows).
    
- `ping -f localhost`  
    **Flood ping** – send packets as fast as possible (needs `sudo`). Good for stress-testing, can be noisy.

    ![](../images/Pasted%20image%2020251123145230.png)
    
- `ping -a IP`  
    On Linux: **audible ping** – makes a sound when replies are received. (On Windows, `-a` resolves addresses to hostnames.)
    
- `ping -c 1 google.com`  
    Send just **one** packet to test if the host is reachable.
    
- `ping -c 5 -q 127.0.0.1`  
    Send 5 pings but **quiet mode** – only show a final summary, not each reply.
    
- `ping -s 100 localhost`  
    Use a **100-byte payload** instead of the default size.
    
- `ping -w 5 localhost`  
    Set a **deadline of 5 seconds** – ping stops after 5 seconds total.
    
- `ping -w 100 localhost`  
    Same idea, but with a **100-second** overall timeout.
    
- `ping hop1 hop2 hop3 ... hopN destination`  
    Old **source-route** style usage (you specify intermediate hops).  
    This is often **disabled or unsupported** on modern systems, so it may not work.
    
- `ping -R 192.168.1.63`  
    Ask routers to **record the route** in the IP options (record route). Many routers/firewalls ignore or block this, so it may fail or show limited data.


### Windows-style ones

- **`netstat -a`**  
    Show **all connections and listening ports** (TCP + UDP).
    
- **`netstat -o`**  
    Show connections **with the PID** (Process ID) that owns each one.
    
- **`netstat -s`**  
    Show **per-protocol statistics** (TCP, UDP, ICMP etc.).
    
- **`netstat -r`**  
    Show the **routing table** (similar to `route print`).
    
- **`netstat -e`**  
    Show **Ethernet stats**: bytes sent/received, errors, etc.
    
- **`netstat -f`**  
    Show **FQDNs** (full DNS names) instead of just IPs where possible.
    
    
    

> These exist on Linux too in some form, but options may differ a bit.
     netstat -ano show all connections + numeric + PIDs for windows
---

### Mainly Linux-style ones

These use the same `netstat` binary but with Unix-style options:

- **`netstat -at`**  
    Show **all TCP** connections (listening + established).
    
- **`netstat -au`**  
    Show **all UDP** "connections" (really, sockets).
    
- **`netstat -l`**  
    Show only **listening** sockets (services waiting for connections).
    
- **`netstat -lt`**  
    Show **listening TCP** sockets only.
    
- **`netstat -lu`**  
    Show **listening UDP** sockets only.
    
- **`netstat -lx`**  
    Show **listening Unix domain** sockets (local IPC, not network).
    
- **`netstat -st`**  
    Show **TCP statistics** (counts of opens, closes, errors, etc.).
    
- **`netstat -su`**  
    Show **UDP statistics**.
    
- **`netstat -i`**  
    Show **network interfaces** and basic stats (packets, errors).
    
- **`netstat -tp`**  
    Show TCP connections **with the process** using each one (`-p` = PID/program name, needs sudo sometimes).
    
- **`netstat -lp`**  
    Show **listening** sockets **with process info**.
    
- **`netstat -g`**  
    Show **multicast group memberships** (which groups each interface has joined).

netstat -tuna
means:

-t → show TCP connections
-u → show UDP sockets
-n → show numeric IPs/ports (no DNS names)
-a → show all (listening + established)

So it's a nice "all connections" snapshot on Linux - very useful in real life.
But it does NOT show:
- routing table
- interface statistics
- protocol statistics


Lab 2b – Computer Name Resolution & IPv6 Basics (Windows)

![](../images/Pasted%20image%2020251204085315.png)

Modify host file :
1. open Notepad as administrator, right-click and run as administrator
2. Click on file at the top right 
3. naviagte to C:\Windows\System32\drivers\etc

![](../images/Pasted%20image%2020251204085958.png)

### Hosts File - What it Does & Why It's Powerful

**What you can do with the hosts file:**

- **Give custom names to machines**  
    Map IP --> name for your own PC, e.g.  
    `10.14.122.154 2k8serv` -->  you can `ping 2k8serv` instead of remembering the IP.
    
- **Override DNS for testing**  
    Temporarily point a domain to a different server _only on your PC_, e.g.  
    `203.0.113.10 www.mywebsite.com`  
    --> useful for testing new servers or staging environments.
    
- **Block domains (basic security/ad-blocking)**  
    Redirect bad/annoying domains to `127.0.0.1`, e.g.  
    `127.0.0.1 ads.badsite.com`  
    → connections to that hostname will fail.
    
- **Work in small / offline labs without DNS**  
    Use names instead of IPs even when no DNS server exists.
    

**Why it's powerful / dangerous:**

- The hosts file is checked **before DNS**, so it can **override real DNS answers**.
    
- That means it can:
    
    - Silently **redirect** `www.bank.com` to an attacker's IP.
        
    - **Block** antivirus update sites or security tools.
        
- Because of this, editing it needs **administrator rights**, and malware often tries to modify it.

### FQDN = Fully Qualified Domain Name

**Definition:**  
An **FQDN** is the _full_ name of a host on the internet or in a domain, including **all levels** of the domain, ending with the top-level domain.

### FQDN (Fully Qualified Domain Name)

- **FQDN = Fully Qualified Domain Name**
    
- It is the **complete** name of a host in a domain, including **all parts**:
    
    `hostname.subdomain.domain.tld`
    
    - `hostname` – specific machine (e.g. `server1`, `www`, `2k8serv`)
        
    - `subdomain` – optional part (e.g. `lab`, `cs`)
        
    - `domain` – main domain (e.g. `google`, `mylab`)
        
    - `tld` – top-level domain (e.g. `.com`, `.net`, `.co.uk`)
        
- **Examples of FQDNs:**
    
    - `www.google.com`
        
    - `server1.lab.local`
        
    - `mail.microsoft.com`
        
- **Not FQDN:**
    
    - `server1`, `2k8serv` (these are just **hostnames** / unqualified names).
        
- In the lab, "ping by the FQDN" means:
    
    - Use the **full name** of the machine instead of just its IP or short hostname, so Windows has to do **name resolution (hosts/DNS)** to find the IP.
### How `ipconfig /displaydns` relates to FQDN & name resolution

- When you **ping a name** (e.g. `2k8serv` or a full FQDN like `server1.lab.local`), Windows must first **resolve the name → IP**.
    
- It does this by checking:
    
    1. **hosts file**
        
    2. **DNS cache**
        
    3. **DNS server**
        
- **After** it finds the IP, Windows **stores the result** in the **DNS cache** (so the next time it doesn't have to ask again).

![](../images/Pasted%20image%2020251204094043.png)


**IPV6**
### IPv6 – Lab 2b Notes

**Why IPv6 exists**

- IPv4 = 32-bit addresses → almost out of space.
    
- IPv6 = **128-bit** addresses → huge address space + some protocol improvements.
    

**IPv6 address format**

- Written in **hex**, 8 blocks separated by `:`
    
    - Example: `fe80::495d:f2e4:3c5e:b9c5`
        
- `::` can be used once to **compress** consecutive `0000` blocks.
    

**Important IPv6 address types**

- **Link-local**:
    
    - Starts with `fe80::/10` (e.g. `fe80::495d:...`)
        
    - Valid only on the **local link**, not routed.
        
- **Global unicast**:
    
    - Public, routable IPv6 addresses (like public IPv4).
        
- **Loopback**:
    
    - `::1` (equivalent of `127.0.0.1` in IPv4).
        

### Commands used in the lab (Windows)

**View IPv6 config**

`netsh interface ipv6 show address`

- Shows IPv6 addresses on each interface (link-local, global, etc.).
    

`netsh interface ipv6 show interfaces`

- Lists IPv6 interfaces and their index numbers.
    

`netsh interface ipv6 show dnsservers`

- Shows DNS servers used for IPv6.
    

`netsh interface ipv6 show route`

- IPv6 routing table (where IPv6 traffic is sent).
    

`netsh interface ipv6 show neighbors`

- Neighbor cache = IPv6 version of ARP table (uses **Neighbor Discovery**, not ARP).

### Testing IPv6 connectivity

`ping -6 <IPv6-address>`

- Forces **IPv6 ping** (ICMPv6 echo request/reply).
    
- Used in the lab from the VM to the Windows host and vice-versa.
    
### Wireshark in the lab

- Capture on the correct interface while doing `ping -6`.
    
- You should see:
    
    - `ICMPv6 Echo Request`
        
    - `ICMPv6 Echo Reply`
        
- Confirms that **IPv6 packets are actually being sent and received** between the two machines.

![](../images/Pasted%20image%2020251204094619.png)

![](../images/Pasted%20image%2020251204094637.png)
