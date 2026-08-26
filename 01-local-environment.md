[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 01: Local environment and installer checks

[⬅ Back](./README.md) | [🏠 Home](./README.md)

## Goal

Prepare a reproducible workstation for all later exercises. Use the scripts in [tinitiate-onboarding-installer](https://github.com/tinitiateprime/tinitiate-onboarding-installer) instead of maintaining duplicate installers here.

## Required software

- Git
- Docker Desktop or Docker Engine with Compose v2
- Python 3.11 or later and `venv`
- AWS CLI v2
- Java 17 for PySpark
- Optional: a code editor and `make`

Clone and follow the operating-system instructions in the installer repository:

```bash
git clone https://github.com/tinitiateprime/tinitiate-onboarding-installer.git
cd tinitiate-onboarding-installer
```

Return to this repository after installation. Do not copy installer binaries into this project.

## Verify

```bash
git --version
docker version
docker compose version
python --version
aws --version
java -version
```

Create an isolated Python environment:

```bash
python -m venv .venv
```

Activate it on PowerShell with `.\.venv\Scripts\Activate.ps1`, or on Bash with `source .venv/bin/activate`, then install the course libraries:

```bash
python -m pip install --upgrade pip
python -m pip install boto3 pyspark kafka-python psycopg2-binary requests
```

Record versions for troubleshooting:

```bash
python -m pip freeze
docker info
```

## Completion check

Docker must be running, `aws --version` must succeed, and `python -c "import boto3, pyspark"` must exit without an error. If Java is not found, set `JAVA_HOME` to the JDK installation and reopen the terminal.


