FROM amazoncorretto:17-alpine AS build

RUN apk add --no-cache maven && \
    rm -rf /var/cache/apk/*

WORKDIR /app

COPY pom.xml .
RUN mvn dependency:go-offline -B

COPY src ./src
RUN mvn clean package -DskipTests && \
    rm -rf ~/.m2/repository

FROM amazoncorretto:17-alpine

RUN addgroup -S spring && adduser -S spring -G spring

WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

RUN chown -R spring:spring /app

USER spring

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]