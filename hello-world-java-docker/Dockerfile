FROM registry.access.redhat.com/ubi8/ubi-minimal:8.5

MAINTAINER Muhammad Edwin < edwin at redhat dot com >

LABEL BASE_IMAGE="registry.access.redhat.com/ubi8/ubi-minimal:8.5"
LABEL JAVA_VERSION="11"

RUN microdnf install --nodocs java-11-openjdk-headless && microdnf clean all
RUN mvn install
WORKDIR /work/
COPY target/*.jar .

EXPOSE 8080
CMD ["java", "-jar", "application.jar"]
# Use an image with Maven and JDK pre-installed
FROM maven:3.8.3-openjdk-11 AS build

# Set the working directory in the container
WORKDIR /app

# Copy the Maven project file (pom.xml) into the container
COPY pom.xml .

# Download and install dependencies specified in the pom.xml file
RUN mvn dependency:go-offline

# Copy the rest of the application code into the container
COPY src src

# Build the application
RUN mvn install

# Use a lightweight base image for the final image
FROM openjdk:11-jre-slim

# Set the working directory in the container
WORKDIR /app

# Copy the built application JAR file from the previous stage into the container
COPY --from=build /app/target/*.jar app.jar

# Expose the port the app runs on
EXPOSE 8080

# Command to run the application
CMD ["java", "-jar", "app.jar"]

