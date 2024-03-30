
# Nmap

```
$ sudo nmap  -v -sV -sC 10.10.11.233 -p22,80 -oN nmap.txt   
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-30 00:28 EET

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://analytical.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```





![image](https://github.com/0xbazooka/HackTheBox/assets/99322823/0b6e2bdc-bf84-451f-a242-2cf4bb1ec289)
