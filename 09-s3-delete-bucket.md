[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 09: Delete an S3 bucket

## Goal and warning

Delete only the explicitly named training bucket. S3 refuses to delete a non-empty bucket. Versioned buckets also contain versions and delete markers that `list_objects_v2` does not show.

For the unversioned local exercise:

```python
import boto3

s3 = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "s3", endpoint_url="http://localhost:4566"
)
bucket = "course-delete-me"

paginator = s3.get_paginator("list_objects_v2")
for page in paginator.paginate(Bucket=bucket):
    objects = [{"Key": item["Key"]} for item in page.get("Contents", [])]
    if objects:
        s3.delete_objects(Bucket=bucket, Delete={"Objects": objects, "Quiet": True})

s3.delete_bucket(Bucket=bucket)
print(f"deleted {bucket}")
```

For versioned buckets, paginate `list_object_versions` and delete both `Versions` and `DeleteMarkers` using their `VersionId`. Object Lock retention or legal holds can prevent deletion. Never generalize this script to delete every listed bucket. Require an allow-listed name/prefix and confirm the active account with STS in real AWS.

Verify that `head_bucket` now returns `404`/`NoSuchBucket`.


