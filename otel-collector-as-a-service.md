# Setting up Otel Collector as a Service (Proxy)

Instrumented Application --<Zipkin>--> SmartAgent(0.0.0.0:9080/v1/trace) --<Zipkin>--> OtelCollector(0.0.0.0:9411/v2/trace && 0.0.0.0:9943) --<<Zipkin>>--> Splunk

## 1. Install Splunk OpenTelemetry Collector as gateway. 
```
curl -sSL https://dl.signalfx.com/splunk-otel-collector.sh > /tmp/splunk-otel-collector.sh;
sudo sh /tmp/splunk-otel-collector.sh --realm us1 --mode gateway -- <YOUR_ACCESS_TOKEN>
```

Ref:
- Instalation Doc: https://github.com/signalfx/splunk-otel-collector/blob/main/docs/getting-started/linux-installer.md
- The available options can be found here: https://github.com/signalfx/splunk-otel-collector/blob/main/internal/buildscripts/packaging/installer/install.sh

## 2. Install Smart Agent 

```
curl -sSL https://dl.signalfx.com/signalfx-agent.sh > /tmp/signalfx-agent.sh;
sudo sh /tmp/signalfx-agent.sh --realm us1 -- <YOUR_ACCESS_TOKEN>

```
- Make sure that the agent is up running by checking it status 
- E.g. sudo signalfx-agent status

## 3. 
 
