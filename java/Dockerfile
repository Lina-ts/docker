FROM maven:3.9-eclipse-temurin-21

RUN apt-get update
RUN apt-get install -y curl vim git wget unzip htop
RUN apt-get clean

WORKDIR /app
COPY . .

RUN mvn package
RUN rm -rf /root/.m2

EXPOSE 8080
CMD ["java", "-jar", "target/app.jar"]
