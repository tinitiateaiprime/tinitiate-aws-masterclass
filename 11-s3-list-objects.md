[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 11: List folders and files in a bucket

## Goal

Understand that S3 is a flat key-value store. “Folders” are key prefixes, usually separated by `/`.

List all objects recursively with pagination:

```python
import boto3

s3 = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "s3", endpoint_url="http://localhost:4566"
)
for page in s3.get_paginator("list_objects_v2").paginate(Bucket="course-data"):
    for item in page.get("Contents", []):
        print(item["Key"], item["Size"], item["LastModified"].isoformat())
```

List one logical directory level:

```python
response = s3.list_objects_v2(
    Bucket="course-data", Prefix="incoming/", Delimiter="/"
)
folders = [entry["Prefix"] for entry in response.get("CommonPrefixes", [])]
files = [entry["Key"] for entry in response.get("Contents", [])]
print("folders", folders)
print("files", files)
```

Always paginate for an unknown object count. `list_objects_v2` returns at most one page per call. A zero-byte object ending in `/` may act as a folder marker but is not required. Prefix matching is case-sensitive.


