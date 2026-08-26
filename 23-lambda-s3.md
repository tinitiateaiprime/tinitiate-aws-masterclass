[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 23: Lambda and S3

## Goal

Deploy a Lambda that is invoked by an object-created notification, reads JSON safely, and writes a result without creating a trigger loop.

```python
import boto3, json
from urllib.parse import unquote_plus

s3 = boto3.client("s3")

def lambda_handler(event, context):
    processed = []
    for record in event.get("Records", []):
        bucket = record["s3"]["bucket"]["name"]
        key = unquote_plus(record["s3"]["object"]["key"])
        version = record["s3"]["object"].get("versionId")
        params = {"Bucket": bucket, "Key": key}
        if version:
            params["VersionId"] = version
        source = s3.get_object(**params)
        document = json.loads(source["Body"].read())
        output_key = key.replace("incoming/", "processed/", 1)
        s3.put_object(Bucket=bucket, Key=output_key, Body=json.dumps(document).encode())
        processed.append(output_key)
    return {"processed": processed}
```

When deploying to Floci, set `AWS_ENDPOINT_URL=http://floci:4566` for the Lambda container if the runtime does not inject the emulator endpoint. Package and deploy with Task 19, add S3 invoke permission, and configure only the `incoming/` prefix using Tasks 17–18.

Test by uploading `incoming/order.json`, then verify `processed/order.json`. The function role needs only `s3:GetObject` on the input prefix, `s3:PutObject` on the output prefix, and log permissions. Handle duplicate notifications, missing/deleted objects, malformed JSON, and files too large to read entirely into memory. Use SQS between S3 and Lambda when buffering/redrive are important.


