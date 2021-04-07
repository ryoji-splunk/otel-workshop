# Welcome to the OpenTelemetry Workshop - Python 

## Overview

### Learn to instrument a Python flask app with OpenTelemetry. 

Sending spans directly to the Splunk APM backend. 
```
SPLUNK_SERVICE_NAME=myapp OTEL_EXPORTER_JAEGER_ENDPOINT=https://ingest.us1.signalfx.com/v2/trace SPLUNK_ACCESS_TOKEN=<deducted>
splk-py-trace python main.py

```

1. Intall the packages
```
pip install splunk-opentelemetry
```

2.  This commands inspects the active Python site-packages and figures out which instrumentation packages the user might want to install. By default it prints out a list of the suggested instrumentation packages which can be added to a requirements.txt file. It also supports installing the suggested packages when run with --action=install flag. Ref: https://pypi.org/project/opentelemetry-instrumentation/

```
splk-py-trace-bootstrap
```

3. 

Reference: https://github.com/signalfx/splunk-otel-python, https://opentelemetry-python.readthedocs.io/en/latest/examples/auto-instrumentation/README.html
