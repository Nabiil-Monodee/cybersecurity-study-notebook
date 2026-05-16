**FTP (File Transfer Protocol)**

**FTP (File Transfer Protocol)** is an application-layer protocol used to **transfer files between a client and a server** over a TCP/IP network.  

It is mainly used to:
- Upload files from a client to a server
- Download files from a server to a client
- Manage files on a remote server (list, rename, delete, change directories)

FTP is **not secure by default** – usernames, passwords and data are sent in **cleartext**.

**Windows:**
In this lab, I use FTP from **Windows 7** to upload an HTML file to the **FreeBSD web server**:

1. Open Command Prompt on Windows and connect:
    
    `ftp 192.168.206.132`
    
    Log in as `user00`.
    
2. Inside FTP:
    
    - Set local folder (where my HTML file is stored):
        
        `lcd C:\Users\mdx\Desktop`
        
    - Go to the web root on FreeBSD:
        
        `cd /usr/local/www/apache24/data`
        
    - Upload the file:
        
        `put goobye.html     (or send index.html)`
        
    - Check files on the server:
        
        `ls`

    ![](../images/Pasted%20image%2020251204114709.png)
        
3. Then I can view the uploaded page in a browser using HTTP, e.g.:  
    `http://192.168.206.132/` or `http://192.168.206.132/goodbye.html`.

    ![](../images/Pasted%20image%2020251204114515.png)

**Security point:** because FTP is cleartext, an attacker who sniffs the network could steal my FTP username/password or tamper with uploaded files.

**Linux:** 

![](../images/Pasted%20image%2020251204114910.png)

`nano` is a **simple text editor that runs in the terminal**, used to create and edit text files (like a command-line Notepad).

On terminal, type nano lab.html and write you html code inside nano

<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Marvel Page</title>
</head>
<body>

    <!-- Big red title, with some words in other colours -->
    <h1 style="color: red; font-size: 48px; text-align: center;">
        Hello,
        <span style="color: blue;">welcome</span>
        to our
        <span style="color: orange;">Marvel</span>
        page
    </h1>

    <h2 style="color: darkred;">My name is Star-Lord</h2>

    <p>
        Name's Star-Lord - yeah, the legendary outlaw, thank you very much.
        I'm a human-Celestial hybrid who got snatched from Earth, raised by Yondu's
        Ravagers, and somehow ended up leading the Guardians of the Galaxy.
    </p>

    <p>
        I've got a pretty cool helmet, great taste in music, and a complicated thing
        going on with Gamora. I help save the galaxy, crack jokes, and sometimes
        mess things up before fixing them again.
    </p>

</body>
</html>

![](../images/Pasted%20image%2020251204120631.png)

Launch the HTML on Firefox:

![](../images/Pasted%20image%2020251204120828.png)

**E-mail Protocol: SMTP and POP3**

Simple Mail Transfer Protocol (SMTP) is used for sending e-mail messages between servers and operates on TCP port 25. Messages sent are retrieved by using either Post Office Protocol version 3 (POP3) or Internet Message Access Protocol version 4 (lMAPv4). POP3 Operates on TCP port 110, and IMAP operates on TCP port 143. An e-mail client is usually configured to work with these protocols and makes it easier to manage e-mail.

**Telnet to the mail server FreeBSD** 
command: telnet 192.168.206.132 25 

![](../images/Pasted%20image%2020251204131352.png)

From Kali, I used `telnet 192.168.206.132 25` to connect directly to the FreeBSD mail server on the SMTP port (25). The banner `220 FreeBSD ESMTP Sendmail...` shows that the SMTP service is running and ready. At this point, I can manually type SMTP commands (`helo`, `mail from:`, `rcpt to:`, `data`, `.`) to send an email from the command line, acting like a mail client. This demonstrates how SMTP is a simple text-based protocol and how email can be sent without any GUI.

![](../images/Pasted%20image%2020251204132250.png)

![](../images/Pasted%20image%2020251204132334.png)

now to read the email, we have to read that email using POP3.
- **SMTP = postman that _sends_ mail between mail servers.**
- **POP3 = mailbox that you _read_ your mail from.**
Telnet to FreeBSD server through POP3 port 110
telnet 192.168.206.132 110

![](../images/Pasted%20image%2020251204133658.png)

Enter the login credentials and manually write user and pass 

![](../images/Pasted%20image%2020251204133841.png)

Then enter command list , to list the message in your mailbox
Enter command retr to select the last message which is your mail that you sent.

![](../images/Pasted%20image%2020251204133938.png)

press Enter 

![](../images/Pasted%20image%2020251204134723.png)

Here the email that you wrote.

It is not necessary to access the email box from the server itself, if you have the ip address and the right port you can connect from any machine on the same LAN.
- telnet 192.168.206.132 110 
- user user00
- pass user00
- list
- retr 6

![](../images/Pasted%20image%2020251204135428.png)
