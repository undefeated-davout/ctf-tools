# commands

## Spawning a TTY Shell

```bash
python -c 'import pty; pty.spawn("/bin/sh")'

echo os.system('/bin/bash')

/bin/sh -i

perl -e 'exec "/bin/sh";'

# perl
exec "/bin/sh";

# ruby
exec "/bin/sh"

# lua
os.execute('/bin/sh')

# From within IRB
exec "/bin/sh"

# From within vi
:!bash

# From within vi
:set shell=/bin/bash:shell

# From within nmap
!sh
```

## MISC

```bash
# ↓ It's sooo cool! Because it can use clear(Ctrl + l) command.
rlwrap nc -nlvp {port}
# tty shell
/usr/bin/script -qc /bin/bash /dev/null
```
