## 3

`whatis` one line description of the tool
`apropos` give it a description and it will suggest related tools
`which` find command

`sudo updatedblocate flag.txt`  find file with name

`passwd` change password

`apt-cache search <apache2>`

`sudo apt remove <haga>` delete a program
`sudo apt remove --purge <haga>` remove config files

## 4

`bash` opens a new bash session
`exit` exits to the prev session

- `env` shows all enviroment variables
- to change `$PATH` variable value:
`export PATH=/my/new/path:$PATH` NOT PERMENANT
- `unset PATH` deletes its content
- ![[Pasted image 20230428101037.png]]

ctrl+r
`;`   execute one command then the other
`&&`  when the prev  command is done *successfully* execute the second comand

## 5

`tee` y3rad el output + redirect into the next command
`xargs`

## 6

Regex
![[Pasted image 20230428135304.png]]

`sed`  substitute: `sed 's/password/<with this word>/g' <file.txt>/g` for global or else hybos 3ala awel kelma fe kol satr w bas
hy8ayar w y3rad el output without editing the actual file

`splitawk`   very useful lots of options
`awk '{print $1}'cut -d " " -f 1`

![[Pasted image 20230428151524.png]]

`comm <file1> <file2>`  comparing files

## 7

### bg and fg

`<command>&`  //go into the background
`ps aux | grep "command"`  we'll see it
or `jobs`

`fg`  bas htforeground last command
%[id]   command id
Ctrl+Z  pause running command
`bg` resume running in background

`nohup <command>` run regardless of closing the bash session

`kill sleepkillall sleep` hykill kolo

## 8 downloading files/ customizing bash env

### file and command monitoring

`tail -f /var/log/apache2/access.log`  view activity of the apache server live

`watch -n 0.5 -d free-n` seconds between every refresh (default is 2 sec)
`-d` highlight the change
`free` shows free/used/total memory (بتتغير كل شويه)

```
`df`

```

### Downloading files

`wget` keeps the original server date
`curlaxel <url> -n <no. of connections> -a -o <outputfilename>`
use it for large files

### customizing the bash env

`export HISTIGNORE="&:ls:exit:clear:history:cd:cd .."`  dont store insignificant commands in history //DIDNT WORK
`alias l='ls -lah'`

[how to make it persistant](https://unix.stackexchange.com/questions/645540/how-to-add-persistent-aliases-on-kali-linux)

# Module 4 - Practical Tools

## 9 nc

- bind-shell
for transfering files
`nc -nlvp 4444 < sent.txt` `nc -nv <ip> 4444 > received`
- reverse-shell
- relay (access a whole network from a compromised server)
![[Pasted image 20230430091519.png]]

## 10

# Module 6 - Passive Info Gathering

## 18

![[Pasted image 20230508185057.png]]
![[Pasted image 20230508185116.png]]

check out job offerings to know what technologies they run, what they need, ..etc
[crunchbase](https://www.crunchbase.com/)  to

`the harvester` find emails and subdomains..etc

# Module idk

`masscan` very fast scanning ports-wise bas msh adaq
law m7tag ascan network kebera b3den a3raf tafaseel aktar b `nmap`

## 28. SMB Enumeration

eternal blue vulnerability

wannaCry Ransomware Attack
