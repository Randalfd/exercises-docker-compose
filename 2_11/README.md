# flight Booking

## how i use docker in the project

I enabled postgresql image instead of using binaries, i make port sharing to use it externally in dbeaver.
Also i has fully setup composer to make run when i need test, deploy or something else.

## Compose

```yalm
services:
  spring-app:
    container_name: reserva_vuelos_app
    image: reserva_vuelos:1.0.0
    build: .
    ports:
      - "8080:8080"
    environment:
      DATABASE_URL: jdbc:postgresql://psql:5432/postgres
      DATABASE_USER: postgres
      DATABASE_PASSWORD: reserva_vuelos_321
    depends_on:
      - psql
  psql:
    image: postgres:18
    environment:
      POSTGRES_DB: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: reserva_vuelos_321
    ports:
      - "5432:5432"
```

## Dockerfile

```Dockerfile
FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /build
COPY pom.xml .
RUN mvn -B -q dependency:go-offline
COPY src ./src
RUN mvn -B -q clean package -DskipTests

FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
RUN adduser -D spring
COPY --from=build /build/target/*.jar app.jar
USER spring
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```
