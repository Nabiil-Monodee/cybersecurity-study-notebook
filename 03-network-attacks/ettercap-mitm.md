Ettercap is a network security tool designed for performing **man-in-the-middle (MITM) attacks** on local networks. It specializes in things like **ARP poisoning**, **DNS spoofing**, and transparent sniffing of traffic that flows between hosts on the same LAN. Beyond just capturing packets, Ettercap can **modify traffic on the fly**, inject data, kill connections, and run plug-ins to perform advanced attacks (like replacing downloads, hijacking sessions, or faking services). Because of this, it’s widely used in labs and penetration tests to demonstrate how weak or unencrypted protocols (like FTP, HTTP, Telnet) can be easily observed and abused if an attacker is on the same network.

Ettercap is a network security tool that is designed to perform man in the middle (MITD)attack. it is a tool that can do things like ARP poisoning, DNS spoofing and transparent sniffing traffic between hosts on LAN network , private.Ettercap can modify traffic . inject data, kill connections and more advanced attack like hijacking sessions and fakiing services.
How ARP poisoning works under the hood:
On a local network Area LAN devices need to map IP to MAC addresses so they can actually send frames and they use ARP(address resolution protocol ) for this.
when a host wants to talk to 192.168.1.1 for example, it shouts on the network Who has 192.168.1.1?Tell me your MAC. So, on LAN this is how hosts communicate, it need the MAC for more rapid access and knows that by launching a broadcasting messages and then saved to the ARP cache. The proble is that ARP blidnly trust any reply, and it doenst verify authenticity. By this attackers can abuse this by sending fake ARP replies which is spoofing. ARP spoofing is a man in the middle methos that forges ARP reply packets to lie about the MAC address own. The attacker send the fake ARP replies to the viction that says i am the gateway IP use my MAC address and then ARP blindly trust these replies both device update their ARP cache but with the fake MAC address of the attacker. Now every data can be capture by the attacker.
### How ARP poisoning works under the hood

On a LAN, devices need to map **IP → MAC** so they can actually send frames. They use **ARP (Address Resolution Protocol)** for this.

- When a host wants to talk to `192.168.1.1`, it shouts on the network:  
    **“Who has 192.168.1.1? Tell me your MAC.”**
    
- The real owner of that IP replies with its MAC, and the sender stores it in an **ARP cache**.
    

The problem: **ARP blindly trusts any reply**. It doesn’t check if it asked for it, and it doesn’t verify authenticity.

An attacker abuses this by sending **fake ARP replies** (spoofed):

- To the victim:  
    “Hey, I am `192.168.1.1` (gateway), and my MAC is **attacker’s MAC**.”
    
- To the gateway:  
    “Hey, I am `victim_IP`, and my MAC is **attacker’s MAC**.”
    

Both machines update their ARP caches with these lies. Result:

- Victim sends traffic meant for the gateway → to attacker’s MAC.
    
- Gateway sends traffic meant for victim → to attacker’s MAC.
    

The attacker then **forwards** the traffic to the real destination so everything still “works”, but all packets pass through them first = **Man-in-the-Middle**.

### How DNS spoofing works under the hood

DNS is the system that turns names like `www.google.com` into IP addresses. Normally:

1. Client asks a DNS server: “What is the IP of `example.com`?”
    
2. DNS server replies with the **real** IP.
    
3. Client connects to that IP.
    

In **DNS spoofing**, the attacker tries to **trick the client into using a fake IP**:

- If the attacker is already in the middle (e.g., via ARP poisoning), they can see DNS requests and quickly send a **forged reply**:
    
    - “`example.com` is at `attacker_IP`.”
        
- If the victim accepts this fake response (often because DNS doesn’t verify authenticity in simple setups), the victim’s browser goes to the attacker’s IP instead of the real server.
    

That fake IP could point to:

- A phishing website that looks like the real one,
    
- A fake update/download server,
    
- Or a server that just silently logs what the victim is doing.
    

So, **ARP poisoning** gets you in the middle of the traffic on the LAN, and **DNS spoofing** lets you redirect specific website names to any IP you control.

Attack Lab

1. Type ettercap -G to start Ettercap GUI
![[Pasted image 20251127134055.png]] 
2. click the Tick icon and then click on three dot in the right hand of the menu :Choose Hosts - Scan for Hosts.
This will start an ARP scan to detect what machines are up on the subnetwork. It will add those machines to a host list.
This will start an ARP scan to detect what machines are up on the subnetwork. It will add those machines to a host list.
![[Pasted image 20251127134453.png]] 
3. Choose Hosts - Host List
In the Host List you will find every devices on the network that ettercap scanned. 
![[Pasted image 20251127134535.png]] 
4. Select to target machine ip address and click Add to Traget 1 to select the target.
![[Pasted image 20251127134723.png]]  
5. After added the target Ip address to the target list click in the menu bar and select Targets Menu option and then select Current Targets
![[Pasted image 20251127134932.png]] 
6.  Now go to the MITM menu option and select Arp poisoning and choose the remote sniffing option, click ok.
![[Pasted image 20251127135021.png]] 
7. The ARP poison attack is happening underneath. You now have access to all the traffic which is being routed to the IP address which you have entered earlier
![[Pasted image 20251127135123.png]] 
8.  Login from the target machine and try to connect to a server  and sees what does ettercap catures
![[Pasted image 20251127140031.png]]
Those are credential i have typed 
![[Pasted image 20251127140051.png]] 
9. go onto profile and sees 
![[Pasted image 20251127142028.png]] 
Ettercap successfully intercepted the FTP session between Metasploitable and Telehack, automatically extracted the login credentials, and saved them in this profile view—showing how easy it is to steal usernames and passwords from unencrypted protocols when you’re in the middle of the network.
![[Pasted image 20251208210256.png]]

**In this lab you used ARP poisoning so that your Kali machine could silently sit in the middle of the traffic between Metasploitable (the victim) and the gateway/Internet: Ettercap sent fake ARP replies to both sides, making the victim think “the gateway’s IP is at Kali’s MAC” and the gateway think “the victim’s IP is at Kali’s MAC,” so all packets between them were routed through Kali. When the victim then ran `ftp telehack.com` and logged in with `cst353520 / cst3530`, that FTP traffic (which is unencrypted) flowed victim --> Kali -->gateway -->Telehack and back, and Ettercap-already in MITM position because of the ARP spoofin-could see and parse those packets, recognize the FTP session, and extract and display the username and password in the Profile Details window.**