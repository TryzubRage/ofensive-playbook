# ncat

`ncat` is the Nmap project's network Swiss-army knife. In this repo it is used to send a UDP packet from a specific source port.

## Commands Used

### UDP Packet From a Controlled Source Port
<!-- cmd: linux -->
```bash
echo "data" | ncat -p 5000 -u cctv.thm
```
Used on: **Bypass**

Flags:
- `-p 5000` sets the local source port.
- `-u` switches from TCP to UDP.
