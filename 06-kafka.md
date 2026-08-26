[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 06: Kafka locally

[⬅ Back](./README.md) | [🏠 Home](./README.md)

## Goal

Run a Kafka-compatible broker for event-driven exercises and prove producer/consumer connectivity before integrating Lambda or PySpark.

Floci can provide MSK-compatible behavior through a Docker-backed broker. Alternatively, add a Redpanda or Apache Kafka service to the same Compose network. Keep the external bootstrap address for host programs and the internal service address for containers; `localhost` inside a container points to that container itself.

## Python smoke test

Create a topic named `course-events`, then run a producer:

```python
from kafka import KafkaProducer
import json

producer = KafkaProducer(
    bootstrap_servers="localhost:9092",
    value_serializer=lambda value: json.dumps(value).encode("utf-8"),
)
producer.send("course-events", {"event": "course.started", "version": 1})
producer.flush()
```

Consume it:

```python
from kafka import KafkaConsumer
import json

consumer = KafkaConsumer(
    "course-events",
    bootstrap_servers="localhost:9092",
    group_id="course-reader",
    auto_offset_reset="earliest",
    value_deserializer=lambda value: json.loads(value.decode("utf-8")),
)
for message in consumer:
    print(message.offset, message.value)
```

## Operating rules

Use stable consumer group IDs, commit only after successful processing, make consumers idempotent, and route repeatedly failing records to a dead-letter topic. Monitor consumer lag and rebalance count in Grafana. For PySpark Structured Streaming, use a Spark Kafka connector matching the Spark/Scala version, and checkpoint to a durable location.


