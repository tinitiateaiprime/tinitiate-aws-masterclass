[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 26: Lambda microservices, load, and tuning

## Goal

Build and tune a small request-driven service while protecting downstream systems.

A typical flow is:

```text
API Gateway -> Lambda -> DynamoDB
                     -> SQS -> worker Lambda -> S3/RDS
```

Keep handlers thin: validate input, authorize, call a domain service, and return a stable response. Use correlation IDs, structured logs, explicit errors, idempotency keys, timeouts, and least-privilege roles. Put slow/retriable work on SQS and configure visibility timeout longer than the Lambda timeout, partial batch failure handling, and a dead-letter queue.

## Caller/load exercise

```python
import boto3, concurrent.futures, json, time

client = boto3.Session(profile_name="floci", region_name="us-east-1").client(
    "lambda", endpoint_url="http://localhost:4566"
)

def invoke(number):
    started = time.perf_counter()
    response = client.invoke(
        FunctionName="course-orders-api",
        InvocationType="RequestResponse",
        Payload=json.dumps({"order_id": str(number)}).encode(),
    )
    response["Payload"].read()
    return response.get("FunctionError"), (time.perf_counter() - started) * 1000

with concurrent.futures.ThreadPoolExecutor(max_workers=10) as pool:
    results = list(pool.map(invoke, range(100)))

latencies = sorted(duration for _, duration in results)
print("errors", sum(error is not None for error, _ in results))
print("p50_ms", latencies[len(latencies) // 2])
print("p95_ms", latencies[int(len(latencies) * 0.95) - 1])
```

Increase load gradually; do not aim this script at a shared/production account without authorization. Record throughput, error/throttle rate, p50/p95/p99, cold starts, concurrency, memory, duration, queue age, and downstream capacity.

Tune one variable at a time: memory/CPU, package size/import work, connection reuse, reserved concurrency, provisioned concurrency for strict latency, batch size/window for event sources, and downstream client pools/timeouts. Reserved concurrency can protect RDS; excessive concurrency can overload it. Validate the result with a second run and compare cost per successful request, not latency alone.

Deploy/update via Task 19. Use aliases and versions for controlled rollout, with alarms and rollback criteria. Floci is valuable for function/flow testing, but AWS load characteristics, cold starts, IAM, networking, quotas, and billing must be validated in an isolated AWS test environment.


