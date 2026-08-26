[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 05: `.aws` folder and Floci profile

[⬅ Back](./README.md) | [🏠 Home](./README.md)

## Goal

Create an isolated CLI/SDK profile for local work while keeping real AWS credentials safe.

AWS reads `~/.aws/config` and `~/.aws/credentials` on macOS/Linux and `%UserProfile%\.aws\...` on Windows. Create them with the CLI:

```bash
aws configure set aws_access_key_id test --profile floci
aws configure set aws_secret_access_key test --profile floci
aws configure set region us-east-1 --profile floci
aws configure set output json --profile floci
```

The resulting credentials are deliberately fake and are accepted only by the emulator. Select the profile with `--profile floci` or `AWS_PROFILE=floci`. Floci still needs an endpoint override:

```bash
aws s3api list-buckets --profile floci --endpoint-url http://localhost:4566
```

For Python, centralize client creation:

```python
import boto3

session = boto3.Session(profile_name="floci", region_name="us-east-1")
s3 = session.client("s3", endpoint_url="http://localhost:4566")
```

Do not place `.aws` inside this repository and never commit access keys. In real AWS, omit `endpoint_url`; prefer AWS IAM Identity Center for humans and IAM roles for compute. Check the selected identity before destructive exercises:

```bash
aws sts get-caller-identity --profile floci --endpoint-url http://localhost:4566
```


