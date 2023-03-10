### Hello Hackers,
this is the first machine I solve on hack the box AND my first writeup, so any feedback would be much appreciated (my linkedin: https://www.linkedin.com/in/basmatalaat/)

let's begin ;)
# Nmap

first I pinged the ip to see if it's up

![image](https://user-images.githubusercontent.com/99322823/224144051-af782f7f-322b-40be-9ce3-a72fc8a23abc.png)

it's good to go, now we'll start with an nmap scan of the ip:

//`export ip=10.10.11.189` 

//defining a variable in the terminal with the machine IP so I don't have to type it each time, instead just wtite $ip (you can skip this step)

`sudo nmap -sV -sC -oN nmap.txt $ip`

`-oN` → saving the output to a file, in our case nmap.txt

`-sV` → service/version info detection

`-sC` → performs a script scan using the default set of scripts
 
#### the results:
 ```
 cat nmap.txt                              
# Nmap 7.93 scan initiated Wed Feb 15 19:58:40 2023 as: nmap -sV -sC -vv -oN nmap 10.10.11.189
Nmap scan report for 10.10.11.189
Host is up, received echo-reply ttl 63 (0.083s latency).
Scanned at 2023-02-15 19:58:53 EST for 13s
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 845e13a8e31e20661d235550f63047d2 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDEAPxqUubE88njHItE+mjeWJXOLu5reIBmQHCYh2ETYO5zatgel+LjcYdgaa4KLFyw8CfDbRL9swlmGTaf4iUbao4jD73HV9/Vrnby7zP04OH3U/wVbAKbPJrjnva/czuuV6uNz4SVA3qk0bp6wOrxQFzCn5OvY3FTcceH1jrjrJmUKpGZJBZZO6cp0HkZWs/eQi8F7anVoMDKiiuP0VX28q/yR1AFB4vR5ej8iV/X73z3GOs3ZckQMhOiBmu1FF77c7VW1zqln480/AbvHJDULtRdZ5xrYH1nFynnPi6+VU/PIfVMpHbYu7t0mEFeI5HxMPNUvtYRRDC14jEtH6RpZxd7PhwYiBctiybZbonM5UP0lP85OuMMPcSMll65+8hzMMY2aejjHTYqgzd7M6HxcEMrJW7n7s5eCJqMoUXkL8RSBEQSmMUV8iWzHW0XkVUfYT5Ko6Xsnb+DiiLvFNUlFwO6hWz2WG8rlZ3voQ/gv8BLVCU1ziaVGerd61PODck=
|   256 a2ef7b9665ce4161c467ee4e96c7c892 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFScv6lLa14Uczimjt1W7qyH6OvXIyJGrznL1JXzgVFdABwi/oWWxUzEvwP5OMki1SW9QKX7kKVznWgFNOp815Y=
|   256 33053dcd7ab798458239e7ae3c91a658 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIH+JGiTFGOgn/iJUoLhZeybUvKeADIlm0fHnP/oZ66Qb
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0
|_http-title: Did not follow redirect to http://precious.htb/
|_http-server-header: nginx/1.18.0
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Feb 15 19:59:06 2023 -- 1 IP address (1 host up) scanned in 26.16 seconds
```

we find 2 open ports:
-port 22 running ssh 
-port 80 running the Nginx web server version 1.18.0

since we have a web server running we'll use `nikto`, which is a web server scanner that identifies vulnerabilities and security issues in the web server. 
 
 `nikto -h $ip`
 
 `-h` → specify host/ip
 ```
 nikto -h $ip                         
- Nikto v2.1.6/2.1.5
+ Target Host: precious.htb
+ Target Port: 80
+ GET Retrieved x-powered-by header: Phusion Passenger(R) 6.0.15
+ GET Uncommon header 'x-runtime' found, with contents: Ruby
+ GET Uncommon header 'x-cascade' found, with contents: pass
```
researched the `X-Runtime` header and it basically reveals the time an application takes to serve a request
but it didn't make sense why its value was Ruby in the nikto scan, so I researched more and found this:

![image](https://user-images.githubusercontent.com/99322823/224205644-0796ca97-49f9-442f-997d-b01853789096.png)

I figured the server probably runs Ruby, so I kept it in mind.

tried fuzzing with gobuster and ffuf and nothing useful came back



port 80 redirect us to precious.htb
Let's quickly add that in /etc/hosts file


we'll browse the site hosted on port 80, 
