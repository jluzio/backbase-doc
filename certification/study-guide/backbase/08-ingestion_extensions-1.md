Ingestion / Integration strategies
==================================

* Inbound REST API

      Push Pattern  
      Downstream system to Backbase  
      Inbound RESTful APIs

* Outbound REST API

      Pull Pattern  
      Backbase to Downstream system  
      Outbound RESTful APIs

* Custom Integration

      Programmatically developed integrations using the Backbase API specifications


What is a Behaviour Extension?
==============================

* Implement custom logic in Process Flow
* Implement custom validators
* Operate before (pre) and after (post) standard operations
* Modify data

Ways to extend the behavior
===========================

* ExtensibleRouterBuilder: workflow with 2 or more steps
* SimpleExtensibleRouterBuilder: simple workflow with 1 step


ExtensibleRouteBuilder: Intercept a step in a route
===================================================

```java
// 1
@Component
@Primary
public class CustomExtendingRouteBuilder extends InitiatePaymentOrderRoute {

    // 2
    @Override
    public void configure() throws Exception {

        // 3
        interceptSendToEndpoint(DIRECT_PAYMENT_VALIDATE)
                .to(CustomEndpoints.VALIDATION_INTERCEPTOR);

        super.configure();
    }

}
```
Notes: 
* InitiatePaymentOrderRoute extends ExtensibleRouteBuilder
* @Primary makes this route replace the existing one
* Step URIs are on documentation for extensions and code

```java
// 1
@Component
public class CustomEndpoints {

    private static final Logger LOGGER = LoggerFactory.getLogger(CustomEndpoints.class);

    public static final String VALIDATION_INTERCEPTOR = "direct:my.custom.payment.validate.interceptor";

    // 2
    @Consume(uri = VALIDATION_INTERCEPTOR)
    public void extraValidation(ExtendedInternalRequest<CreatePaymentRequestDto> internalRequest) {
        LOGGER.info("======== In custom endpoint for validation ========");
        InitiatePaymentOrder initiatePaymentOrder = internalRequest.getData().getInitiatePaymentOrder();

        // 3
        if (initiatePaymentOrder.getInstructionPriority().equals(ValidatedPaymentOrder.InstructionPriority.HIGH)) {
            throw new BadRequestException("This user is not allowed to make high priority payment requests");
        }
    }

}
```


SimpleExtensibleRouterBuilder: Example with single step
=======================================================

```java
package com.backbase.dbs.contactmanager.extension.simple;

import com.backbase.buildingblocks.backend.communication.extension.annotations.BehaviorExtension;
import com.backbase.buildingblocks.backend.communication.extension.annotations.PostHook;
import com.backbase.dbs.contactmanager.contact.service.ContactListContainer;

import org.springframework.core.annotation.Order;
import org.springframework.util.CollectionUtils;

import static com.backbase.dbs.contactmanager.contact.route.ContactRoute.CONTACT_LIST_ROUTE_ID;

// 1
@BehaviorExtension(
    name = "list-contact-behavior-extension",
    routeId = CONTACT_LIST_ROUTE_ID
)
@Order(1)
public class ExampleBehaviorExtension {

    // 2
    @PostHook
    public void postHookListContact(ContactListContainer contactListContainer) {
        if(CollectionUtils.isEmpty(contactListContainer.getElements())){
            return;
        }

        // 3
        contactListContainer.getElements().forEach(contact -> {
            contact.getAdditions().put("description",
                    String.format(
                            "Contact %s from category %s with phone number %s, email address %s and account in bank %s with IBAN %s",
                            contact.getName(),
                            contact.getCategory(),
                            contact.getPhoneNumber(),
                            contact.getEmailId(),
                            contact.getAccounts().get(0).getBankName(),
                            contact.getAccounts().get(0).getIban()
                            )
            );
        });
    }
}
```


Extend the behavior of a service
================================

Behavior extensions enable you to customize the workflow of your service. You can extend the service behavior in order to:

*   Operate before and after standard business operations.

*   Modify data.

*   Bypass standard business operation. That is, if some extra validation fails, throw a runtime exception. Such exceptions should be part of the API for the service.

*   Upgrade the workflow to modify out-of-the-box behavior.

