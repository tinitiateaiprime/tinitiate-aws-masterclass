[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 17: S3 create, update, delete, and select events

## Event model

S3 notifications support object-created and object-removed event families. An “update” is another write to the same key and therefore appears as `ObjectCreated:*`; there is no distinct object-updated notification. S3 Select is a read operation and does not emit an object-created/removed notification.

Configure notifications to Lambda, SQS, SNS, or EventBridge. Use non-overlapping prefix/suffix filters when possible:

```python
import boto3

s3 = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "s3", endpoint_url="http://localhost:4566"
)
s3.put_bucket_notification_configuration(
    Bucket="course-data",
    NotificationConfiguration={
        "LambdaFunctionConfigurations": [{
            "Id": "json-created",
            "LambdaFunctionArn": "arn:aws:lambda:us-east-1:000000000000:function:course-s3-handler",
            "Events": ["s3:ObjectCreated:*"],
            "Filter": {"Key": {"FilterRules": [
                {"Name": "prefix", "Value": "incoming/"},
                {"Name": "suffix", "Value": ".json"},
            ]}},
        }],
        "EventBridgeConfiguration": {},
    },
)
```

`put_bucket_notification_configuration` replaces the bucket's complete notification configuration. Read and merge existing destinations before updating a shared bucket.

In AWS, grant S3 permission to invoke the Lambda before applying the notification. Test create, overwrite, and delete separately. URL-decode keys in event records. Delivery is asynchronous and at least once, so duplicates and out-of-order events are possible. Make handlers idempotent using bucket + key + version ID/sequencer.

To audit selects/reads, use CloudTrail data events or access logging instead of S3 notifications; these add cost and should be scoped carefully.


