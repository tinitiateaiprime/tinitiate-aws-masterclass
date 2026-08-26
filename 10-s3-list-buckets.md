[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 10: List all S3 buckets
[⬅ Back](./README.md) | [🏠 Home](./README.md)
## Goal

List buckets visible to the current identity and capture both their names and creation times.

```python
import boto3

s3 = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "s3", endpoint_url="http://localhost:4566"
)
response = s3.list_buckets()
for bucket in sorted(response.get("Buckets", []), key=lambda value: value["Name"]):
    print(bucket["Name"], bucket["CreationDate"].isoformat())
```

The response includes only buckets the identity may list. A bucket can still be accessible by an explicit name even when broad list permission is denied. Listing buckets does not list their contents.

CLI equivalent:

```bash
aws s3api list-buckets --query "Buckets[].{Name:Name,Created:CreationDate}" --output table --profile floci --endpoint-url http://localhost:4566
```

In production inventory jobs, enrich results with region, tags, encryption, versioning, public-access block, lifecycle, and logging configuration. Handle access-denied per bucket instead of aborting the entire inventory.


