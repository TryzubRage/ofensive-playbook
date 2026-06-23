# smbmap

SMB share enumeration tool that quickly lists readable and writable shares with optional anonymous or credentialed access.

## Commands Used

### Anonymous share listing

<!-- cmd: linux -->
```bash
smbmap -H $TARGET -u '' -p ''
```

Used on: **Kenobi**

confirmed the `anonymous` share was readable after Nmap SMB scripts returned nothing useful.

## Related

- [smbclient](smbclient.md)
- [../../playbooks/enumeration/smb.md](../../playbooks/enumeration/smb.md)



### Anonymous and authenticated share listing

<!-- cmd: linux -->
```bash
smbmap -H $TARGET -u '' -p ''
smbmap -H $TARGET -u 'ArthurMorgan' -p 'DeadEye'
```

Used on: **coldvvars**

anonymous enum found the SMB surface; authenticated enum confirmed access to `SECURED`.

### Domain credential share listing

<!-- cmd: linux -->
```bash
smbmap -H $TARGET -u 'alex.turner' -p 'Checkpoint2024!'
smbmap -H 10.129.23.75 -u 'mark.davies' -p 'Checkpoint2024!'
```

Used on: **Checkpoint**

Confirmed initial SMB visibility and later write access to the `DevDrop` VSIX share.
