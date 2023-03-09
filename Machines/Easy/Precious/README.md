### Hello Hackers,
this is the first machine I solve on hack the box AND my first writeup, so any feedback would be much appreciated (my linkedin: https://www.linkedin.com/in/basmatalaat/)

let's begin ;)

we'll start by an nmap scan of the ip, first I pinged the ip to see if it's up


with the flags: `-oN` → saving the output to a file, 
                                                       `-sV` → service/version info, 
                                                       `-sC` → script scan
 
`sudo nmap -sV -sC -oN nmap.txt 10.10.11.189`
