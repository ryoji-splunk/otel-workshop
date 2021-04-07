# Welcome to the OpenTelemetry Workshop - Java 

## Overview
### Learn to instrument a Java Spring app with OpenTelemetry. 
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
2. Add the import statement 

```
import io.opentelemetry.api.trace.Span;

```


3. Locate the following block of code from OwnerController.java
```
public String processCreationForm(@Valid Owner owner, BindingResult result) {

```
4. Add the following lines. A common need when instrumenting an application is to capture additional application-specific or business-specific information as additional attributes to an existing span from the automatic instrumentation. Grab the current span with Span.current() and use the setAttribute() methods:

```
Span span = Span.current();
span.setAttribute("first_name", owner.getFirstName());
span.setAttribute("last_name", owner.getLastName());

```


## Creating spans around methods
To capture a span corresponding to one of your methods, you can 

1. Add the @WithSpan annotation OR 

```

@WithSpan
public String createNewSpan2(Span parentspan) {
     Span span = Span.current();

```

2. Add the OTEL_INSTRUMENTATION_METHODS_INCLUDE environemnt variable 

```
OTEL_INSTRUMENTATION_METHODS_INCLUDE=my.package.MyClass1[method1,method2];my.package.MyClass2[method3]

```

## Creating spans manually with a Tracer 

Creating spans manually with a Tracer

If @WithSpan doesn't work for your specific use case, you can take advantage of underlying OpenTelemetry API which allows you to obtain a tracer that can be used to manually create spans and execute code within the scope of that span.

E.g. 

```
private static final String INSTRUMENTATION_NAME = OwnerController.class.getName();
private static final OpenTelemetry openTelemetry = OpenTelemetrySdkAutoConfiguration.initialize();
private static final Tracer tracer = openTelemetry.getTracer(INSTRUMENTATION_NAME);

...


Span span =
     tracer.spanBuilder("importantwork2").setParent(Context.current().with(parentspan)).startSpan();
     try {
		span.setAttribute("abc", "def"); // set our attribute
     }finally {
		span.end();
     }

```








Reference: https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/docs/manual-instrumentation.md





