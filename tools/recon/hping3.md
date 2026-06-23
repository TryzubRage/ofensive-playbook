# hping3

`hping3` crafts custom packets. In this repo it is used to send an ICMP packet with controlled payload content to trigger a Suricata-backed password recovery challenge.

## Commands Used

### Send ICMP Payload Content
<!-- cmd: linux -->
```bash
sudo hping3 -1 -c 1 -d 32 -E /dev/stdin cctv.thm <<< "User-Agent: Mozilla"
```
Used on: **Bypass**

Flags:
- `-1` sends ICMP echo requests.
- `-c 1` sends a single packet.
- `-d 32` sets payload size.
- `-E /dev/stdin` reads payload bytes from standard input.
