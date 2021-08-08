# Mr-Robot-walkthrough

# Get VMs IP

nmap 192.168.1.0/24

Nmap scan report for 192.168.1.2

#  Enumeration

nmap -sV -sC --script vuln 192.168.1.72

![image](https://user-images.githubusercontent.com/52453415/128607576-d0016042-a0e7-49b6-87ec-8e93175ec1c3.png)


the services was not vulnerable but the scan revealed some exposed files 

/robots.txt

![image](https://user-images.githubusercontent.com/52453415/128607639-17e35a5c-1761-4811-ba53-63aa431ff04e.png)

![image](https://user-images.githubusercontent.com/52453415/128607647-4098f8e5-34e6-4fce-b53d-afc51cc526a9.png)

our first key and a file that seams to be a wordlist but repeated over and over again , need to be cleaned up

![image](https://user-images.githubusercontent.com/52453415/128607738-0a0a9756-bcbf-40fc-b3ff-9a01786f8b8e.png)


nothing else is useful even the source page for the server 

i've tried to brute force directories using dirbustrer ; nothing useful unless for login page 

/wp-login.php

trying sqli 

nothing 

![image](https://user-images.githubusercontent.com/52453415/128607919-dc35a632-5d6c-4a4a-b329-767721f5013a.png)

now time to use the wordlist 

I've entered random login credentials , then i clicked on forgot my password 

ERROR: Invalid username. [Lost your password?](http://192.168.1.2/wp-login.php?action=lostpassword)

i moved to burpsuite to perform sniper attack on username using the word list but the attack was very very slow and all responses was status 200

![image](https://user-images.githubusercontent.com/52453415/128608024-160ca169-6d7a-4cdc-80cb-7f3cd37f01ce.png)

I've tried different approach using curl and inspect the response 

i found that every wrong attempt' response comes with " invalid username " ; so I've written a simple code that inspect the response looking for this sentence and if the response varies this would mean that the username is correct 

![image](https://user-images.githubusercontent.com/52453415/128608121-ade79de2-d0f1-4462-ab69-652d0aa2981b.png)

running the scripts get me elliot as valid username 

![image](https://user-images.githubusercontent.com/52453415/128608136-73785bb3-cff6-4c8d-84bf-fcabcf1eb43a.png)

now back to the login page and brute force the password with the same technique but this time i will grep " The password you entered for username elliot is wrong " as the response says

but I've got a problem 

the response from the user Agent varies from the curl response there is some thing wrong 

![image](https://user-images.githubusercontent.com/52453415/128608463-b25429a6-6385-4324-98d7-cf75d5cf08a7.png)

![image](https://user-images.githubusercontent.com/52453415/128608376-b9a68d42-f948-41a5-834c-5ca9c281b731.png)


I've inspected curl response and i figured out why ; the server don't accept login request without cookies 

![image](https://user-images.githubusercontent.com/52453415/128608220-ca98372c-590a-40a0-85d5-5e4b89eee990.png)


so i took  the cookie from burp suite and i sat it  for curl and edited the script to be 

![image](https://user-images.githubusercontent.com/52453415/128608390-f1627a40-9ebc-4545-971d-d498b96dae4e.png)

running the script again 

![image](https://user-images.githubusercontent.com/52453415/128608405-fa30fc07-0f76-4564-a5aa-3f27000b1578.png)

back to the website ; login 

![image](https://user-images.githubusercontent.com/52453415/128608434-aece7bf7-be2a-417d-8e21-2bff73ed977f.png)


checking the plugins 


i found two different vulnerable plugins that can be activated 

wp JET and WPtouch both are vulnerable to sqli 

i activate them and tried to exploit them buf i failled 

but I've realized that elliot has admin privilege so i went to edit php files so include reverse shell 

 Appearance -> Editor and swap the 404.php code with  my php reverse shell 
 
 ![image](https://user-images.githubusercontent.com/52453415/128643966-a619c924-3564-48c2-8402-e4e907ff9435.png)


![image](https://user-images.githubusercontent.com/52453415/128643974-c102cf32-0a09-4c99-8f65-f4a575583f88.png)

Then start a listener and hit a random page like 192.168.1.2/sdfdsfsdf get the shell 

some navigation to get the second key and robot passwd hashed


A quick search revealed that the MD5 hash stands for abcdefghijklmnopqrstuvwxyz

![image](https://user-images.githubusercontent.com/52453415/128644158-371fbab8-541b-420c-9932-5fded8e70ba2.png)


now swich to robot 

![image](https://user-images.githubusercontent.com/52453415/128644031-6c983cbc-f45c-42ed-afbd-87276c6a9591.png)

# priv Esc

sudo -l ; robot my not run sudo

searching for binaries with setuid and root privilege  

find / -user root -perm -4000 2>/dev/null

![image](https://user-images.githubusercontent.com/52453415/128644121-162c5cc2-61e9-4964-b3a1-b5c643c731a4.png)

nmap --interactive

!sh 

whoami >> root 

find / -name "key-3-of-3.txt"


