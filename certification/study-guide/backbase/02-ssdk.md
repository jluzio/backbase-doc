# SSDK

Rapidly create new digital banking services with Service SDK. DBS and CXS capabilities are composed of one or more of these services, following a microservice architecture.

A digital banking service is created using the core-service-archetype and has the following functionality out of the box:
- Security: enforce request authentication, support internal JWTs, and use extendable HTTP security.
- Logging: use the standard Spring Boot log format by default, with the option to select alternative log formats. Service SDK also offers protection against log injection attacks.
- Production support: use Spring Boot Actuator health-checks and metrics for production support.
- Tracing: identify requests as they pass from service to service with Spring Cloud Sleuth. Optionally search and aggregate tracing logs with Zipkin.
- Service discovery: automatically detect services with Eureka Client or Kubernetes.
- Load balancing: support the efficient distribution of network traffic.
- Cloud configuration: externalise the config of distributed microservices with Spring Cloud Config.

To provide additional functionality such as persistence, integration, and multi-tenancy to your services, extend your digital banking services with Service SDK’s collection of libraries.

## Security starter
The service-sdk-starter-security library provides the following authentication behavior by default:
- Enforces authentication for all requests, except for the following types of request:
  - requests to URL patterns configured as public.
  - requests to the /actuator/health end point, for basic health check details.
- Enforces authentication to actuator endpoints, so only users with the ACTUATOR role have access.
- Sets the default session creation policy to STATELESS, ensuring every request requires authentication.

## Logging starter
The service-sdk-starter-logging logging starter library provides the following features:
- Logging profiles
- Log injection mitigation
- Logging for HTTP requests and responses

### Logging profiles
- By default, the standard Spring Boot log format is used. For example:
```log
2019-04-24 11:06:29.506  INFO [test-wallet-presentation-service,2fdf0cf5627e3bdf,0cc38cfd504a2e5d,false] 20559 --- [nio-9921-exec-4] c.b.b.t.serviceapi.ServiceApiController  : Received create payment card request for card with id be7a257c-9918-45a7-89cb-ab94419fdd16
```

- json-logging: formats log messages as JSON. For example:
```json
{"@timestamp":"2019-04-24T11:12:28.098+01:00","@version":"1","message":"Received create payment card request for card with id b24bec96-fe67-4a47-963e-d81af722811d","logger_name":"com.backbase.buildingblocks.test.serviceapi.ServiceApiController","thread_name":"http-nio-9921-exec-4","level":"INFO","level_value":20000,"traceId":"7f1e8247385d6c9f","spanId":"a411dcb32193989d","spanExportable":"false","X-Span-Export":"false","X-B3-SpanId":"a411dcb32193989d","X-B3-ParentSpanId":"7f1e8247385d6c9f","X-B3-TraceId":"7f1e8247385d6c9f","parentId":"7f1e8247385d6c9f"}

```
- legacy-logging: formats log messages using the legacy Service SDK default pattern. For example:
```log
11:17:04.510 [] [test-wallet-presentation-service] [http-nio-9921-exec-3]  INFO [test-wallet-presentation-service,bdd0747a22d73cb7,47bf946f52990fb9,false] c.b.b.t.s.ServiceApiController - Received create payment card request for card with id a43518f4-3301-43d6-8bab-7ed873200d5e
```

### Log injection mitigation
To mitigate log injection, service-sdk-starter-logging replaces "\n" with "\n+[ " in the log messages. This distinguishes multiple lines logged from single messages.

### Logging for HTTP requests and responses
To support the debugging of HTTP requests and responses, service-sdk-starter-logging enables CommonsRequestLoggingFilter by default.
To view log statements when debugging HTTP communications, set the following property:
```conf
logging.level.org.springframework.web.filter.CommonsRequestLoggingFilter=DEBUG
```


## Starters
- service-sdk-starter-core: A BOM with a minimal set of dependencies which can be used via Maven dependency import. Use this when developing a generic service that does not require the DBS dependencies that other starters provide.
- backbase-openapi-spec-starter-parent: A Maven parent starter for OpenAPI spec projects.
- backbase-spec-starter-parent: A Maven parent starter for RAML spec projects.


## Libraries
Service SDK functionality is contained within libraries. Libraries are grouped into starters to support the rapid implementation of services. For more information on starters, see Starters.
Service SDK provides the following libraries:
- api: Supplies consistent API functionality for your services.
- auth-security: Identifies the authentication status for a request through the system. An authentication status wraps the level and is allocated a system user.
- boot-loader: A Spring boot application launcher that allows you to add dependencies to the CLASSPATH of your service at runtime.
- service-sdk-common-core: Common interfaces, annotations and utilities, including generic API error exception classes mapped directly to HTTP responses, such as BadRequestException and NotFoundException.
- building-blocks-common: "A shared library of common utility classes and interfaces for DBS services such as:
  - InternalRequest - ensures that once a transaction (request) enters the system the contextual information is not lost, and can be passed for latter processing.
  - Models
  - Annotations
- behavior-extensions-camel: Camel-based behavior extensions.
- communication: An HTTP transport library that facilitates communication between microservices.
- events: Enables eventing through an event bus, Spring Cloud Stream, and HTTPS support.
- multi-tenancy: Supports a single instance of software that runs on a server to serve multiple customers or tenants.
- multi-tenancy-liquibase: Enables Liquibase to populate multi-tenanted databases on startup.
- persistence: Configures the Spring-provided persistence unit and Liquibase.
- production-support: Enables you to perform status checks on your microservices. This includes health checks and metrics.
- resilience: Contains configuration for Netflix’s Hystrix latency and fault tolerance library. Hystrix is no longer in active development.
- validation: Validate API requests inline with JSR standards.
- backbase-mock-starter-parent: Maven parent starter for mock services.
- backbase-spec-starter-parent: Maven parent starter for spec projects. See Create a service specification using a Maven archetype.
- service-sdk-starter-core: A BOM with a minimal set of dependencies which can be used via Maven dependency import. Use this when developing a generic service that does not require the DBS dependencies that other starters provide.
- service-sdk-starter-eureka-client: Configures and extends Eureka client side load balancer. Provides the option to use Kubernetes service discovery instead of Eureka.
- service-sdk-starter-logging: Configures logback formats for Spring applications.
- service-sdk-starter-mapping: Supports MapStruct to manage Java Bean mapping.
- service-sdk-starter-security: Configures Spring Security authentication using JSON Web Tokens. Provides optional scoped access,
- service-sdk-web-client: Supports asynchronous calls to services through reactive processing.


BE Essentials Notes
-------------------

## What is SSDK?
Toolset to build Backbase Microservices
Taking care of non-functional requirements
Combination of best practices
Standartized way of development

## How does it accelerate development?
Speed: archetypes & starters to quickly scaffold new services
Consistency: Libraries for cross-cutting concerns, like auth or persistence
Solidity: Build on proven technology, according to best practices, maintained in monthly releases
Completeness: Essentials such as logging and resilience ootb
