FROM maven:3.9-eclipse-temurin-21-alpine AS build
WORKDIR /app
COPY pom.xml .
RUN --mount=type=cache,target=/root/.m2 mvn -B -q dependency:go-offline
COPY src ./src
RUN --mount=type=cache,target=/root/.m2 mvn -B -q package -DskipTests

RUN mkdir /tmp/app \
    && cd /tmp/app \
    && unzip -q /app/target/app.jar \
    && jdeps \
        --ignore-missing-deps \
        -q \
        --recursive \
        --multi-release 21 \
        --print-module-deps \
        --class-path 'BOOT-INF/lib/*' \
        /app/target/app.jar \
        > /tmp/modules.txt \
    && jlink \
        --add-modules "$(cat /tmp/modules.txt)" \
        --strip-debug \
        --no-man-pages \
        --no-header-files \
        --compress=zip-6 \
        --output /jre

FROM alpine:3.20
ENV JAVA_HOME=/opt/jre
ENV PATH="$JAVA_HOME/bin:$PATH"
COPY --from=build /jre $JAVA_HOME
RUN addgroup -S app && adduser -S app -G app
WORKDIR /app
COPY --from=build /app/target/app.jar app.jar
USER app
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]

