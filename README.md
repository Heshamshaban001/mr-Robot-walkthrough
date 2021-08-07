# mr-Robot-walkthrough

# Get VMs IP

nmap 192.168.1.0/24

Nmap scan report for 192.168.1.2

#  Enumeration

nmap -sV -sC --script vuln 192.168.1.72

![image](https://user-images.githubusercontent.com/52453415/128607576-d0016042-a0e7-49b6-87ec-8e93175ec1c3.png)


the services was not vulnarable but the scan reveald some exposed files 

/robots.txt

![image](https://user-images.githubusercontent.com/52453415/128607639-17e35a5c-1761-4811-ba53-63aa431ff04e.png)

![image](https://user-images.githubusercontent.com/52453415/128607647-4098f8e5-34e6-4fce-b53d-afc51cc526a9.png)

our first key and a file that seams to be a wordlist but repeated over and over again , need to be cleaned up

![image](https://user-images.githubusercontent.com/52453415/128607738-0a0a9756-bcbf-40fc-b3ff-9a01786f8b8e.png)


nothing else is usfull even the source page for the server 

i've tried to brute force directories using dirbustrer ; nothing usfull unless for login page 

/wp-login.php

trying sqli 

nothing 

![image](https://user-images.githubusercontent.com/52453415/128607919-dc35a632-5d6c-4a4a-b329-767721f5013a.png)

now time to use the wordlist 

i've ented random login credintials , then i clicked on forgot my password 

ERROR: Invalid username. [Lost your password?](http://192.168.1.2/wp-login.php?action=lostpassword)

i moved to burpsuit to perform sniper attack on username using the word list but the attack was very very slow and all responses was status 200

![image](https://user-images.githubusercontent.com/52453415/128608024-160ca169-6d7a-4cdc-80cb-7f3cd37f01ce.png)

i've tried differnet apporch using curl and inspect the response 

i found that every wrong attemp' response comes with " invaild username " ; so i've written a simple code that inspect the response looking for this sentence and if the respone varies this would mean that the username is correct 

![image](https://user-images.githubusercontent.com/52453415/128608121-ade79de2-d0f1-4462-ab69-652d0aa2981b.png)

running the scripts get me elliot as vaild username 

![image](https://user-images.githubusercontent.com/52453415/128608136-73785bb3-cff6-4c8d-84bf-fcabcf1eb43a.png)

now back to the login page and brute force the password with the same technique but this time i will grep " The password you entered for username elloiot is wrong " as the response says

but i've got a problem 

the response from the user Agent varies from the curl response there is some thing wrong 

![image](https://user-images.githubusercontent.com/52453415/128608463-b25429a6-6385-4324-98d7-cf75d5cf08a7.png)

![image](https://user-images.githubusercontent.com/52453415/128608376-b9a68d42-f948-41a5-834c-5ca9c281b731.png)


i've inspected curl response andi figured out why ; the server don't accept login request without cookies 

![image](https://user-images.githubusercontent.com/52453415/128608220-ca98372c-590a-40a0-85d5-5e4b89eee990.png)


so i took  the cookie from burpsuite and i sat it  for curl and edited the script to be 

![image](https://user-images.githubusercontent.com/52453415/128608390-f1627a40-9ebc-4545-971d-d498b96dae4e.png)

running the script again 

![image](https://user-images.githubusercontent.com/52453415/128608405-fa30fc07-0f76-4564-a5aa-3f27000b1578.png)

back to the website ; login 

![image](https://user-images.githubusercontent.com/52453415/128608434-aece7bf7-be2a-417d-8e21-2bff73ed977f.png)


checking the plugins 


i found two differnt vulnarable plugins that can be activated 

wp JET and WPtouch both are vulnarable to sqli 

i activate them and tried to exploit them 



