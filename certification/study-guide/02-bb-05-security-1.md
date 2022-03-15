Authentication Service
=====================================

You can [build custom Authentication Services](https://community.backbase.com/documentation/platform-services/1-12-6/build_a_custom_authentication_service) using Service SDK. These custom services easily integrate with Infrastructure and Platform Services.

![backbase architecture](https://community.backbase.com/documentation/platform-services/1-12-6/images/puml/backbase_architecture.svg?ver=1.12.6)

Understand the request flow
---------------------------

The following diagram shows the flow of a request to a downstream service using the Authentication Service.

![authentication service flow](https://community.backbase.com/documentation/platform-services/1-12-6/images/puml/platform/authentication_service_flow.svg?ver=1.12.6)

Understand starters
-------------------

Starters contain basic functionality and minimal dependencies to build custom authentication services. Starters are organized hierarchically as shown, extending functionality from their parents:

![understand the authentication service](https://community.backbase.com/documentation/platform-services/1-12-6/images/platform/understand_the_authentication_service.png?ver=1.12.6)

The current starters are:

*   `authentication-external-jwt-service-spring-boot-starter-parent` - the Spring Boot parent starter for the Authentication Service, with support for external JWTs.    
        GroupId+ArtifactId: com.backbase.buildingblocks:authentication-external-jwt-service-spring-boot-starter-parent

*   `authentication-service-spring-boot-starter-parent` - the Spring Boot parent starter for the Authentication Service, without support for external JWTs.    
        GroupId+ArtifactId: com.backbase.buildingblocks:authentication-service-spring-boot-starter-parent

*   `authentication-external-jwt-service-starter` - the starter for the Authentication Service, with support for external JWTs.    
        GroupId+ArtifactId: com.backbase.buildingblocks:authentication-external-jwt-service-starter

    You can use this starter to add the [default implementation](https://community.backbase.com/documentation/platform-services/1-12-6/default_authentication_service_endpoints) of the following endpoints to the existing service:    
    *   `/login`        
    *   `/logout`        
    *   `/status`      
    *   `/refresh`        

*   `authentication-service-starter` - a minimal Authentication Service, without support for external JWTs.    
        GroupId+ArtifactId: com.backbase.buildingblocks:authentication-service-starter


Authentication security
=======================

The `auth-security` library supports the following types of authentication required for DBS services:
*   Mutual TLS (mTLS)
*   JWT based authentication

To use the authentication security library, add the following dependency to your service’s POM file:

    <dependency>    
      <groupId>com.backbase.buildingblocks</groupId>
      <artifactId>auth-security</artifactId>
    </dependency>

Auth Security HttpSecurityConfigurer beans
------------------------------------------

The `AuthSecurityAutoConfiguration` class registers several `HttpSecurityConfigurer` beans. These beans and their [sort order](https://docs.spring.io/spring/docs/5.2.9.RELEASE/javadoc-api/org/springframework/core/annotation/Order.html) are listed below:

*   `MtlsHttpSecurityConfigurer` - order 80
    Configure /integration-api/\*\* access. We require any user authenticating via mTLS to also have the {@Code MutualTlsUserDetailsService.MTLS\_AUTHORITY} GrantedAuthority to be authorised to access the service. Users without this authority will receive a HTTP Status code 403.

*   `ServiceApiHttpSecurityConfigurer` - order 81
    Configure /service-api/\*\* access. We require any JWT to have the "api:service" scope claim.

The configuration properties for Auth Security are:
---------------------------------------------------

### Group backbase.security.service-api.authentication

- `backbase.security.service-api.authentication.required-scope`  

  **Default:** _none_

### Group backbase.security.mtls
Mutual TLS authentication (mTLS)

- `backbase.security.mtls.subject-principal-regex`

  **Default:** `CN=(.*?)(?:,|$)`  
  Regular expression used for extracting the ID from the X509 certificate.
  By default this extracts the value of the CN field.

- `backbase.security.mtls.paths`  

  **Type:** java.util.List<java.lang.String>  
  **Default:** `/integration-api/**`  
  Configures the list of paths for mTLS access.

  Uses ant-style path patterns, where:
  *   `?` matches one character.
  *   `*` matches zero or more characters.
  *   `**` matches zero or more 'directories' in a path.

  The default path is `/integration-api/**`. To enable all paths, configure to: `/**`.

- `backbase.security.mtls.enabled`

    **Default**: true  
    If `true`, enables mutual TLS authentication and its associated configuration.

- `backbase.security.mtls.trusted-clients`

  **Type**: java.util.List<com.backbase.buildingblocks.backend.security.auth.config.MutualTlsConfigurationProperties.TrustedClient>
  **Default**: _none_  
  List of trusted clients supported by the service.
  Includes the _subject_ (see `TrustedClient#subject`) field which is validated against the ID extracted from the DN in the certificate by the `#subjectPrincipalRegex`.

- `backbase.security.mtls.validate-client`

    **Default**: false  
    If `true`, validates the client ID against the trusted client list.

### Group backbase.security.service

- `backbase.security.service.paths`

  **Type**: java.util.List<java.lang.String>  
  **Default**: /service-api/**

  Configures the list of paths for Service API access. Uses the following ant-style path patterns:
  Uses ant-style path patterns, where:
  *   `?` matches one character.
  *   `*` matches zero or more characters.
  *   `**` matches zero or more 'directories' in a path.

  Default path is `/service-api/**` To enable all paths, configure to: `/**`.

- `backbase.security.service.enabled`

  **Default**: true

Property used to enable Service API authentication and its associated configuration.
Spring Security method access-control
-------------------------------------

You can configure the Spring Security method access-control to use the @PreAuthorize method level annotation with a custom Spring-EL expression. To check whether a user has the privilege to perform a function, use the annotation as follows:

    @PreAuthorize("checkPermission('some_resource', 'some_function', {'privilege1', 'privilege2'})")

To use the annotation in your application, enable Spring Security with an authentication provider. To use the annotation with the Edge Service and internal JWT token, enable the JWT internal token consumer.



Build a custom Authentication Service
=====================================

This section shows you how to build a custom Authentication Service using the Maven archetype.

Generate your project with Maven
--------------------------------

Before you implement your Authentication Service, use Maven to generate your project. Either use Maven interactive mode or batch mode as follows:

*   **Generate your project using Maven interactive mode**

*   **Generate your project using Maven batch mode**

    To generate the project using batch mode, execute the Maven command as follows:
    *   To activate batch mode, use the `-B` flag.
    *   To select the archetype that Maven uses to generate your project, define the `archetypeGroupId`, `archetypeArtifactId`, and `archetypeVersion` parameters.

    *   To configure the archetype, define values for the `groupId`, `artifactId`, `version` of the project, and the base `package` as shown.

            mvn archetype:generate \
              -DarchetypeArtifactId=auth-service-archetype \
              -DarchetypeGroupId=com.backbase.archetype \
              -DarchetypeVersion=2.24.0 \
              -DgroupId=com.backbase.service.auth \
              -DartifactId=auth-service \
              -Dversion=1.0-SNAPSHOT \
              -Dpackage=com.backbase.service.auth \
              --batch-mode

### Default project structure

The `<ROOT PACKAGE>` contains the following classes:

*   `Application.class` - provides a default Spring Boot class with the following extra annotations:
    *   `@EnableDiscoveryClient` - enables the Authentication Service to register with the Registry Service.
    *   `@EnableDefaultAuthEndpoint` - enables the default implementation of authentication endpoints.

*   `SecurityConfiguration.class` - extends the `AuthSecurityConfiguration` class. It includes the default Spring Security Configuration and adds in-memory users.


### Default configuration

The default configuration is provided with the Authentication Service archetype. These properties are picked up as the initial service configuration when you deploy the Authentication Service on an app server.

The default configuration is stored in the following files in the `/resources` folder:
*   `application.yml` - the base configuration containing properties for service registration, user authorization and sending login/logout events to the message broker.
*   `logback.xml` - defines the severity levels to filter the log messages shown during service startup, runtime and shutdown.
*   `bootstrap.yml` - optionally enable this file to externalize your configuration with [Spring Config Cloud Server (SCCS)](https://spring.io/projects/spring-cloud-config).


When the Authentication Service starts as a [plain Spring Boot service](https://community.backbase.com/documentation/platform-services/1-12-6/build_a_custom_authentication_service#ips_run_the_service), some of the default property values are overwritten by the top-level local development `application.yml`. This property file is never a part of the Authentication Service WAR.

Optional: Override the default endpoint
---------------------------------------

You can override the default endpoints. For example, when you override the login endpoint, define in `@EnableDefaultAuthEndpoint` the endpoints that are not going to be overridden.

1.  To exclude the `LOGIN` endpoint when you override default endpoints:
2.  Import the enum based on the selected exclusion:
        import static com.backbase.buildingblocks.authentication.core.AuthEndpoints.LOGIN;
3.  Annotate the class:
        @EnableDefaultAuthEndpoint(exclude = LOGIN)

Optional: Customize successful user login and logout processing
---------------------------------------------------------------
You can customize successful user login and logout processing. For example, you can provide specific HTTP response handling. To do this, add a custom `AuthenticationHandler` interface bean implementation to your application context.

The following example shows how to implement custom processing for the Authentication Service. It redirects the initial HTTP request to a specific URL when the user logs in or logs out.

    /** * Bean AuthenticationHandler interface implementation example for processing successful user login and logout. */
    public class CustomAuthenticationHandler implements AuthenticationHandler {      
      @Override    
      public void onSuccessfulLogin(HttpServletRequest request, HttpServletResponse response, Authentication authentication)
      {
        redirect(request, response);    
      }
      @Override    
      public void onSuccessfulLogout(HttpServletRequest request, HttpServletResponse response, Authentication authentication)
      {        
        redirect(request, response);    
      }
    }

Note that the Spring AOP aspect is implemented for both the `onSuccessfulLogin(…​)` and `onSuccessfulLogout(…​)` methods. Calling either of these methods sends the login or logout event to a message broker. For more details on event producing, see [Default Authentication Service endpoints](https://community.backbase.com/documentation/platform-services/1-12-6/default_authentication_service_endpoints) .

Optional: Customize successful token refresh processing
-------------------------------------------------------

You can customize successful token refresh processing. For example, you can provide specific HTTP response handling. To do this, add a custom `RefreshHandler` interface bean implementation to your application context.

Warning

This customization only affects token refreshes that are explicitly called through the `/refresh` endpoint under Authentication Service. This does not cover a scenario where the token is [implicitly refreshed](https://community.backbase.com/documentation/platform-services/1-12-6/external_jwt_renewal_mechanism#jwt-expiration-and-the-automatic-renewal-process) during token conversion.

The following example shows how to implement custom processing for the Authentication Service. It redirects the initial HTTP request to a specific URL when the user logs in or logs out.

    /** * Bean RefreshHandler interface implementation example for processing successful token refresh. */
    public class CustomRefreshHandler implements RefreshHandler {     
      @Override
      public void onSuccessfulRefresh(HttpServletRequest request, HttpServletResponse response, Authentication authentication)
      {        
        redirect(request, response);    
      }
    }

Note

The Spring AOP aspect is implemented for the `onSuccessfulRefresh()` method. Calling the method sends the refresh event to a message broker. For more details on event producing, see [Default Authentication Service endpoints](https://community.backbase.com/documentation/platform-services/1-12-6/default_authentication_service_endpoints) .

Run the service
---------------

To run a service with Spring Boot:

1.  Set the `SIG_SECRET_KEY`, `EXTERNAL_SIG_SECRET_KEY` and `EXTERNAL_ENC_SECRET_KEY` to the value set in the target environment.

2.  Ensure that the service is correctly pointing to the Registry Service location. This is also set in the `application.yml`.

3.  Run the service:

        mvn clean install spring-boot:run

4.  Verify that the service is running with the health endpoint:

    http://{auth\_host}:{auth\_port}/actuator/health

5.  Check whether the service has registered with the Registry Service by accessing:

        http://{registry_host}:{registry_port}/registry



Configure services to consume internal JWTs
===========================================

Only an authorized user with a valid internal token that contains all the user’s details can reach a secured Backbase service configured under the Edge Service. Other users receive a `401 Unauthorized` response.

Enable internal JWT consumption
-------------------------------

To configure any downstream service to consume the internal tokens that the BAS Token Converter produces, use the `@EnableInternalJwtConsumer` annotation. This annotation enables internal JWT consumption by intercepting calls to the existing `WebSecurityConfigurerAdapter` and appending `InternalJwtConsumerFilter` to the Spring Security configuration.

Follow these steps to configure the downstream service to verify and load the internal tokens:

1.  Add the `@EnableInternalJwtConsumer` annotation to your main `Application` class:

        @SpringBootApplication
        @EnableInternalJwtConsumer
        @EnableDiscoveryClient
        public class Application extends SpringBootServletInitializer implements WebApplicationInitializer {     

          public static void main(String[] args) {
            SpringApplication.run(Application.class, args);    
          }
        }

2.  Add the following default configuration to the `application.yml` file:

        # Default internal JWT configuration
        sso.jwt.internal:
          type: signed      
          signature.key:          
            type: ENV          
            value: SIG_SECRET_KEY

3.  Ensure that you [generate](https://community.backbase.com/documentation/platform-services/1-12-6/backbase_generate_jwt_keys) the secret key and populate the `SIG_SECRET_KEY` environment variable.


Get claims from the internal token
----------------------------------

Once the downstream service has received the request and verified the internal token, follow these steps to get the claims from the internal token:

1.  Check the application `SecurityContext` and get the [Spring Authentication](https://docs.spring.io/autorepo/docs/spring-security/4.2.3.RELEASE/apidocs/org/springframework/security/core/Authentication.html) that was created based on the details in the internal token:

        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

    By default, this returns the `InternalJwtAuthentication`.

2.  Use the `JsonWebTokenClaimsSetUtils` utility method to get the claim by name from the Spring Authentication:

        Optional<String> value = JsonWebTokenClaimsSetUtils.getClaim(Authentication authentication, String claimName);



### Token Converter
(note: repeated from IPS)

![Token conversion flow](https://community.backbase.com/documentation/platform-services/1-12-6/images/puml/platform/token_converter.svg?ver=1.12.6)

#### Add user IDs from Access Control Users to internal tokens

If the Users capability is part of your environment, the system administrator can configure the token converter to get a user’s IDs from the User Manager service in Users, and add them to the internal token as follows:
- The token converter uses the external user ID in the access token to look up the user and find their internal DBS user ID and legal entity ID.
- It includes the internal user ID, legal entity ID, external user ID, and issuer ID in the following claims in the internal token: inuid (internal user id), leid (legal entity id), idp.sub (external user id), idp.iss (external issuer id)

![token](https://community.backbase.com/documentation/platform-services/1-12-6/images/puml/platform/token_converter_lookup_bas.svg?ver=1.12.6)

Adding these IDs to the internal token has the following benefits:
- Improved performance: The Access Control client does not have to call the User Manager service and convert the username into the user ID for every request that uses Access Control.

- Interoperability: Services that deal with external systems like IdPs don’t have to look up the user’s IDs. For example, when creating a confirmation for the Transaction Signing capability, the domain capability can use the information in the internal token to record the internal and external IDs of the current user in the confirmation. This enables the IdP that signs the transaction to directly compare the ID in the current external access token to the value stored in the confirmation, without further lookups.

- Reduce the need for unique usernames: When using an IdP, there is no guarantee that there is only one user with a particular username. The same username could exist in several realms or organisations. Including the IDs in the internal token reduces the need for unique usernames in such cases.

#### Caching
To improve performance, the token converter can cache the following data during the token conversion process:
- Caching conversions: To improve the performance of page loading in CXS, the token converter can cache the token conversion for a short, configurable amount of time. The default value is 3 seconds. This provides a balance between security and performance. Caching is disabled by default. However a system administrator can enable it and customize the configuration if needed.
- Caching the internal record lookup: If the token converter is configured to get the user’s IDs from the User Manager service in the Users capability, the token converter can cache the internal record lookup so that it doesn’t have to perform the lookup every time a new token is needed. The token converter can cache the user record for a configurable amount of time, that is representative of a user session, for example, 30 minutes. As this data rarely (if ever) changes, it can be cached for a longer period to improve lookup times.

Use the token converter service that is appropriate for your Backbase environment:
- BAS Token Converter - use this external token converter with custom Authentication Services built on the authentication archetype.
- Identity Token Converter - use this external token converter with Backbase Identity Services.
- Custom token converter - create a custom token converter based on the token converter starter to convert custom tokens from a third party.

Default URL: http://token-converter/convert
To override this URL, set the gateway.token.converter.url Edge Service property.


### Understand JWT claims

JSON Web Tokens (JWTs) use claims to provide and transfer information between the token producer and the token consumer. Claims are statements about a user or entity. They also include additional data.

There are three types of claims:
- Registered claims - predefined claims with reserved names.
These claims are recommended but not mandatory, for example, iss (issuer) and sub (subject). For the list of registered claims, see RFC 7519.
- Public claims - claims that anyone using JWTs can define and use. To avoid collisions, define these in either:
  - The IANA JSON Web Token Registry.
  - A URI that contains a collision resistant namespace.
- Private claims - custom claims that two parties agree to use privately.

In Backbase Platform, JWTs are split into the following types. Each type has its own mandatory and optional claims.
- External tokens are used for authentication and integration with services that run externally to Edge Service.
- Internal tokens are used for communication between services within the DMZ and under the Edge Service. The Token Converter generates internal tokens from external tokens.

JWTs use these claims to transfer:
- Information you need transmit. For example, data to identify the user or entity.
- Information about the token. For example, how long it is valid for.

You can also add custom claims to external tokens or internal tokens. These are claims that you include in addition to the Backbase Platform claims. You can add a private claim to a custom application to include more information. For example, a mobileActive claim that adds more information about the user’s activity within a banking mobile app.



Custom JWT Mappers
==================

- ExternalJwtMapper

  On Auth service:

      @Component
      @Slf4j
      //1
      public class CustomExternalJwtMapper implements ExternalJwtMapper {

          public Optional<ExternalJwtClaimsSet> claimSet(Authentication authentication, HttpServletRequest request) {
              log.info("Adding extra claim to the token");
              // 2
              String country = request.getLocale().getCountry();
              return Optional.of(new ExternalJwtClaimsSet(Collections.singletonMap("usr_country", country)));
          }
      }

- InternalTokenEnhancer

  On token converter:
      @Component
      public class CustomTokenEnhancer implements InternalTokenEnhancer {
          @Override
          public OAuth2AccessToken enhance(OAuth2AccessToken accessToken, OAuth2Authentication authentication) {
              accessToken.getAdditionalInformation().putAll(getCustomClaims());
              return accessToken;
          }

          private Map<String, Object> getCustomClaims() {
              Map<String, Object> claims = new HashMap<>();
              claims.put("twToken", "58f0755d-24fb-4ab3-a420-b7a10e31226c");
              return claims;
          }
      }




How to use the REST API to add user groups
==========================================

Append the REST API URLs listed in the table below to the following URL:
http://host:port/gateway/api/portal

Users with ADMIN role can create User Groups.
<group>
    <name>{group_name}</name>
    <description>{group_description}</description>
    <role>{USER|MANAGER}</role>
</group>