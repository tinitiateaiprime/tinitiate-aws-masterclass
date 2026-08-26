[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 19: Simple Lambda: create, deploy, and invoke

## Handler

Create a working folder containing `lambda_function.py`:

```python
import json

def lambda_handler(event, context):
    return {
        "statusCode": 200,
        "body": json.dumps({
            "message": "hello from Lambda",
            "request_id": context.aws_request_id,
            "input": event,
        }),
    }
```

Zip the file at the archive root (not inside another directory):

```powershell
Compress-Archive -Path lambda_function.py -DestinationPath function.zip -Force
```

## Deploy with AWS CLI

Create a local role placeholder, then deploy. Floci may accept the role ARN without a full IAM trust setup; real AWS requires a role trusted by `lambda.amazonaws.com` with basic logging permission.

```bash
aws lambda create-function --function-name course-hello --runtime python3.12 --handler lambda_function.lambda_handler --zip-file fileb://function.zip --role arn:aws:iam::000000000000:role/course-lambda-role --timeout 10 --memory-size 128 --profile floci --endpoint-url http://localhost:4566
```

For later changes:

```bash
aws lambda update-function-code --function-name course-hello --zip-file fileb://function.zip --profile floci --endpoint-url http://localhost:4566
```

## Run a caller

```python
import boto3, json

client = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "lambda", endpoint_url="http://localhost:4566"
)
response = client.invoke(
    FunctionName="course-hello",
    InvocationType="RequestResponse",
    Payload=json.dumps({"name": "student"}).encode(),
)
print(response["StatusCode"])
print(json.loads(response["Payload"].read()))
```

Check `FunctionError` as well as the payload. `RequestResponse` is synchronous; `Event` is asynchronous and returns before the handler completes.


