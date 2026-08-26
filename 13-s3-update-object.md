[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 13: Update an S3 object
[⬅ Back](./README.md) | [🏠 Home](./README.md)
## Goal

Replace an object's contents and understand S3 update semantics. There is no in-place byte edit: putting the same key replaces the current object, or creates a new version when versioning is enabled.

```python
import boto3
import json

s3 = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "s3", endpoint_url="http://localhost:4566"
)
bucket, key = "course-data", "incoming/orders/1001.json"

before = s3.head_object(Bucket=bucket, Key=key)
updated = json.dumps({"order_id": 1001, "status": "validated"}).encode("utf-8")
s3.put_object(
    Bucket=bucket,
    Key=key,
    Body=updated,
    ContentType="application/json",
    Metadata={"producer": "aws-master-class", "schema-version": "2"},
)
after = s3.head_object(Bucket=bucket, Key=key)
print(before.get("ETag"), after.get("ETag"), after.get("VersionId"))
```

Replacing an object also replaces its user metadata unless it is supplied again. For concurrency control, record the current ETag/version and use conditional operations where supported, or write immutable keys and atomically update a small manifest. Enable versioning before the exercise if rollback/history is required.


