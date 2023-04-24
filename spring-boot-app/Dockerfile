FROM curlimages/curl:7.81.0 AS download
ARG OTEL_AGENT_VERSION="1.25.0"
RUN curl --silent --fail -L "https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/download/v${OTEL_AGENT_VERSION}/opentelemetry-javaagent.jar" \
    -o "$HOME/opentelemetry-javaagent.jar"

FROM maven:eclipse-temurin AS build
ADD . /build
RUN cd /build && mvn package --quiet

FROM eclipse-temurin:8-jdk-focal
# Healthcheck is done by Kubernetes probes
HEALTHCHECK NONE
COPY --from=build /build/target/*.jar /app.jar
COPY --from=download /home/curl_user/opentelemetry-javaagent.jar /opentelemetry-javaagent.jar
ENV TZ=Asia/Taipei
ENTRYPOINT ["java", \
  "-javaagent:/opentelemetry-javaagent.jar", \
  "-jar", "/app.jar" \
  ]