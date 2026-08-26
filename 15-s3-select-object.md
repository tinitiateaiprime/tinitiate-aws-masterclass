[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 15: Select content from an S3 object

## Goal

Use S3 Select to return matching records from CSV/JSON without downloading the entire object. Emulator and AWS support can vary, so confirm the target environment.

> **Availability:** [AWS states that S3 Select is no longer available to new customers](https://docs.aws.amazon.com/AmazonS3/latest/userguide/selecting-content-from-objects.html). Existing S3 Select customers can continue to use it. For a new account, use this example only where the emulator supports it and teach Athena or S3 Tables as the AWS alternative.

```python
import boto3

s3 = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "s3", endpoint_url="http://localhost:4566"
)
response = s3.select_object_content(
    Bucket="course-data",
    Key="sales/orders.csv",
    ExpressionType="SQL",
    Expression="SELECT s.order_id, s.total FROM S3Object s WHERE CAST(s.total AS DECIMAL) > 100",
    InputSerialization={"CSV": {"FileHeaderInfo": "USE"}},
    OutputSerialization={"JSON": {"RecordDelimiter": "\n"}},
)

for event in response["Payload"]:
    if "Records" in event:
        print(event["Records"]["Payload"].decode("utf-8"), end="")
    elif "Stats" in event:
        print("stats", event["Stats"]["Details"])
```

The response is an event stream; read all `Records` chunks and handle `Stats`/`End`. Quote unusual column names and validate casts against dirty data. If `SelectObjectContent` is unavailable, download/stream the object and filter locally, or query a registered dataset with Athena/S3 Tables. “Select” is a read and does not produce an S3 object-created event.


