# obs-otel-python
# Intro
This is an example how to add the otel collector layer and Python instrumentation layer to your Lambda function using Python.
This gves the following advantages:
 - the ability to send observability data to multiple different backends without having to re-instrument your code
 - autoinstrumentation for all metrics traces and logs from supported libraries. See OpenTelemetry Python Instrumentation Libraries
 - the ability to add pipelines, filter and transform metrics, logs and traces at the source
 - the ability to add context like tags, metadata and properties which you can use to apply multitenancy
 - the ability to choose a different backend or multiple backends.

 This example is written for **Dynatrace** as backend.
 
## Prerequisites using devbox
Devbox creates isolated, reproducible development environments that run anywhere.
   1. Please install vscode and the devbox extension: 
https://marketplace.visualstudio.com/items?itemName=jetpack-io.devbox
   2. Close Vscode.
   3. Install devbox on your operating system (Windows/Mac/Linux)  
See: https://www.jetify.com/docs/devbox/installing_devbox/
   4. Clone this repository: `git clone git@github.com:organization-or-user/thisrepo.git`
   5. Open Vscode and start a terminal. Devbox will start automatically and setup your dev environment.
Setup: use [![Built with Devbox](https://www.jetify.com/img/devbox/shield_galaxy.svg)](https://www.jetify.com/devbox/docs/contributor-quickstart/)


## Opentelemetry in the Dynatrace GUI
![Alt text](docs/images/service.png?raw=true "collector-python Dynatrace")

## Design
![Alt text](docs/images/collector-python-layer.png?raw=true "collector-python Dynatrace")

## How to deploy with terraform to lambda:
This is terraform but can be changed to CDK, SAM or whatever
```bash
# Clone the repo, set your user profile and use the Makefile to deploy the lambda
(.venv) user:~/otel-pythontest$ git clone git@github.com:organization-or-user/thisrepo.git
(.venv) user:~/otel-pythontest$  cd terraform
(.venv) user:~/otel-pythontest/terraform$ export AWS_PROFILE="yourprofile"
(.venv) user:~/otel-pythontest/terraform$ aws sts get-caller-identity
{
    "UserId": "userid",
    "Account": "accountid",
    "Arn": "arn:aws:sts::accountid:assumed-role/AWSReservedSSO_useraccountname"
}
(.venv) user:~/otel-pythontest/terraform$ $ make clean
(.venv) user:~/otel-pythontest/terraform$ $ make deploy
```
## AWS Parameter store
The terraform config uses config from the AWS parameter store in the following format:
```bash
key name: /observability/otel/tst/config
description: Opentelemetry demo for AWS Lambda/EKS with Dynatrace backend
```
```bash
{
"OTEL_EXPORTER_OTLP_ENDPOINT":"https://yourtenant.live.dynatrace.com/api/v2/otlp",
"OTEL_EXPORTER_OTLP_TOKEN":"yourtoken.yourkey",
"OTEL_EXPORTER_OTLP_METRICS_TEMPORALITY_PREFERENCE":"delta",
"OTEL_EXPORTER_OTLP_PROTOCOL":"http/protobuf",
"OTEL_PYTHON_LOGGING_AUTO_INSTRUMENTATION_ENABLED":"true",
"AWS_PROFILE":"yourprofile-optionally"
}
```


#### Opentelemetry layers: an alternative for collector-python
```bash
    # 1. OpenTelemetry Community Python Instrumentation Layer
    # Find the latest stable version for your Python runtime and region from:
    # https://github.com/open-telemetry/opentelemetry-lambda/releases
    "arn:aws:lambda:eu-central-1:184161586896:layer:opentelemetry-python-0_14_0:1", # Example ARN for Python 3.11
    # 2. OpenTelemetry Community Collector Layer
    # Find the latest stable version for your architecture and region from:
    # https://github.com/open-telemetry/opentelemetry-lambda/releases
    "arn:aws:lambda:eu-central-1:184161586896:layer:opentelemetry-collector-amd64-0_15_0:1", # Example ARN for amd64 architecture
```

# Links
- https://www.highlight.io/blog/the-complete-guide-to-python-and-opentelemetry

- https://aws.amazon.com/blogs/opensource/auto-instrumenting-a-python-application-with-an-aws-distro-for-opentelemetry-lambda-layer/
- https://catalog.workshops.aws/observability/en-US/aws-managed-oss/collector-python/lambda-tracing
- https://github.com/aws-observability/aws-otel-lambda/tree/main
- https://github.com/open-telemetry/opentelemetry-lambda/blob/main/python/src/otel/Makefile
- https://xebia.com/blog/automate-lambda-dependencies-with-terraform/
- https://github.com/open-telemetry/opentelemetry-lambda/blob/main/python/README.md
- https://github.com/open-telemetry/opentelemetry-lambda/blob/main/python/sample-apps/aws-sdk/deploy/wrapper/main.tf
- https://jessitron.com/2021/08/11/run-an-opentelemetry-collector-locally-in-docker/


# OpenTelemetry Python Instrumentation Libraries
The following is a categorized list of commonly available OpenTelemetry Python instrumentation libraries. This list helps you identify which libraries can automatically collect telemetry from your application's dependencies.

### Web Frameworks & ASGI/WSGI Servers

* `opentelemetry-instrumentation-asgi`: For ASGI (Asynchronous Server Gateway Interface) applications and frameworks (e.g., FastAPI, Starlette).
* `opentelemetry-instrumentation-django`: For Django web applications.
* `opentelemetry-instrumentation-falcon`: For Falcon web APIs.
* `opentelemetry-instrumentation-fastapi`: Specific instrumentation for FastAPI applications.
* `opentelemetry-instrumentation-flask`: For Flask web applications.
* `opentelemetry-instrumentation-pyramid`: For Pyramid web applications.
* `opentelemetry-instrumentation-starlette`: For Starlette web applications.
* `opentelemetry-instrumentation-tornado`: For Tornado web applications.
* `opentelemetry-instrumentation-wsgi`: For WSGI (Web Server Gateway Interface) applications.

### HTTP Clients

* `opentelemetry-instrumentation-httpx`: For the HTTPX asynchronous HTTP client.
* `opentelemetry-instrumentation-requests`: For the popular `requests` HTTP library.
* `opentelemetry-instrumentation-urllib`: For Python's standard `urllib` module.
* `opentelemetry-instrumentation-urllib3`: For the `urllib3` HTTP client.

### Database Clients & ORMs

* `opentelemetry-instrumentation-asyncpg`: For `asyncpg` PostgreSQL client.
* `opentelemetry-instrumentation-cassandra`: For the Apache Cassandra Python Driver.
* `opentelemetry-instrumentation-dbapi`: A generic instrumentation for Python Database API Specification v2.0 compliant drivers (e.g., Psycopg2, MySQL Connector/Python).
* `opentelemetry-instrumentation-mysql`: For MySQL client libraries.
* `opentelemetry-instrumentation-psycopg2`: For `psycopg2` PostgreSQL adapter.
* `opentelemetry-instrumentation-sqlalchemy`: For SQLAlchemy ORM and SQL toolkit.
* `opentelemetry-instrumentation-sqlite3`: For Python's built-in `sqlite3` module.

### Message Queues & Brokers

* `opentelemetry-instrumentation-celery`: For Celery distributed task queue.
* `opentelemetry-instrumentation-kafka`: For Apache Kafka clients.
* `opentelemetry-instrumentation-pika`: For `pika` (RabbitMQ) client library.
* `opentelemetry-instrumentation-redis`: For Redis client libraries (`redis-py`).
* `opentelemetry-instrumentation-sqs`: For AWS SQS (Simple Queue Service) via `boto3`.

### Asynchronous & Concurrency Libraries

* `opentelemetry-instrumentation-aiohttp-client`: For `aiohttp` HTTP client.
* `opentelemetry-instrumentation-aiohttp-server`: For `aiohttp` web server.
* `opentelemetry-instrumentation-asyncio`: For Python's `asyncio` framework.
* `opentelemetry-instrumentation-threading`: For Python's `threading` module.

### Cloud & AWS Services

* `opentelemetry-instrumentation-aws-lambda`: For AWS Lambda functions.
* `opentelemetry-instrumentation-boto3`: For the `boto3` AWS SDK.
* `opentelemetry-instrumentation-google-cloud-client`: For Google Cloud client libraries.

### Other Utilities & Libraries

* `opentelemetry-instrumentation-click`: For Click command-line interface creation.
* `opentelemetry-instrumentation-grpc`: For gRPC framework.
* `opentelemetry-instrumentation-logging`: Integrates with Python's standard `logging` module to inject trace context into logs.
* `opentelemetry-instrumentation-pyspark`: For Apache PySpark.
* `opentelemetry-instrumentation-system-metrics`: Collects system-level metrics (CPU, memory, disk I/O, etc.).
* `opentelemetry-instrumentation-jinja2`: For the Jinja2 templating engine.
* `opentelemetry-instrumentation-confluent-kafka`: For Confluent Kafka Python client.
* `opentelemetry-instrumentation-elasticsearch`: For Elasticsearch Python client.
* `opentelemetry-instrumentation-pymemcache`: For `pymemcache` client.
* `opentelemetry-instrumentation-tortoiseorm`: For Tortoise ORM.
