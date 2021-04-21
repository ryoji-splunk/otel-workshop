# Setting up Otel Collector as a Service (Proxy)
### Agenda: Send both traces and host metrics to Splunk via Otel Collector. 


**Instrumented Application-->SmartAgent(0.0.0.0:9080/v1/trace)-->OtelCollector(0.0.0.0:9411/v2/trace && 0.0.0.0:9943)-->Splunk**

## 1. Install Splunk OpenTelemetry Collector as gateway. 

By default, Splunk OpenTelemetry Collector gets installed as the agent_config.yaml is used.  

```
curl -sSL https://dl.signalfx.com/splunk-otel-collector.sh > /tmp/splunk-otel-collector.sh;
sudo sh /tmp/splunk-otel-collector.sh --realm us1 --mode gateway -- <YOUR_ACCESS_TOKEN>
```

You should be able to see the following message as the installation script gets executed.

```
The Splunk OpenTelemetry Connector for Linux has been successfully installed.

Make sure that your system's time is relatively accurate or else datapoints may not be accepted.

The collector's main configuration file is located at /etc/otel/collector/gateway_config.yaml,
and the environment file is located at /etc/otel/collector/splunk-otel-collector.conf.

```

Make sure that the smart agent can connect to the newly configured otel collector. 

E.g. Do telnet from the host where you are going to install SmartAgent on. 
```
telent <OpenTelemetryCollectorURL> 9943

telent <OpenTelemetryCollectorURL> 9411
```

Ref:
- Instalation Doc: https://github.com/signalfx/splunk-otel-collector/blob/main/docs/getting-started/linux-installer.md
- The available options can be found here: https://github.com/signalfx/splunk-otel-collector/blob/main/internal/buildscripts/packaging/installer/install.sh
- SignalfxReceiver (with TLS option): https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/signalfxreceiver

## 2. Install Smart Agent 

```
curl -sSL https://dl.signalfx.com/signalfx-agent.sh > /tmp/signalfx-agent.sh;
sudo sh /tmp/signalfx-agent.sh --realm us1 -- <YOUR_ACCESS_TOKEN>

```
- To verify that you've successfully installed the Smart Agent, copy and paste the following command into your terminal.
```
sudo signalfx-agent status
```

## 3. Make the following changes to the files 

1. /etc/signalfx/ingest_url
```
http://<OpenTelemetryCollectorURL>:9943
```
2. /etc/signalfx/trace_endpoint_url
```
http://<OpenTelemetryCollectorURL>:9411/v2/trace
```
3. (Optional) /etc/signalfx/agent.yaml
Add the environment tag 
```
  # If using SignalFx auto instrumentation with default settings
  - type: signalfx-forwarder
    listenAddress: 0.0.0.0:9080
    # Used to add a tag to spans missing it
    defaultSpanTags:
     # Set the environment filter in SignalFx
     environment: "ryo_otel"
    # Used to add and override a tag on a span
    #extraSpanTags:
     #SPAN_TAG_KEY: "SPAN_TAG_VALUE"
```

