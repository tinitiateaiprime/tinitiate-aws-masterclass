[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 14: Delete an S3 object

## Goal

Delete one explicitly named object and verify the result.

```python
import boto3
from botocore.exceptions import ClientError

s3 = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "s3", endpoint_url="http://localhost:4566"
)
bucket, key = "course-data", "incoming/orders/1001.json"
s3.delete_object(Bucket=bucket, Key=key)

try:
    s3.head_object(Bucket=bucket, Key=key)
except ClientError as error:
    assert error.response["ResponseMetadata"]["HTTPStatusCode"] == 404
    print("object no longer has a current readable version")
```

Delete is idempotent: deleting a missing key generally succeeds. With versioning enabled, an unqualified delete creates a delete marker; older content still exists and can incur storage charges. To permanently remove a version, pass its `VersionId`. Object Lock, retention, replication, and lifecycle rules can change the effective behavior. Log bucket, key, version ID, and request ID for auditable production deletes.


