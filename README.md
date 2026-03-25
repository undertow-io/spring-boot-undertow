# spring-boot-undertow

> ⚠️ **This is a temporary bridge project.** It will be deprecated once the official Spring Boot 4 upstream adds native support for the Undertow starter.

## Background

As of Spring Boot 4.1.0-M1, the upstream project does not yet publish an Undertow starter as a Maven artifact. This project fills that gap by providing:

- **`spring-boot-undertow`** — the core Undertow integration module for Spring Boot 4
- **`spring-boot-starter-undertow`** — a ready-to-use starter that wires Undertow as the embedded servlet container

Once the official Spring Boot 4 release includes Undertow starter support, this project should be replaced with the official dependency.

## Project Structure

```
spring-boot-undertow/
├── pom.xml                                       ← parent POM
├── module/
│   └── spring-boot-undertow/                     ← core Undertow integration
│       └── pom.xml
└── starter/
    └── spring-boot-starter-undertow/             ← Undertow starter
        └── pom.xml
```

## Usage

Add the starter to your Spring Boot 4 project:

```xml
<dependency>
    <groupId>com.redhat.integration</groupId>
    <artifactId>spring-boot-starter-undertow</artifactId>
    <version>4.19.0-SNAPSHOT</version>
</dependency>
```

Make sure to exclude the default Tomcat starter if you are using `spring-boot-starter-web`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## Building

```bash
# Build and install to local Maven repository (skipping tests — see note below)
mvn clean install -Dmaven.test.skip=true
```

## ⚠️ Note on Tests

Tests **cannot be compiled or executed** within this Maven project. This is because some base test infrastructure classes (e.g. abstract test base classes and test utilities) exist only inside the Spring Boot Gradle source tree and have **not been published as Maven artifacts**.

However, all tests in this project have been **manually verified against the Spring Boot Gradle project** and pass successfully. If you need to run the tests, clone the official Spring Boot repository and run them within its Gradle build environment.

## Requirements

- Java 17+
- Maven 3.6+
- Spring Boot 4.1.0-M1

## License

This project follows the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0), consistent with the upstream Spring Boot project.
