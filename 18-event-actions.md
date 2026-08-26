[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 18: Event actions

## Goal

Route S3 events to the right AWS service and design for retries, duplicates, and failure isolation.

Common patterns:

- **S3 -> Lambda:** low-latency transformation or validation. Add a Lambda resource policy allowing the bucket ARN and account.
- **S3 -> SQS -> Lambda:** preferred when buffering, back-pressure, controlled concurrency, and redrive are needed.
- **S3 -> SNS:** fan-out to several subscribers.
- **S3 -> EventBridge:** content-based routing to Lambda, Step Functions, SQS, SNS, API destinations, and other targets.
- **S3 -> Glue/Athena workflow:** catalog/query-oriented data pipelines, often orchestrated through EventBridge and Step Functions.

Example Lambda permission:

```bash
aws lambda add-permission --function-name course-s3-handler --statement-id allow-course-data --action lambda:InvokeFunction --principal s3.amazonaws.com --source-arn arn:aws:s3:::course-data --profile floci --endpoint-url http://localhost:4566
```

Then apply the notification from Task 17. Prevent recursive loops: if a handler writes results back to the same bucket, use an `incoming/` trigger and write to `processed/`, or use a separate destination bucket.

For reliable processing, define a dead-letter/redrive path, alarm on failures and queue age, reserve appropriate concurrency, log an idempotency key, and test duplicate delivery. Event payloads identify the object; fetch the object from S3 only after validating bucket/key and accounting for deletion or a newer version.