*   Inject modifications into the workflow to add logic around out-of-the-box behavior.


This section shows you how to extend service behavior by adding a behavior extension to an extensible route. To create an extensible route, see [Make your service extensible](https://community.backbase.com/documentation/ServiceSDK/11-3-0/make_your_service_extensible).

Extend an extensible route
--------------------------

To create a behavior extension, you create a new service extension project in one of the following ways:

*   **Use the service-extension-archetype to create a behavior extension project**

        mvn archetype:generate \
          -DarchetypeArtifactId=service-extension-archetype \
          -DarchetypeGroupId=com.backbase.archetype \
          -DserviceGroupId=com.backbase.dbs.<extensible_service> \
          -DarchetypeVersion=<version>

        or in batch mode (with all params):

        mvn archetype:generate -B \
          -DarchetypeArtifactId=service-extension-archetype \
          -DarchetypeGroupId=com.backbase.archetype \
          -DarchetypeVersion=11.3.0 \
          -DserviceGroupId=com.backbase.dbs.paymentorder \
          -DserviceArtifactId=payment-order-service \
          -DdbsVersion=2.19.3.2 \
          -DgroupId=com.backbase.dbs.paymentorder \
          -DartifactId=payment-order-service-extension \
          -Dversion=1.0.0-SNAPSHOT \
          -Dpackage=com.backbase.dbs.payment.extension \
          -DrouteBuilderToExtend=com.backbase.dbs.payment.services.configuration.routes.InitiatePaymentOrderRoute


*   **Manually create a Maven project with the following dependency**

        <dependencies>
          <dependency>
            <groupId>com.backbase.dbs.example-capability</groupId>
            <artifactId>example-extensible-presentation-service</artifactId>
            <version>1.0.0</version>
            <classifier>classes</classifier>
            <scope>provided</scope>
          </dependency>
        </dependencies>


To manage library dependencies in the behavior extension, take the following steps in the behavior extension project’s POM file:

1.  Scope any dependencies that are available in the service as `<scope>provided</scope>`.

2.  Scope any dependencies that are not part of the service but are new in the extension as `<scope>runtime</scope>`.

3.  To make the new runtime dependencies available on the classpath, see [Add your behavior extension JAR to a service](https://community.backbase.com/documentation/ServiceSDK/11-3-0/extend_service_behavior#add_your_behavior_extension_to_a_service).


Create a class to hold the entry point of your behavior extension and annotate it with `@BehaviorExtension`. For example:

    @BehaviorExtension(name="my-extension-name”, routeId = TargetServiceRoute.ROUTE_ID)
    public class MyExtensionClass { }

The `name` parameter of the `@BehaviorExtension` annotation defines a key which can be used to enable or disable the extension using configuration properties. The `routeId` parameter defines the target service’s business process that you wish to extend.

The `@BehaviorExtension` annotation is itself annotated with Spring’s `@Component` annotation, so if this class is placed in a package which is component scanned, such as the package where the application being extended is located, Spring automatically creates an instance of the class and adds it to the application context.

Alternatively, [create your own auto-configuration](https://docs.spring.io/spring-boot/docs/2.3.4.RELEASE/reference/html/spring-boot-features.html#boot-features-developing-auto-configuration) using `META-INF/spring.factories` to register the behavior extension beans from a Java configuration class.

You can define code in a `@BehaviorExtension` class to execute before the out-of-the-box business process by providing a method annotated with `@PreHook` within the class. Similarly, you can define code to execute after the out-of-the-box business process by providing a method annotated with `@PostHook`.

For example:

    @PreHook
    public void doSomethingBefore(InternalRequest<PostRequestBody> internalRequest) {    
      // custom code here
    }

    @PostHook
    public void doSomethingAfter(InternalRequest<PostResponseBody> internalRequest) {    
      // custom code here
    }

The methods will be invoked by Camel, so may have any of the usual parameters acceptable in a Camel route. For example, the Camel exchange’s input body type, parameters annotated with `@Header`, or the Camel `Exchange` object itself. Consult the documentation for the target extension point to see the available parameters and types.

Add your behavior extension JAR to a service
--------------------------------------------

In order to include your behavior extension, you include the JAR that contains your custom route and other code to the classpath when the service is started. You must also add any third-party libraries that are not included in the original service.

When you run a service as an executable JAR, use the `loader.path` command line argument to add JARs or directories of JARs to the classpath. For example:

    ./myservice.jar -Dloader.path=/path/to/my.jar,/path/to/my/other.jar,/path/to/lib-dir

If you are using Thin WAR files instead of executable JAR files, add the runtime dependencies to the classpath in one of the following ways:

1.  Inject the behavior extension JAR into the service WAR:

        mkdir -p WEB-INF/lib
        cp /path/to/service-extension.jar WEB-INF/lib/
        jar -uf /path/to/target-service.war WEB-INF/lib/service-extension.jar

2.  Use a Maven WAR overlay to package the extension [Maven WAR overlay](https://maven.apache.org/plugins/maven-war-plugin/overlays.html)

    **Only use this in a purely additive mode inline with recommended approach to extensions. This is not recommended for overriding behavior outside of the set service extension points.**

3.  Add the behavior extension JAR to the web-app classpath in a container-specific way. For example, if you are using JBoss, you could use a deployment overlay to add the extension JAR to the /WEB-INF/lib folder of the deployed WAR. Consult the documentation for your specific application container for details.

    You cannot add behavior extensions to the global library of a container or as a JBoss module. This is because the extension depends on classes provided by the service WAR. These classes are not available to the global library classloader.


Add your behavior extension library to a service docker image
-------------------------------------------------------------

To create a Service SDK docker image with the extension library included, follow these steps:

1.  **Configure your extension project’s POM file**

    1.  Set the parent to be `backbase-service-extension-starter-parent`:

            <parent>
              <groupId>com.backbase.buildingblocks</groupId>
              <artifactId>backbase-service-extension-starter-parent</artifactId>
              <version>11.3.0</version>
            </parent>

        This is set by default when you generate your extension project with the `service-extension-archetype`.

        Note

        If it’s not possible to set `backbase-service-extension-starter-parent` as the parent, see [Configure a Jib plugin to create a new image](https://community.backbase.com/documentation/ServiceSDK/11-3-0/extend_service_behavior#configure_jib_plugin_new_image).

    2.  Configure the docker values in the POM file `<properties>`:

        To specify the base image of the extensible service, set the following properties:

            <!-- base image -->
            <docker.base.tag>1.0.0</docker.base.tag>
            <docker.base.name>repo.backbase.com/backbase-docker-releases/example-extensible-presentation-service</docker.base.name>

        To specify the name of the new image that’s created with the extension included, set the following properties:

            <!-- docker configuration -->
            <docker.image.name>my.docker.repo/staging/extended-banking-service</docker.image.name>
            <docker.image.tag>1.0.0</docker.image.tag>


2.  **Generate the image**

    To generate the image, run the following Maven command:

        mvn package -Pdocker-image

    In the generated image, the extension library resides under `app/extras`.

    To generate a local Jib Docker image that is stored directly in your local Docker daemon, run the following Maven command:

        mvn package -Pdocker-image,local-client


### Configure a Jib plugin to create a new image

If your project can’t use the `backbase-service-extension-starter-parent` as a parent, use the following Jib plugin configuration to create a new image:

    <profile>
      <id>docker-image</id>
      <build>
        <plugins>
          <plugin>
            <groupId>com.google.cloud.tools</groupId>
            <artifactId>jib-maven-plugin</artifactId>
            <version>2.5.2</version>
            <dependencies>
              <dependency>
                <groupId>com.backbase.buildingblocks</groupId>
                <artifactId>jib-extras-extension</artifactId>
                <version>11.3.0</version>
              </dependency>
            </dependencies>
            <configuration>
              <pluginExtensions>
                <pluginExtension>
                  <implementation>com.backbase.buildingblocks.maven.jib.JibExtrasExtension</implementation>
                </pluginExtension>
              </pluginExtensions>
              <from>
                <image>repo.backbase.com/backbase-docker-releases/example-extensible-presentation-service:1.0.0</image>
              </from>
              <to>
                <image>my.docker.repo/staging/${project.artifactId}:${project.version}</image>
              </to>
              <container>
                <appRoot>/app/extras</appRoot>
                <entrypoint>INHERIT</entrypoint>
                <creationTime>USE_CURRENT_TIMESTAMP</creationTime>
              </container>
              <containerizingMode>packaged</containerizingMode>
            </configuration>
            <executions>
              <execution>
                <id>extension-image</id>
                <phase>package</phase>
                <goals>
                  <goal>build</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
        </plugins>
      </build>
    </profile>

Access the HttpServletRequest in a behavior extension
-----------------------------------------------------

When you `@Autowire` the `HttpServletRequest` in your behavior extension component, Spring injects a request-scoped proxy which delegates to the current `HttpServletRequest` during request processing.

For example, to get the value of a query parameter called `myQueryParameter` during a post-hook behavior extension:

Using behavior extension annotations (Service SDK 6.0.0 or later)

    @BehaviorExtension(name="my-extension-name”, routeId = TargetServiceRoute.ROUTE_ID)
    public class MyExtensionClass {     
      private final HttpServletRequest httpServletRequest;

      @Autowired    
      public MyExtensionClass(HttpServletRequest httpServletRequest) {
        this.httpServletRequest = httpServletRequest;
      }     

      @PostHook
      public void myPostHook(Exchange exchange) {
        String myQueryParameter = httpServletRequest.getParameter("myQueryParameter");        
        // ...    
      }
    }

Overriding the extensible RouteBuilder (Service SDK 5.13.0 or earlier)

    @Component
    @Primary
    public class MyExtensionClass extends TargetServiceRoute {
      private final HttpServletRequest httpServletRequest;     

      @Autowired
      public MyExtensionClass(HttpServletRequest httpServletRequest) {
        this.httpServletRequest = httpServletRequest;
      }

      @Override
      public void configurePostHook(RouteDefinition rd) throws Exception {
        rd.process(exchange -> {
          String myQueryParameter = httpServletRequest.getParameter("myQueryParameter");
          // ...        
        });
      }
    }

Multiple behavior extensions
----------------------------

Any number of `@BehaviorExtension` classes may be provided for the same route ID. The order of execution can be controlled using Spring’s `@Order` annotation or by implementing Spring’s `Ordered` interface. For example, consider the following two extension classes which both provide a @PreHook method and target the same route ID:

    @BehaviorExtension(name="my-extension-name”, routeId = TargetServiceRoute.ROUTE_ID)
    @Order(1)
    public class MyExtensionClass {
      @PreHook
      public void doSomething(Exchange exchange) {
        // ...
      }
    }

    @BehaviorExtension(name="another-extension-name”, routeId = TargetServiceRoute.ROUTE_ID)
    @Order(2)
    public class AnotherExtensionClass {
      @PreHook
      public void doSomething(Exchange exchange) {
        // ...
      }
    }

Because of the values given in the `@Order` annotations, the `@PreHook` method in `MyExtensionClass` will be executed before the `@PreHook` method in `AnotherExtensionClass`.

Toggling behavior extensions per tenant in a multi-tenant deployment
--------------------------------------------------------------------

When multi-tenancy is enabled, annotation-driven behavior extensions can be toggled per tenant via configuration properties. By default, extensions are enabled for all tenants. To disable an extension for a specific tenant, set the following configuration property, where “`<extension-name>`” is the value of the name property in the `@BehaviorExtension` annotation, and “`<tenant-id>`” is the ID of the specific tenant:

    backbase.behavior-extensions.<extension-name>.enabled@tid(<tenant-id>)=false

Alternatively, a behavior extension can be disabled by default and enabled for a specific tenant as follows:

    backbase.behavior-extensions.<extension-name>.enabled=falsebackbase.behavior-extensions.<extension-name>.enabled@tid(<tenant-id>)=true

Advanced use cases
------------------

We do not recommend using both the annotation based extension mechanism and these more advanced cases because these advanced cases can conflict with the support for multiple extensions for the same route ID.

If defining pre- and post-hook behavior as shown above is insufficient, you can instead directly customize the Camel `RouteBuilder` for a given extension point, or completely replace it by providing an `ExtensibleRouteBuilder` implementation which returns the same route ID value as the original route and is annotated with Spring’s `@Primary` annotation.

For example, you can extend the out-of-the-box `RouteBuilder` implementation, add the `@Primary` annotation, and override the `configure()` method to completely replace the route.

    package com.backbase.extension.example;

    @Component
    @Primary
    public class CustomExtendingRouteBuilder extends com.backbase.<extensible_service>.<extensible_route> {
      @Override
      public void configurePostHook(RouteDefinition rd) throws Exception {
         super.configurePostHook(rd);
         rd.to("direct:myposthookuri");
      }
    }

You do not have to extend a route class to replace it. Instead, you can provide a new `ExtensibleRouteBuilder` implementation using the same route ID as the route you want to replace. Provided that the route IDs match, the `ExtensibleRouteBuilder` bean with the `@Primary` annotation is used.

    @Component
    @Primary
    public class ExtendedRoute2 extends ExtensibleRouteBuilder {
      public ExtendedRoute2() {
        super(ExampleRoute.ROUTE_ID);
      }

      @Override
      public void configure() throws Exception {
         from(ExampleEndpointConstants.DIRECT_BUSINESS_HELLO)
          .routeId(getRouteID())
          .to(“direct:goodbyeRoute”)
          .transform(simple("Goodbye ${in.body}. That’s all folks!"));
      }
    }

Overriding the `configurePreHook` and `configurePostHook` methods of the original `ExtensibleRouteBuilder` implementation without calling the super methods, or providing a replacement `ExtensibleRouteBuilder` implementation that does not extend `SimpleExtensibleRouteBuilder`, will bypass the mechanism which delegates to annotation-driven behavior extensions.



What is a Data Extension?
=========================

* Extend the data model for an existing DBS capability
* Add custom key-value pairs per API
* No additional code required, just configuration


Extend the data model for a service
===================================

You use data model extensions to persist custom data that is not stored in your bank database. The model extension mechanism allows you to add custom key-value pairs to the default Backbase data types without adding more code.

To implement a model extension for a service, you load a YAML configuration defining custom parameters to the service.

These extensions fields are returned in the JSON response’s `additions` field. The following example shows a JSON response containing custom properties:

    {
      "name": "John Doe",
      "category": "Employee",
      "phoneNumber": "055512345678",
      "emailId": "john@example.com",
      "accounts": [{
        "name": "Saving account",
        "alias": "Savings",
        "IBAN": "FI21 1234 5600 0007 85",
        "bankName": "Test Bank",
        "bankAddressLine1": "Jodenbreestraat 96",
        "bankTown": "Amsterdam",
        "bankPostCode": "1011NS",
        "bankCountry": "NL"
      }],
      "additions": {
        "favPokemon": "Picachu",
        "rank": "2"
      }
    }

For information on how to configure the maximum number of additions in a group, and the maximum length of an addition’s key and value, see [Data model extensions](https://community.backbase.com/documentation/ServiceSDK/11-3-0/service_sdk_ref_model_extensions).

Backbase rejects any additional property keys that have not been configured for the target entity type.  


Extend the data model for your service
--------------------------------------

To create a model extension:

1.  Install the service you want to extend. This example uses the DBS Contacts capability’s `contact-manager` service.

2.  Define your custom properties in the service’s YAML configuration file. For example, the following configuration defines a `pokemon-data` set of extension properties and associates it with a service’s specification and persistence classes:

        backbase:
          api:
            extensions:
              classes:
                com.backbase.presentation.contact.rest.spec.v2.contacts.ContactsPostRequestBody: pokemon-data
                com.backbase.presentation.contact.rest.spec.v2.contacts.ContactPutRequestBody: pokemon-data
                com.backbase.presentation.contact.rest.spec.v2.contacts.AccountInformation: pokemon-data
                com.backbase.dbs.party.persistence.spec.v2.parties.PartyDto: pokemon-data
                com.backbase.dbs.party.persistence.spec.v2.parties.AccountInformation: pokemon-data
                com.backbase.dbs.contactmanager.party.persistence.Party: pokemon-data
                com.backbase.dbs.contactmanager.party.persistence.AccountInformation: pokemon-data
              property-sets:
                pokemon-data:
                  properties:
                  - property-name: favPokemon
                    security:
                    - confidential
                    type: string
                  - property-name: rank
                    security:
                    - confidential
                    type: number

3.  Start the service with the following parameter:

        -Dspring.config.additional-location=/path/to/application.yml

    To see the values you can use in your YAML configuration, see [Data model extensions](https://community.backbase.com/documentation/ServiceSDK/11-3-0/service_sdk_ref_model_extensions).


Test your model extension
-------------------------

Your model extension is ready to use. To test the functionality you have just implemented:

1.  Send an HTTP `POST` request to insert custom data:

    `POST http://localhost:8080/contact-manager/client-api/v2/contacts`

        {
          "name": "John Doe",
          "additions": {
            "favPokemon": "Alakazam",
            "rank": "3"
          },
          "accounts": [{
            "name": "Saving account",
            ...
          }],
          ...
        }

2.  Send an HTTP `GET` request to return the custom data:

    `GET http://localhost:8080/contact-manager/client-api/v2/contacts`

        {
          "name": "John Doe",
          "category": "Employee",
          "phoneNumber": "055512345678",
          "emailId": "john@example.com",
          "accounts": [{
            "name": "Saving account",
            "alias": "Savings",
            "IBAN": "FI21 1234 5600 0007 85",
            "bankName": "Test Bank",
            "bankAddressLine1": "Jodenbreestraat 96",
            "bankTown": "Amsterdam",
            "bankPostCode": "1011NS",
            "bankCountry": "NL"
          }],
          "additions": {
            "favPokemon": "Alakazam",
            "rank": "3"
          }
        }

3.  To verify that incorrect extension data is rejected by your service, send an HTTP `POST` request with a non-configured addition:

    `POST http://localhost:8080/contact-manager/client-api/v2/contacts`

        {
          "name": "John Doe",
            ...
           "additions":   
           {
               "favPokemon": "Alakazam",  
               "rank": "3",
               "color": "Purple"
           },
          "accounts": [
            {
              "name": "Saving account",
                ...
            }
          ]
        }

    Check the error returned:

        {
          "message": "Bad Request",
          "errors": [
              {
                  "message": "The key is unexpected",
                  "key": "api.AdditionalProperties.additions[color]",
                  "context": {
                      "rejectedValue": "Purple"
                  }
              }
          ]
        }


Essentials workshop example for contacts
========================================

```yaml
---
### Live profile
spring:
  profiles: live
  datasource:
    driver-class-name: ${spring.datasource.driver-class-name}
    username: ${spring.datasource.username.contact-manager}
    password: ${spring.datasource.password.contact-manager}
    url: ${spring.datasource.url.contact-manager}

backbase:
  api:
    extensions:
      classes:
        # 1 - Presentation spec
        com.backbase.presentation.contact.rest.spec.v2.contacts.ContactsPostRequestBody: extra-info-data
        com.backbase.presentation.contact.rest.spec.v2.contacts.ContactPutRequestBody: extra-info-data
        # 2 - Persistence spec
        com.backbase.dbs.party.persistence.spec.v2.parties.PartyDto: extra-info-data
        com.backbase.dbs.party.persistence.spec.v2.parties.AccountInformation: extra-info-data
        # 3 - Persistence entities
        com.backbase.dbs.contactmanager.party.persistence.Party: extra-info-data
        com.backbase.dbs.contactmanager.party.persistence.AccountInformation: extra-info-data
      property-sets:
        extra-info-data:
          properties:
          - property-name: socialProfileLink
            type: string
          - property-name: relationship
            type: number
          - property-name: jobTitle
            type: number
  audit:
    enabled: false
  security:
    mtls:
      enabled: false      
```
Notes:
* // 1 PUT and POST
  
  To support your additional fields via the HTTP REST API you need to extend the ContactsPostRequestBody and ContactPutRequestBody class.

* // 2 PartyDto and AccountInformation

  To persist the data you need to make the additional fields available for the PartyDto and AccountInformation specification classes.

* // 3 Party and AccountInformation

  Finnally, the entity classes Party and AccountInformation classes needs to be updated with the additional data fields.
