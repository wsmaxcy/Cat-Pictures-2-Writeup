
# Cat Pictures 2 Writeup

Quick runthrough of a fun CTF from TryHackMe called [Cat Pictures 2](https://tryhackme.com/room/catpictures2).

## Recon

I started the box, then ran the Nmap command `nmap -sV -sC -p- #BOXIP -o nmap` to get see what ports were open on this box. It returned the follwing ports open:

 - 22 - OpenSSH 7.6p1
 - 80 - http nginx 1.4.6
 - 222 - OpenSSH 9.0 - *hmmm this seems weird*
 - 1337 - Another http web server
 - 3000 - ANOTHER http web server...
 - 8080 - Python http server..... *ok we get it*

Cool, now we have some stuff to explore. Lets check out the web pages first.
![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/1.png?text=First+Pic)
On port 80 we have a Lychee version 3.1.1 photo album full of cat pictures. Oh! like the name of the room. Probably a good idea to come to this one first.

![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/2.png?text=First+Pic)
On port 1337 we have OliveTin, which on it's [GitHub page](https://github.com/OliveTin/OliveTin) says to be a "safe and simple access to predefined shell commands from a web interface". Great! We know that there is a way to run code on the machine. I see it has run a script, ability to ping the host, and run an Ansible Playbook. I'll keep this in mind visiting the other assets.

![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/3.png?text=First+Pic)
On port 3000 we have a "Gitea" web application. On [their website](https://about.gitea.com/) it says that it is a "lightweight DevOps platform", so maybe this is where the scripts and runbooks are hosted?

![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/4.png?text=First+Pic)
On port 8080 there is the Nginx default webpage. This seems weird... might have some subdirectories that can be found.

At this point I ran `gobuster dir -u http://#TARGETIP:TARGETPORT -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 75` on every web application and got nothing interesting back. I also checked for vulnerabilites in each of the web applications too. Nothing interesting yet, so I decided to go check out the cat pics hosted on port 80.

After looking for more clues with the pics, I decided to download them all and see if there was any exif data located on the files themselves. I ran `exiftool *.jpg` in the file of pics and got back something interesting...

![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/5.png?text=First+Pic)
Looks like a port number and text file. I visited the url and was greeted with this...

![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/6.png?text=First+Pic)
Awesome! Access to something! 

when I login and see what's visible on the website clicked the user's repository and found this...

![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/7.png?text=First+Pic) 
flag1.txt found! 1/3

From here, I checked out the `playbook.yaml` file and saw this...

![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/8.png?text=First+Pic) 

I remembered earlier the web app hosted on port 1337 mentioned Ansible. I went back to the URL and saw the "Run Ansible Playbook" button with the Ansible logo. I knew from previous expereince with Ansible that the YAML file would be run by ansible, and the "shell: echo hi" would be executed by bash on the target machine. 

From here, I jumped onto [RevShells](https://revshells.com), plugged in my IP and port, copied the reverse shell payload over the "echo hi" code, set up an NetCat listener, clicked the "Run Ansible Playbook", and boom.

![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/9.png?text=First+Pic) 

From here, I checked around the file system to see if there was anything interesting. I then uploaded `linpeas.sh` onto the system and ran it. From here I found a few vulnerabilites that the box had. One that stood out was for getting sudo access called `sudo Baron Samedit`

![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/10.png?text=First+Pic) 

From here, I visited the website, downloaded the exploit, uploaded it to the target machine, ran the `make` command, then exploited the program. From here I ran the `./sudo-hax-me-a-sandwich` outfile and through a buffer overflow got root.

![App Screenshot](https://willmaxcy.com/assets/imgs/catpics2/11.png?text=First+Pic) 

Thanks for checking out my writeup! Feel free to reach out with any questions or comments.


## Feedback

If you have any feedback, please reach out to me at will@willmaxcy.com.


[![portfolio](https://img.shields.io/badge/my_portfolio-000?style=for-the-badge&logo=ko-fi&logoColor=white)](https://willmaxcy.com/)
[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/willmaxcy)
