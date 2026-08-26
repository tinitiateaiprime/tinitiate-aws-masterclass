[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 22: Manage Lambda timeout

## Goal

Choose a timeout from observed latency and make downstream calls finish early enough for cleanup/error handling.

Update configuration:

```bash
aws lambda update-function-configuration --function-name course-hello --timeout 15 --profile floci --endpoint-url http://localhost:4566
aws lambda get-function-configuration --function-name course-hello --query "{Timeout:Timeout,Memory:MemorySize}" --profile floci --endpoint-url http://localhost:4566
```

Use the remaining time in code:

```python
def lambda_handler(event, context):
    remaining_ms = context.get_remaining_time_in_millis()
    if remaining_ms < 2_000:
        raise TimeoutError("insufficient time to start work")
    # Configure HTTP/DB client connect and read timeouts below the remaining budget.
    return {"remaining_ms_at_start": remaining_ms}
```

Test with a controlled sleep both below and above the configured limit. A timeout terminates execution abruptly, so code after the deadline does not reliably run. Retried event sources can then deliver the same work again; handlers must be idempotent.

Do not use a very large Lambda timeout to hide slow architecture. Measure p95/p99 duration, cold-start contribution, downstream latency, and throttling. Increasing memory also increases CPU allocation and can reduce duration. For long-running, checkpointed, or multi-stage work, use Step Functions, queues, containers, or batch processing.


