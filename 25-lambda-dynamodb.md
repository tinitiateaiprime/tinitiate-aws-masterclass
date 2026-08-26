[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 25: Lambda and DynamoDB

## Goal

Create an idempotent order-write micro-operation using a conditional DynamoDB put.

```python
import boto3, os
from botocore.exceptions import ClientError

table = boto3.resource("dynamodb").Table(os.getenv("TABLE_NAME", "course-orders"))

def lambda_handler(event, context):
    item = {
        "order_id": str(event["order_id"]),
        "status": event.get("status", "created"),
        "request_id": context.aws_request_id,
    }
    try:
        table.put_item(
            Item=item,
            ConditionExpression="attribute_not_exists(order_id)",
        )
        return {"created": True, "order": item}
    except ClientError as error:
        if error.response["Error"]["Code"] == "ConditionalCheckFailedException":
            return {"created": False, "reason": "order already exists"}
        raise
```

Create `course-orders` with string partition key `order_id`, then package/deploy with Task 19 and set `TABLE_NAME`. For Floci Lambda containers, ensure the SDK resolves the local endpoint through `AWS_ENDPOINT_URL` or create the resource with an explicit endpoint during local-only testing.

The AWS execution role needs only relevant table actions/resources. Test a first call and an identical retry. Add a sort key only when access patterns require it; design keys to spread load. Use strongly consistent reads only when necessary. For multi-item invariants use transactions, and for downstream processing use DynamoDB Streams with idempotent consumers. Monitor throttles, consumed capacity, latency, stream iterator age, and Lambda errors.


