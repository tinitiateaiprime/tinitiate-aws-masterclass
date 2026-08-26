[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 08: Rename an S3 bucket

## Important constraint

S3 has no rename-bucket API. “Rename” means create a destination bucket, copy every object version and relevant configuration, verify the copy, redirect clients, then delete the source later.

For a small, unversioned training bucket:

```python
import boto3

s3 = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "s3", endpoint_url="http://localhost:4566"
)
source, destination = "course-data", "course-data-renamed"
s3.create_bucket(Bucket=destination)

paginator = s3.get_paginator("list_objects_v2")
copied = 0
for page in paginator.paginate(Bucket=source):
    for item in page.get("Contents", []):
        s3.copy_object(
            Bucket=destination,
            Key=item["Key"],
            CopySource={"Bucket": source, "Key": item["Key"]},
            MetadataDirective="COPY",
        )
        copied += 1

source_count = sum(
    len(page.get("Contents", [])) for page in paginator.paginate(Bucket=source)
)
destination_count = sum(
    len(page.get("Contents", [])) for page in paginator.paginate(Bucket=destination)
)
assert source_count == destination_count == copied
print(f"verified {copied} objects; source was not deleted")
```

Object counts alone are not enough for a real migration. Compare keys, sizes, checksums where available, metadata, tags, encryption, retention, policies, CORS, lifecycle, notification configuration, version history, and access logs. Update producers/consumers and observe them before cleanup. Use S3 Batch Operations or replication for large/versioned buckets. Task 09 handles explicit deletion.


