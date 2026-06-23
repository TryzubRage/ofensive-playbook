# Volatility 3

Volatility 3 analyzes memory images. In this repo it is used to locate and dump Windows registry hives from a VMware memory snapshot before offline credential extraction with Impacket.

## Commands Used

### Install and Locate the CLI
<!-- cmd: linux -->
```bash
pipx install volatility3
export VOL=/home/kali/.local/share/pipx/venvs/volatility3/bin/vol
```
Used on: **Checkpoint**

### List and Dump Registry Hives
<!-- cmd: linux -->
```bash
vol -f snapshot.vmem windows.registry.hivelist.HiveList
mkdir dump
vol -f snapshot.vmem \
  -o dump \
  windows.registry.hivelist.HiveList --dump
```
Used on: **Checkpoint**
