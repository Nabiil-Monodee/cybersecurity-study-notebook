John the Ripper is a password-cracking tool that takes password hashes and tries to recover the original passwords. It does this by generating guesses, hashing them with the same algorithm, and checking for matching hashes. In this lab, we use John to perform a **dictionary attack**, which tests passwords from a wordlist of common choices, and a more advanced **hybrid attack**, which takes dictionary words and automatically adds variations such as numbers, symbols, or case changes. This shows how weak or predictable passwords can be quickly cracked, even when only their hashes are available.

To test machine cracking speed:
The `john --test` command runs a quick self-test and benchmark of John the Ripper to make sure it's working properly. It tests several common hash types and measures how fast your system can process them. The output shows the speed in "c/s" (crypts per second), which is the number of password guesses John can hash per second. This gives you an idea of how powerful your machine is for password cracking and how long different attacks might take.

![](../images/Pasted%20image%2020251130171327.png)

The wordlist for John the Ripper is in this folder:
cd /usr/share/john

The file **`password.lst`** is John's built-in **password wordlist** (similar idea to `rockyou.txt`).

To **view the file one page at a time**, use `less` (viewer for text files):
less password.lst


Dictionary Attack 
john --wordlist=password.lst /root/Desktop/file1.txt

- `john` → runs John the Ripper.
    
- `--wordlist=password.lst` → tells John to use `password.lst` as the list of guesses.
    
- `/root/Desktop/file1.txt` → file that contains the password **hashes** to crack.
    

What happens: John takes each word from `password.lst`, turns it into a hash with the same algorithm, and compares it to the hashes in `file1.txt`. When a hash matches, John has found that user's password.


![](../images/Pasted%20image%2020251130181011.png)

the command is john --wordlist=password.lst /root/Desktopfile1.txt and the password is abc of (user1)


Hybrid Attack 

Clear old result 

rm /root/.john/john.pot

![](../images/Pasted%20image%2020251130182220.png)

Attack :
john --wordlist=password.lst --rules /home/cst3530/Desktop/file1.txt

![](../images/Pasted%20image%2020251130182511.png)

- Still uses `password.lst` as the **dictionary**.
    
- `--rules` tells John to create **variations** of each word (add numbers, symbols, change case, etc.).
    
- This is a **hybrid attack**: dictionary + smart modifications, so it can crack passwords that are not in the wordlist exactly (like `pookie1`, `Pookie!`, etc.).

result :
one hash is cracked and the password is abc for user1
1g means 1 password found in this run

![](../images/Pasted%20image%2020251130182546.png)

Using the hybrid attack command `john --wordlist=password.lst --rules /home/cst3530/Desktop/file1.txt`, John was able to crack one of the hashes. It discovered that the password for user1 is `abc`, showing that the hybrid (rule-based) attack can successfully recover passwords that the simple dictionary attack alone did not find.


Default attack of john the ripper
sudo john /home/cst3530/Desktop/file1.txt > /home/cst3530/Desktop/test.txt

- **What this does**
    
    - Uses **John's default cracking mode**, which automatically chains attacks (single, wordlist + rules, then brute-force if needed).
        
    - Tries many different methods, not just dictionary or hybrid alone.
        
- **Why `>` is used**
    
    - `> /home/cst3530/Desktop/test.txt` sends all the output into a file called **`test.txt`** on your Desktop.
        
    - You can open `test.txt` later to see the full cracking process.
        
- **View cracked passwords**
    
    `john --show /home/cst3530/Desktop/file1.txt`
    
    - This cleanly lists any usernames and passwords that John managed to crack.
-

![](../images/Pasted%20image%2020251130190303.png)

sudo john file.txt > cracked.txt

![](../images/Pasted%20image%2020251130190242.png)
