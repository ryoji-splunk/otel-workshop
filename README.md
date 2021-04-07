# Welcome to the OpenTelemetry Workshop 

## Overview
### 1. Learn to instrument a Java Spring app with OpenTelemetry. 
Spring Petclinic App: https://github.com/spring-projects/spring-petclinic

Understanding the Spring Petclinic application with a few diagrams: 
https://speakerdeck.com/michaelisvy/spring-petclinic-sample-application?slide=3


### 2. Learn to instrument a Python flask app with OpenTelemetry. 




## Auto Instrument Sprint Petclinic App 

0. Install SmartAgent (optional) 
1. Follow the steps described here (https://github.com/spring-projects/spring-petclinic) and run the app. 
2. Visit http://localhost:8080 in your browser.
3. Download the Splunk Otel Distro JVM agent https://github.com/signalfx/splunk-otel-java
```
curl -L https://github.com/signalfx/splunk-otel-java/releases/latest/download/splunk-otel-javaagent-all.jar \
     -o splunk-otel-javaagent.jar
```
4. Review config options https://github.com/signalfx/splunk-otel-java/blob/main/docs/advanced-config.md

```
export OTEL_EXPORTER_JAEGER_ENDPOINT= <The Jaeger endpoint to connect to, default to http://localhost:9080/v1/trace> 
export OTEL_TRACES_EXPORTER= <Select the span exporter to use, default to jaeger-thrift-splunk>

```

5. If you prefer not to run the app from Maven directly using the Spring Boot Maven plugin so that it will pick up changes that you make in the project immediately, add the folloiwng jvmArguments option. This lets you you add the instrumentation agent in your run time. 

```
 <jvmArguments>
   ${spring-boot.run.jvmArguments}
 </jvmArguments>
```

6. Run the app with the runtime parameters below

```
./mvnw spring-boot:run -Dotel.javaagent.debug=true -Dspring-boot.run.jvmArguments="-javaagent:./splunk-otel-javaagent.jar" 
```

## Getting Deeper Insights into the App by Manually Instrumenting Sprint Petclinic App 
Review the latest version of all the dependencies from Maven Repo 
https://mvnrepository.com/artifact/io.opentelemetry


1. You'll need to add a dependency on the opentelemetry-api library to get started; if you intend to use the @WithSpan annotation, also include the opentelemetry-extension-annotations dependency.

```
  <dependencies>
    <dependency>
      <groupId>io.opentelemetry</groupId>
      <artifactId>opentelemetry-api</artifactId>
      <version>1.0.0</version>
    </dependency>
    <dependency>
      <groupId>io.opentelemetry</groupId>
      <artifactId>opentelemetry-extension-annotations</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>


```


2. 


Reference: https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/docs/manual-instrumentation.md





