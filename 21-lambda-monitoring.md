[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 21: Monitor output, logs, and duration

## Goal

Observe success/failure, application logs, billed duration, memory, concurrency, throttles, and cold starts.

Python logging:

```python
import json, logging, time

logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    started = time.perf_counter()
    logger.info(json.dumps({
        "event": "request_started",
        "request_id": context.aws_request_id,
    }))
    result = {"ok": True}
    logger.info(json.dumps({
        "event": "request_completed",
        "elapsed_ms": round((time.perf_counter() - started) * 1000, 2),
    }))
    return result
```

Tail AWS logs after invocation:

```bash
aws logs tail /aws/lambda/course-hello --follow --since 10m
```

For Floci, use its logs API if implemented; otherwise inspect `docker logs floci` and any spawned Lambda container logs. The invoke response can include the last 4 KB of logs when `LogType="Tail"` is requested synchronously:

```python
import base64
response = client.invoke(FunctionName="course-hello", LogType="Tail", Payload=b"{}")
print(base64.b64decode(response.get("LogResult", "")).decode())
```

Build dashboards/alarms for `Errors`, `Duration` percentiles, `Throttles`, `ConcurrentExecutions`, iterator age/queue age, and dead-letter failures. Avoid logging secrets or entire untrusted events. Use structured JSON fields so logs can be queried reliably. Set a finite log retention period in AWS.


