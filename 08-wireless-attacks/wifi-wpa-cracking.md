Tools:
1. Kali Linux 
2. Aircrack-ng 
3. Wi-Fi Adapter with monitor mode + packet injection
4.  An Access Point(AP)


**Kali Linux**
Purpose-built toolkit: Kali bundles is a prepackaged pentest toolkit.

**Aircrack-ng**
Aircrack-ng is a group of Wi-Fi tools that help you _see_ what's happening on Wi-Fi airwaves and analyze the traffic.
airodump (scan), aireplay (inject/helper), aircrack (analyse).

**Monitor mode(why needed)**
normally your laptop, your NIC, only hears messages meant for it. So having a wifi adapter with monitor mode makes the wireless card listen to everything in the air such as all beacons, probes and data. It capture raw wifi frames that shw how AP and client talk.
**Analogy:** Managed mode = your post delivered to your mailbox only. Monitor mode = standing on the street listening to all conversations.  
**Keywords:** raw frames, beacons, management frames, promiscuous (Wi-Fi).

**Packet Injection**
Enables sending crafted frames. It makes a device to re-do a hanshskae or reponds and to be able to inject frames to capture handshake itself.

Access Point(Ap)
basically, it your router or hostpot from phone that the client connects to.

step one:
Run
==sudo airmon-ng check kill==
 why?
 this command stops background networking services on your machine like NetworkManager, dhclient etc which will fight with your wireless card(wifi adapter) then you put it into monitor mode.

![](../images/Pasted%20image%2020251105211216.png)

![](../images/Pasted%20image%2020251105211250.png)

this disabled the conflict services.

step 2:
run ==iwconfig== 
to check if your wifi interface have wlan0 or wlan1 for the wireless card.

step 3:
convert the interface wlan0 to monitor mode from manage mode(thats why we need the wifi adapter also)
==sudo airmon-ng start wlan0== 

![](../images/Pasted%20image%2020251105211635.png)

run iw dev to check if its on monitor mode

![](../images/Pasted%20image%2020251105211813.png)

or you can check using 
iwconfig 

![](../images/Pasted%20image%2020251105211924.png)

step 4: just in case if it doesnt show wlan0mon or wlan1mon , just for fun you can write it manually.
sudo ip link set wlan0 down
sudo ip link set wlan0 name wlan0mon
sudo ip link set wlan0mon up

those command are not necessary it just rename the interface.

step 5: 
run 
==sudo airodump-ng wlan0mon== 
you wifi card is now a radio scanner, in simplest for this command is a form os passive wifi scanning and you selected the interface of the wireless card wlan0mon(for my pc, your can be wlan1mon)

![](../images/Pasted%20image%2020251105212842.png)

![](../images/Pasted%20image%2020251105213124.png)

in this screenshot , in BSSID you will find the MAC address of each wifi in the air and the name of the wifi is located at the column ESSID.
mine was telecom_7.4GHZ(hostpost)

stop the capture using ctrl+c

noticed that on column PWR this is the power signal strenght, around 40 = strong signal, around 55= average signal, aroun70= weak signal and around 80/90 lower limit of signal strenght.

step 6: 

![](../images/Pasted%20image%2020251105213650.png)

sudo airodump-ng -w wificapture -c 11 --bssid <MAC ADDRESS>
sudo- run as root
airodump-ng= the Aircrack-ng suite tool that passively listesn and captures ifi frames

-w wificapture- write capture files suing prefix wificapture(its where the output will be generate and wificapture is a folder).

-c 11= the channel of your targetted wifi(refer in your wifi listing using airmon-ng wlan0mon)
this will show up:

![](../images/Pasted%20image%2020251105214431.png)


you will notices at the top that the no "wpa handshake <Mac address>"

step 7: open another terminal windows to deauth client

using the wifi mac address 

![](../images/Pasted%20image%2020251105214858.png)

we keep deauth the wifi from all devices connected to the wifi, which mean that the deauth injection will keep disconnecting the client until you stop it manually:

![](../images/Pasted%20image%2020251105215428.png)

some people try to add maximum deauth for example sudo aireplay-ng --deauth 0 <MAC address> wlan0mon ==10==
so this command will send only 10 deauth injection
but what will happen the user will be able to connect to the wifi as usual after the 10 injection. so always use the command in the scnresshot and as you will noticed on your phone you wont be able to connect to your wifi because it will keep disconeecting.


the thing is that when the user is trying to press on the ssid name to connect to the same wifi , the client is sending frames (wpa handshake)to your aircrack tools.

NOTE: DO NOT STOP THE CAPTURING OF THE 
sudo airodump-ng -w wificapture -c 11 --bssid <MAC ADDRESS>

step 8:
so when the user (client) try to connect to the wifi , on your other terminal where you ran the command 
sudo airodump-ng -w wificapture -c 11 --bssid <MAC ADDRESS>

you will noticed at the top it generate what tyupe pf handshake this wifi (Ap) have :

![](../images/Pasted%20image%2020251105215650.png)

so you captured the handshake


step 9:
stop the two terminal (deauth attack +airodump-ng)
 type ls and you will notices that you have the file wificapture.cap

 ![](../images/Pasted%20image%2020251105215846.png)

step 10:
run the command 
wireshark wificapture-01.cap & 
to open wireshark on that file.

step 11:
in wireshark it will show a lot of information but filter it using the filter bar and type : eapol

it will generate eapol protocol this is where you can have the cipher text of your password.

![](../images/Pasted%20image%2020251105220057.png)

click on the packet:

![](../images/Pasted%20image%2020251105220146.png)

and the goes onto 802.1x Authentication and you will notice a long hash algo in the PWA key Data filed:

![](../images/Pasted%20image%2020251105220245.png)

 now you captured the key but you dont really need to copy the hash algo

step 12:
on your kali launch a clean terminal and try to find your wordlist to brute force the password offline.


your wordlist (rockyou.txt) is located onto your kali and its located in directory /usr/share/wordlist 
(unzip your rockyou.txt)

then use the command 

aircrack-ng wificapture-01.cap -w /usr/share/wordlist/rockyou.txt

![](../images/Pasted%20image%2020251105220611.png)

and then you got the password cracked

![](../images/Pasted%20image%2020251105220631.png)
