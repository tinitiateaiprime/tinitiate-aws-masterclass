[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 07: Create an S3 bucket
[⬅ Back](./README.md) | [🏠 Home](./README.md)
## Goal

Create a uniquely named bucket with boto3 and verify it. Bucket names are globally unique in AWS, lowercase, and generally should be treated as permanent identifiers.

```python
import boto3
from botocore.exceptions import ClientError

s3 = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "s3", endpoint_url="http://localhost:4566"
)
bucket = "course-data"

try:
    s3.create_bucket(Bucket=bucket)
    s3.get_waiter("bucket_exists").wait(Bucket=bucket)
    print(s3.head_bucket(Bucket=bucket)["ResponseMetadata"]["HTTPStatusCode"])
except ClientError as error:
    print(error.response["Error"])
    raise
```

For a real region other than `us-east-1`, pass `CreateBucketConfiguration={"LocationConstraint": region}`. Do not pass that constraint for `us-east-1`. In AWS, enable Block Public Access, encryption, versioning, lifecycle rules, and tags according to the workload's policy.

CLI verification:

```bash
aws s3api head-bucket --bucket course-data --profile floci --endpoint-url http://localhost:4566
```

Run the program twice and observe the idempotency/error behavior. Production automation should distinguish “already owned by you” from “name owned by someone else.”


