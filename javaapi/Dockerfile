FROM maven:3.9.6-eclipse-temurin-8 AS build
WORKDIR /app

COPY pom.xml ./
COPY src ./src

RUN mvn -B package -DskipTests

FROM eclipse-temurin:8-jre
WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

CMD ["java", "-jar", "app.jar"]
