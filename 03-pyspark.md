[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 03: PySpark and S3

## Goal

Validate local Spark and understand the extra connector required for `s3a://` paths.

## Basic validation

With the virtual environment active:

```bash
python -c "from pyspark.sql import SparkSession; s=SparkSession.builder.master('local[*]').appName('aws-master-class').getOrCreate(); print(s.version); s.stop()"
```

PySpark's pip package does not automatically include every Hadoop cloud connector. Match `hadoop-aws` to the Hadoop version bundled with your Spark distribution. A typical session is:

```python
from pyspark.sql import SparkSession

spark = (
    SparkSession.builder
    .master("local[*]")
    .appName("floci-s3")
    .config("spark.hadoop.fs.s3a.endpoint", "http://localhost:4566")
    .config("spark.hadoop.fs.s3a.path.style.access", "true")
    .config("spark.hadoop.fs.s3a.connection.ssl.enabled", "false")
    .config("spark.hadoop.fs.s3a.access.key", "test")
    .config("spark.hadoop.fs.s3a.secret.key", "test")
    .getOrCreate()
)
```

Add the compatible package at submit time, for example `--packages org.apache.hadoop:hadoop-aws:<matching-version>`. Determine the bundled Hadoop version from Spark before choosing it; mixing versions causes `ClassNotFoundException` or `NoSuchMethodError`.

Write and read a small dataset:

```python
spark.range(10).write.mode("overwrite").parquet("s3a://course-data/spark/numbers")
spark.read.parquet("s3a://course-data/spark/numbers").show()
```

Create `course-data` first with Task 07. In real AWS, remove the custom endpoint, avoid static keys, and let the default credential chain use an IAM role.


