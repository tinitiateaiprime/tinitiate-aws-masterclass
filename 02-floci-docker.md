[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 02: Run Floci with Docker

[⬅ Back](./README.md) | [🏠 Home](./README.md)

## Goal

Run a local AWS-compatible endpoint without using a real AWS account. Floci exposes AWS services on port `4566`; Docker-backed services such as Lambda and Kafka also require access to the Docker daemon.

## Start Floci

The simplest foreground command is:

```bash
docker run --rm --name floci -p 4566:4566 -v /var/run/docker.sock:/var/run/docker.sock -u root floci/floci:latest
```

On Windows, Floci CLI can resolve Docker's named pipe. If the socket mount does not work from your shell, prefer the Floci CLI or a Floci Compose file designed for Windows.

For a reusable stack, create a Compose service with image `floci/floci:latest`, port `4566:4566`, `FLOCI_DEFAULT_REGION=us-east-1`, and the Docker socket mount. When applications run in sibling containers, set:

```yaml
environment:
  FLOCI_HOSTNAME: floci
  FLOCI_BASE_URL: http://floci:4566
```

and configure those applications with `AWS_ENDPOINT_URL=http://floci:4566`.

## Verify

```bash
docker ps
aws sts get-caller-identity --profile floci --endpoint-url http://localhost:4566
aws s3api list-buckets --profile floci --endpoint-url http://localhost:4566
```

Inspect failures with `docker logs floci`. A connection-refused error means the container is not ready or port `4566` is occupied. A Lambda container error usually means Floci cannot reach the Docker daemon.

## Persistence and cleanup

Floci defaults may be ephemeral. For exercises that must survive restarts, use its persistent storage mode and mount a course-specific data directory. Stop with `docker stop floci`. Remove only the named course container/volume; never run a broad Docker prune on a shared workstation.


