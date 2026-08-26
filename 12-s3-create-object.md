[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 12: Create an S3 object
[⬅ Back](./README.md) | [🏠 Home](./README.md)
## Goal

Upload bytes and attach useful metadata/content type.

```python
import boto3
import json

s3 = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "s3", endpoint_url="http://localhost:4566"
)
body = json.dumps({"order_id": 1001, "status": "created"}).encode("utf-8")
response = s3.put_object(
    Bucket="course-data",
    Key="incoming/orders/1001.json",
    Body=body,
    ContentType="application/json",
    Metadata={"producer": "aws-master-class", "schema-version": "1"},
)
print(response.get("ETag"))
```

Verify without downloading the body:

```python
head = s3.head_object(Bucket="course-data", Key="incoming/orders/1001.json")
print(head["ContentLength"], head["ContentType"], head["Metadata"])
```

`put_object` is suitable for small objects; use managed multipart upload (`upload_file`) for large files. In AWS, rely on bucket-default encryption or specify the approved KMS key, and avoid sensitive values in user metadata because metadata is not independently encrypted or redacted from administrative views.


