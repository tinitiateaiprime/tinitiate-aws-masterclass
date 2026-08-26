[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 16: S3 Tables

## Goal

Understand Amazon S3 Tables as managed tabular storage for Apache Iceberg, separate from ordinary general-purpose S3 buckets.

AWS's current [S3 Tables documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-tables.html) describes table buckets as purpose-built storage for Iceberg tables with automatic maintenance. Use the current guide for regional availability and integration versions.

Core hierarchy:

```text
table bucket -> namespace -> table -> Iceberg snapshots/data files
```

Use `boto3.client("s3tables")` in a supported AWS region to create/manage table buckets, namespaces, and tables. The exact SDK operation shapes evolve, so inspect the installed client model before coding:

```python
import boto3

client = boto3.client("s3tables", region_name="us-east-1")
print(client.meta.service_model.operation_names)
```

Then implement this lab sequence with the current SDK/API:

1. Create a course table bucket.
2. Create namespace `training`.
3. Create an Iceberg table such as `orders` with an explicit schema.
4. Configure Spark with the supported [Iceberg REST endpoint/catalog](https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-tables-access.html) for S3 Tables.
5. Insert rows, query them, update/delete records, and inspect snapshots.
6. Configure maintenance/retention and remove the lab resources.

Do not treat a table ARN as a normal `s3://bucket/key`. Iceberg writers commit metadata and snapshots; manually changing underlying files can corrupt table state. Check current regional availability, IAM actions, Spark/Iceberg integration versions, and Floci compatibility before the lab. If local emulation lacks S3 Tables, teach the same Iceberg concepts with a local catalog, then run this task in a sandbox AWS account.


