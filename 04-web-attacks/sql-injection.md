In this lab, we explore **web SQL injection**, a common vulnerability in modern web applications that use SQL databases to store and manage information. SQL (Structured Query Language) allows applications and users to query databases, but if user input is not properly validated (incomplete mediation), an attacker can **inject malicious code into a query**, causing the database to process unauthorized commands and reveal sensitive data. Using the penetration testing tool **sqlmap** on Kali Linux, we perform a controlled SQL injection attack against a deliberately vulnerable website to detect the flaw, list databases and tables, and retrieve sensitive information such as usernames and passwords. This helps us understand how SQL injection works in practice and why secure coding and proper input validation are essential.

1. Open terminal on Kali Linux  and launch the terminal as admin privilege 

![](../images/Pasted%20image%2020251202124428.png)

2. Run the command :
==**sqlmap -u** [**http://testphp.vulnweb.com/listproducts.php?cat=1**]==

 When the website developer designed this page to retrieve data from the database, they took the `cat` parameter directly from the URL and used it in the SQL query without sanitizing or safely handling it. Because of this, `cat` becomes an injection point , a way for an attacker to send their own SQL commands into the database.

![](../images/Pasted%20image%2020251202124702.png)

![](../images/Pasted%20image%2020251202125648.png)

![](../images/Pasted%20image%2020251202125705.png)

`http://testphp.vulnweb.com/listproducts.php?cat=1` points to a site called **vulnweb**, which is an example PHP web app that is **intentionally vulnerable** for learning and testing security tools like Acunetix and `sqlmap`. It's maintained by the company behind Acunetix / Invicti.

3. Enumeration of Database
sqlmap already know that in the URL itself theres a way to access the databse of the website through cat. Using this command: ==sqlmap -u "http://testphp.vulnweb.com/listproducts.php?cat=1"== --dbs== enumerate the database of the website vulneweb.com   

![](../images/Pasted%20image%2020251202131319.png)

![](../images/Pasted%20image%2020251202131348.png)

As you can see, two database was found in the system:
- **acuart**
- **information_schema**
3. Using Table
Now we use the command -D to select the database and command --tables to list the table of all the database present.
==**sqlmap -u** [**http://testphp.vulnweb.com/listproducts.php?cat=1**](http://testphp.vulnweb.com/listproducts.php?cat=1) **-D acuart --tables**==

![](../images/Pasted%20image%2020251202131744.png)

![](../images/Pasted%20image%2020251202131901.png)

4. Using columns
Now we tell sqlmap the specified databse using -D and the -T to request which column we want to access in the database
For this lab we will try to find users in the databse acuart
==**sqlmap -u** [**http://testphp.vulnweb.com/listproducts.php?cat=1**](http://testphp.vulnweb.com/listproducts.php?cat=1) **-D acuart -T users --columns**==  

![](../images/Pasted%20image%2020251202132316.png)

![](../images/Pasted%20image%2020251202132334.png)

after extracting the users  column of the database acuart using -T users  --column we found that thres address , cart, email, name , password, phone number and  uname of the users.

5. Using Data 
Let find out what data can we extract from each columns.
==**sqlmap -u** [**http://testphp.vulnweb.com/listproducts.php?cat=1**](http://testphp.vulnweb.com/listproducts.php?cat=1) **-D acuart -T users -C email,name,pass,uname --dump**==  
 -D : selection of database
 -T : selection of tables
 -C : selection of columns

 ![](../images/Pasted%20image%2020251202144406.png)

![](../images/Pasted%20image%2020251202144347.png)

In the `acuart` database, the `users` table contains a `cart` column whose values appear to be **hashed** rather than stored in plain text. After dumping this column with  
`sqlmap -u "http://testphp.vulnweb.com/listproducts.php?cat=1" -D acuart -T users -C cart --dump`,  
sqlmap recognised the values as  hashes and prompted us to try cracking them using a dictionary attack.

![](../images/Pasted%20image%2020251202145100.png)

![](../images/Pasted%20image%2020251202145035.png)

result:
no cracked 

![](../images/Pasted%20image%2020251202145550.png)

Maybe we can use john the ripper to crack it 
First lets save the hash algo in a text file hash.txt and crack it using command 
john hash2.txt

![](../images/Pasted%20image%2020251202152202.png)

And yes we wait...
uhmm we wait....
and wait...
3 hours later....

![](../images/Pasted%20image%2020251202152237.png)

we still wait...

![](../images/Pasted%20image%2020251202152419.png)

just kidding i have cancled the cracking process after 15min, but maybe the password of this hash is not in the wordlist of john. But the method is right.
