[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 04: Grafana local observability

[⬅ Back](./README.md) | [🏠 Home](./README.md)

## Goal

Build dashboards for the local stack. Grafana is not a CloudWatch replacement by itself: it queries a metrics/log data source such as Prometheus and Loki locally, or CloudWatch in AWS.

## Local architecture

Use this flow:

```text
Floci/application metrics -> Prometheus -> Grafana dashboard
Floci/application logs    -> Loki       -> Grafana Explore
```

Run Grafana in Docker:

```bash
docker run -d --name course-grafana -p 3000:3000 grafana/grafana:latest
```

Open `http://localhost:3000`, change the initial administrator password, and add the course Prometheus/Loki services as data sources. When Grafana is also in Docker, use Compose service names rather than `localhost` for sibling containers.

## Dashboard exercise

Create panels for:

- request count by service and operation;
- error count/rate;
- p50, p95, and p99 duration;
- Lambda invocation count and timeout count;
- Kafka consumer lag;
- container CPU and memory.

Add an alert for sustained error rate and annotate load-test start/end times. Keep service name, operation, status, and environment as bounded labels; never place request IDs or object keys in metric labels because their high cardinality increases cost and memory.

## AWS comparison

In AWS, CloudWatch receives native Lambda metrics and logs. Grafana can query CloudWatch, but CloudWatch still stores and controls that telemetry. Verify both the source data and the dashboard query when a panel is empty.


