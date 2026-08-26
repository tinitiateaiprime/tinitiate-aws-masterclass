# AWS Master Class

This course builds AWS applications locally with Floci, Docker, Python, boto3, AWS CLI, PySpark, Kafka, and Grafana. It then applies the same patterns to AWS with a real profile. Complete the guides in order; every operation has its own Markdown task.

## Safety and conventions

- Local endpoint: `http://localhost:4566`
- Local region: `us-east-1`
- Local profile: `floci`
- Local credentials are dummy values. Never commit real keys or the `.aws` directory.
- Bucket names cannot be renamed by S3. The rename exercise implements copy, verify, and delete.
- S3 `SelectObjectContent` support varies by emulator/version. The guide includes a fallback.
- Grafana is a visualization layer; for AWS metrics it normally reads CloudWatch rather than replacing it. Locally, use Prometheus/Loki as the data source.
- Start with least-privilege IAM in AWS. Do not reuse the permissive local examples in production.

Use the onboarding installers from [tinitiate-onboarding-installer](https://github.com/tinitiateprime/tinitiate-onboarding-installer) for prerequisites. Confirm the installed versions with the commands in Task 01.

## Module 1: local setup

- [Local environment and installer checks](01-local-environment.md)
- [Floci with Docker](02-floci-docker.md)
- [PySpark setup and S3 access](03-pyspark.md)
- [Grafana local observability](04-grafana.md)
- [`.aws` folder and Floci profile](05-aws-profile.md)
- [Kafka locally](06-kafka.md)

## Module 2: Amazon S3

- [Create a bucket](07-s3-create-bucket.md)
- [Rename a bucket](08-s3-rename-bucket.md)
- [Delete a bucket](09-s3-delete-bucket.md)
- [List all buckets](10-s3-list-buckets.md)
- [List folders and files](11-s3-list-objects.md)
- [Create an object](12-s3-create-object.md)
- [Update an object](13-s3-update-object.md)
- [Delete an object](14-s3-delete-object.md)
- [Select object content](15-s3-select-object.md)
- [S3 Tables](16-s3-tables.md)
- [S3 create/update/delete/select events](17-s3-events.md)
- [Event actions: Lambda and other AWS services](18-event-actions.md)

## Module 3: AWS Lambda

- [Simple Lambda: create, deploy, and invoke](19-lambda-simple.md)
- [Lambda with libraries and Python modules](20-lambda-dependencies.md)
- [Monitor logs, output, and duration](21-lambda-monitoring.md)
- [Manage Lambda timeout](22-lambda-timeout.md)
- [Lambda and S3](23-lambda-s3.md)
- [Lambda and RDS](24-lambda-rds.md)
- [Lambda and DynamoDB](25-lambda-dynamodb.md)
- [Lambda microservices, load, and tuning](26-lambda-microservices.md)

## Suggested learning workflow

For each task: read the goal, run the local commands, verify the expected result, intentionally trigger one failure, and clean up only the resources created by that task. Add `--profile floci --endpoint-url http://localhost:4566` to local AWS CLI commands. boto3 examples centralize the same values in a client factory.

