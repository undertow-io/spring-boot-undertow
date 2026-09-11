# spring-boot-undertow

Community-maintained Undertow starter for Spring Boot 4.x.

## Background

Undertow support was [removed from Spring Boot 4.0](https://github.com/spring-projects/spring-boot/issues/46917) because no Servlet 6.1-compatible Undertow release existed at the time. The Spring Boot team has [explicitly declined](https://github.com/spring-projects/spring-boot/issues/50381) re-adding it upstream, recommending a community-maintained third-party starter instead.

This project fills that gap using [Undertow EE](https://github.com/undertow-io/undertow-ee) 2.0.1.Final (which implements Jakarta Servlet 6.1) and the modularised Undertow support from Spring Boot 4.0.0-M1 as a starting point.

## Project Structure

```
spring-boot-undertow/
├── pom.xml                                       <- parent POM
├── module/
│   └── undertow-spring-boot-autoconfigure/       <- core Undertow integration
│       └── pom.xml
└── starter/
    └── undertow-spring-boot-starter/             <- Undertow starter
        └── pom.xml
```

## Usage

Add the starter to your Spring Boot 4 project:

```xml
<dependency>
    <groupId>io.undertow</groupId>
    <artifactId>undertow-spring-boot-starter</artifactId>
    <version>${undertow-starter.version}</version>
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

## Configuration

The canonical configuration prefix is `undertow.server.*` for the main web server and `undertow.management.*` for the management/actuator server.

```yaml
# application.yml
undertow:
  server:
    threads:
      io: 4
      worker: 32
    buffer-size: 16KB
    direct-buffers: true
    max-http-post-size: 4MB
    accesslog:
      enabled: true
      dir: /var/log/undertow
      pattern: combined
    options:
      server:
        ALWAYS_SET_KEEP_ALIVE: "true"
  management:
    accesslog:
      prefix: management_
```

### Deprecated prefixes

The old `server.undertow.*` and `management.server.undertow.*` prefixes are **deprecated** but still work. They are automatically mapped to the new prefixes at startup, and a WARN is logged listing every deprecated key that was set. If both old and new keys are set for the same property, the new key takes precedence.

These deprecated prefixes will be removed in the next feature release. See [MIGRATION.md](MIGRATION.md) for the full old-to-new key table.

### WebFlux (Reactive) Support

This starter also works with `spring-boot-starter-webflux`. Reactive HTTP handling goes through the Servlet bridge (`ServletHttpHandlerAdapter`), the same approach Spring Boot uses for Jetty reactive support.

### Micrometer Metrics

When `spring-boot-starter-actuator` and `micrometer-core` are on the classpath, Undertow-specific metrics are automatically registered:

- `undertow.threads.worker.*` - XNIO worker thread pool metrics (core, max, current, busy, queue size)
- `undertow.threads.io` - IO thread count
- `undertow.sessions.*` - Session metrics (active, created, expired)

## Building

```bash
mvn clean install
```

## Tests

This project uses the Spring Boot Web Server TCK (`spring-boot-web-server` test-fixtures) to validate compatibility. The test-fixtures are not published to Maven Central, so you need to build Spring Boot locally first:

```bash
# 1. Clone and build Spring Boot to install test-fixtures to your local Maven repo
git clone https://github.com/spring-projects/spring-boot.git
cd spring-boot
git checkout v4.1.0
./gradlew publishToMavenLocal

# 2. Run the TCK tests with the 'tck' profile
cd /path/to/spring-boot-undertow
mvn test -pl module/undertow-spring-boot-autoconfigure -Ptck
```

**Test results:** 223 tests, 215 pass, 6 skipped, 8 known incompatibilities:

- **5 log-message pattern tests** (inherited from TCK): The abstract base class uses a `(Jetty|Tomcat)` regex that cannot be modified. Equivalent Undertow-specific log-message tests are provided as `undertowStartedLogMessage*`.
- **3 reactive stop/graceful-shutdown tests** (inherited from TCK): `Undertow.stop()` tears down the XNIO worker immediately. The old native `UndertowHttpHandlerAdapter` (removed from Spring Framework 7.0) kept worker threads busy for the request duration; the Servlet bridge dispatches to the servlet engine's async executor instead. Servlet-based graceful shutdown works correctly.

## Requirements

- Java 17+
- Maven 3.6+
- Spring Boot 4.1.0+

## License

[Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0)
