[⬅ Back](./README.md) | [🏠 Home](./README.md)

# Task 20: Lambda with libraries and Python modules

## Goal

Package a handler, project modules, and third-party dependencies in a deployment ZIP.

Example layout before packaging:

```text
build/
  lambda_function.py
  course/
    __init__.py
    validation.py
  requests/
  ...dependency files...
```

`course/validation.py`:

```python
def require_name(event):
    name = str(event.get("name", "")).strip()
    if not name:
        raise ValueError("name is required")
    return name
```

`lambda_function.py`:

```python
import requests
from course.validation import require_name

def lambda_handler(event, context):
    return {"name": require_name(event), "requests_version": requests.__version__}
```

Build from a clean directory:

```bash
python -m pip install --target build requests
```

Copy the handler and `course` package into `build`, then zip the **contents** of `build`. On PowerShell, run `Compress-Archive -Path build\* -DestinationPath function.zip -Force`. Deploy with the `create-function` command from Task 19 or update with:

```bash
aws lambda update-function-code --function-name course-dependencies --zip-file fileb://function.zip --profile floci --endpoint-url http://localhost:4566
```

Invoke with the boto3 caller from Task 19, changing `FunctionName` and payload.

Dependencies with native extensions must be built for Lambda's Linux OS, CPU architecture, and Python runtime. Build inside the matching Lambda container image or CI environment—not directly on Windows/macOS. Keep deployment artifacts deterministic with pinned versions and hashes. Use a Lambda layer for genuinely shared, independently versioned packages; use a container image when the ZIP/uncompressed size or native runtime needs make ZIP packaging awkward.


