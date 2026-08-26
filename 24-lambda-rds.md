[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 24: Lambda and RDS

## Goal

Call a relational database safely from Lambda and understand networking, credentials, connection reuse, and scaling.

Example PostgreSQL handler:

```python
import os, psycopg2

connection = None

def get_connection():
    global connection
    if connection is None or connection.closed:
        connection = psycopg2.connect(
            host=os.environ["DB_HOST"],
            port=int(os.getenv("DB_PORT", "5432")),
            dbname=os.environ["DB_NAME"],
            user=os.environ["DB_USER"],
            password=os.environ["DB_PASSWORD"],
            connect_timeout=3,
        )
    return connection

def lambda_handler(event, context):
    with get_connection().cursor() as cursor:
        cursor.execute("SELECT order_id, status FROM orders WHERE order_id = %s", (event["order_id"],))
        row = cursor.fetchone()
    return {"order": row}
```

Package `psycopg2` for Lambda's Linux runtime as described in Task 20. Deploy with Task 19 and supply configuration using environment variables for local labs. For AWS, store credentials in Secrets Manager and grant only `GetSecretValue`; never commit or log passwords.

In AWS, place Lambda in subnets/security groups that can reach the private RDS endpoint. VPC attachment does not automatically grant internet access. Prefer RDS Proxy for bursty/concurrent Lambda workloads, reuse connections across warm invocations, set short connection/query timeouts, use parameterized SQL and transactions, and cap concurrency so Lambda cannot exhaust database connections.

Invoke with `{"order_id": 1001}` using the caller from Task 19. Monitor connection count, query latency, locks, Lambda errors, duration, and proxy metrics.


