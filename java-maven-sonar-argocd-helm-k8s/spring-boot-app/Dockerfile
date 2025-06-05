# -------- STAGE 1: Build the Spring Boot App + Install Docker CLI ----------
FROM maven:3.8.7-eclipse-temurin-17 AS builder

LABEL maintainer="anushkafarkade"

# Set working directory
WORKDIR /app

# Install Docker CLI
RUN apt-get update && \
    apt-get install -y ca-certificates curl gnupg lsb-release && \
    install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null && \
    apt-get update && \
    apt-get install -y docker-ce-cli && \
    rm -rf /var/lib/apt/lists/*

# Verify tools
RUN java -version && mvn -version && docker --version

# Copy and build the Spring Boot app
COPY pom.xml .
RUN mvn dependency:go-offline

COPY src ./src
RUN mvn clean package -DskipTests

# -------- STAGE 2: Runtime (slim, safe, no Maven) ----------
FROM eclipse-temurin:17-jdk

WORKDIR /app

# Copy JAR from builder
COPY --from=builder /app/target/spring-boot-web.jar .

# Optional: re-add Docker CLI if needed at runtime
# COPY --from=builder /usr/bin/docker /usr/bin/docker

# Expose app port
EXPOSE 8080

# Run the app
ENTRYPOINT ["java", "-jar", "spring-boot-web.jar"]
