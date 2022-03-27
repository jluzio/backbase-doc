HTTP communication
==================

Service SDK uses HTTP for reliable and secure synchronous service-to-service communication.

client-api, integration-api and service-api
-------------------------------------------

Services require separate entry points for systems and users. To enable this, HTTP service-to-service communication requires a separation of a service’s API into the following types:

*   client-api: specifies the external requests received from users or external clients through the Edge Service. Client-api endpoints require user JWTs and apply access control.

*   integration-api: specifies requests received from trusted external systems that don’t have to go through the Edge Service.

*   service-api: specifies service-to-service requests received from trusted internal systems. These requests happen outside of the context of the user. They use the [OAuth](https://oauth.net/2/) Client Credentials Grant flow and obtain internal JWTs from a Spring Security OAuth2 Authentication Service.


Split the operations of a service specification as follows:

*   If the operation is called by another service, put it into your service-api RAML.

*   If the operation is called by clients through the Edge Service, put it into your client-api RAML.

*   If the operation is called by another service and also called by clients through the Edge Service, put it into your service-api and client-api RAML.

*   If the operation is called by an external service directly, put it into your integration-api RAML.


Ensure the `baseUri` matches either `client-api/{version}`, `integration-api/{version}` or `service-api/{version}`. Access control requires the start of request paths to follow this format.

By default, HTTP Clients use the RAML file’s `baseUri` value. To override this, set the configuration property that includes the `<packageName>` option from the [raml-api-maven-plugin-1-0](https://community.backbase.com/documentation/ServiceSDK/11-3-0/service_sdk_ref_raml_api_maven_plugin_1_0). The `backbase-spec-starter-parent` sets `<packageName>` with the maven `codegen.serviceName` property. For example, where `<packageName>` is `your.service`, use the following property to override the base URI:

`backbase.communication.services.your.service.base-uri=/service-api/v2/persistence`

Multiple RAML files generated from the same plugin execution have the same `<serviceName>` value, so their individual base URIs can’t be overriden with this configuration property. To override the `baseUri` for these RAML files, construct the client bean and call the `setBaseUri()` method in your configuration class.

Integration services
--------------------

Integration services can use mTLS to authenticate inbound communications from external services. To enable mTLS, set the following property: `backbase.security.mtls.enabled=true`

Although it isn’t mandatory for integration services that use mTLS, best practice is to split integration service specifications as follows:
*   Inbound integration services, where the remote system acts on behalf of the user, are integration-api.
*   Outbound integration services, that are called by a DBS backed service, are service-api.

Services which are only exposed to peer services, such as persistence or pandp services, should exclusively use service-api.

HTTP service-api request authentication
---------------------------------------

Services need to authenticate HTTP requests to prove that the request originates from a trusted source. The `interServiceRestTemplate` bean automatically adds an inter-service service authentication token to the `Authorization` header for all requests, for example:

    { "scope" : [   "api:service" ], "sub" : "my-service", "exp" : 2147483647, "iat" : 1484820196}

The `interServiceRestTemplate` bean acquires this token from a Spring Security OAuth2 Authentication Service:

![inter service authentication](https://community.backbase.com/documentation/ServiceSDK/11-3-0/images/puml/service_sdk/inter_service_authentication.svg?ver=11.3.0)

The client service checks whether its cached service API token has expired before sending a request to the service-api endpoint of the target service. If it has expired, it attempts to acquire a new token from the OAuth2 Authentication Service before proceeding.

The target service also checks the expiry timestamp of the service API token when it receives the request and rejects requests for service-api endpoints which contain an expired token. Therefore, there is a small window (typically a few milliseconds) between the client service sending the request and the target service receiving the request in which the service API token could expire. For this reason, when the client service receives a HTTP `401 Unauthorized` response from the target service, it attempts to get a new service API token from the OAuth2 Authentication Service and then retries the request once.

If a user’s claims need to be accessed over service-to-service communication, best practice is to explicitly declare any required user details as request parameters in your API specification. If this isn’t possible, you can attempt to access the information through the `SecurityContextUtil` methods `getOriginatingUserJwt()` and `getUserTokenClaim()`. These methods depend on the non-guaranteed presence of a `X-CXT-User-Token` header, so their responses are `Optional`.

The `X-CXT-User-Token` header is not used for HTTP inter-service authentication or authorization. Instead, a new security context is created based on the inter-service OAuth access token. This context does not contain the user’s claims.

User token claims in service-api RestControllers
------------------------------------------------

The service-api token can’t contain user context claims because a service uses the token to perform multiple requests. Best practice is to design your service-api to send user identifiers as path or payload parameters instead of through a token’s claims.

When using Service SDK generated HTTP client services, the `InternalRequest` fields are sent as HTTP headers. For example, the HTTP client services pass the user token as the `X-CXT-User-Token` header.

`RestControllers` use `SecurityContextUtil` to obtain an `InternalJWT` or a single claim:

*   Use the `getOriginatingUserJwt()` method to get a parsed `InternalJWT`.

*   Use the `getUserTokenClaim()` method to obtain a single claim:

        @Autowired SecurityContextUtil util;
        ...
        String said = util.getUserTokenClaim("said", String.class)


`SecurityContextUtil` derives the correct source of the originating user’s JWT:

*   If the request arrives through a client-api HTTP endpoint, the source of the claims is the `Authorization` header.

*   If the request arrives through HTTP service-to-service communication, the source of the claims is the `X-CXT-User-Token` header.


You can use `SecurityContextUtil` on a service’s request processing thread to extract JWT claims. However, best practice is that service-apis declare required user details as request parameters in their API specification.

## client-api / service-api headers example

Example for calling a client-api that calls a service-api
> Note: the values from the initial internal token are passed using `x-cxt-*`, with `x-cxt-user-token` being the authorization from the first request.

### client-api
```java
"x-b3-parentspanid" -> "0337ede747d8740b"
"content-length" -> "0"
"cookie" -> "XSRF-TOKEN=07d1c5ff-144a-4240-a447-cbba4c969a45"
"x-forwarded-proto" -> "http"
"postman-token" -> "3830d1c2-af76-4f7c-a31a-cfcc428c1d17"
"x-b3-sampled" -> "1"
"x-forwarded-port" -> "7777"
"x-forwarded-for" -> "0:0:0:0:0:0:0:1"
"forwarded" -> "proto=http;host="localhost:7777";for="0:0:0:0:0:0:0:1:52747""
"accept" -> "*/*"
"authorization" -> "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJQZXRlciIsInJvbCI6WyJST0xFX2dyb3VwX3VzZXIoVVNFUikiLCJST0xFX1VTRVIiXSwiY2xpZW50X2lkIjoiVG9rZW4tY29udmVydGVyIiwibmFmIjoxNjQ4NDc5MTgzLCJpZHAiOnsic3ViIjoiUGV0ZXIifSwiY25leHAiOnRydWUsInNjb3BlIjpbXSwiYW5sb2MiOnRydWUsImFuZXhwIjp0cnVlLCJleHAiOjE2NDgzOTYzODMsImVuYmwiOnRydWUsImlhdCI6MTY0ODM5Mjc4MywianRpIjoiNTk0ODZmOGYtYzc2YS00YTA0LThhMTEtNDU2MTlkNzFlOGI5IiwidHJhbnMiOiJoZWFkZXIifQ.Cm9sKmyBpmU3GfC4mi74njE_56Y_lV2cxphVOu5ml2Y"
"x-forwarded-host" -> "localhost:7777"
"x-b3-traceid" -> "0337ede747d8740b"
"x-b3-spanid" -> "ec9d2dc4c559815f"
"x-forwarded-prefix" -> "/api/exchange-rate-service"
"host" -> "host.docker.internal:9915"
"accept-encoding" -> "gzip, deflate, br"
"user-agent" -> "PostmanRuntime/7.29.0"
```

### service-api
```java
"x-b3-parentspanid" -> "ec9d2dc4c559815f"
"postman-token" -> "3830d1c2-af76-4f7c-a31a-cfcc428c1d17"
"x-b3-sampled" -> "1"
"x-cxt-channelid" -> "channelId"
"x-forwarded-for" -> "0:0:0:0:0:0:0:1"
"x-cxt-remote-user" -> "Peter"
"x-cxt-requestuuid" -> "31ff6e0f-f364-4f6b-8d7c-13665d017368"
"forwarded" -> "proto=http;host="localhost:7777";for="0:0:0:0:0:0:0:1:52747""
"accept" -> "application/json, application/*+json"
"authorization" -> "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJiYi1jbGllbnQiLCJhdWQiOlsiYmItb2F1dGgiXSwic2NvcGUiOlsiYXBpOnNlcnZpY2UiXSwiaXNzIjoidG9rZW4tY29udmVydGVyIiwiZXhwIjoxNjQ4MzkzMzcwLCJqdGkiOiI2NjM2MGQyMi03ZWE3LTQ5M2EtOTQ1Ny0xODgxZGNmNjNmMDYiLCJjbGllbnRfaWQiOiJiYi1jbGllbnQifQ.ZSC3RhJ6l1P4Qn-jFHWVTjEA5f814r7JYIq3Lf745gk"
"x-cxt-requesttime" -> "1648393327"
"x-cxt-authstatus" -> "DefaultAuthStatus{authLevel=bb-auth-rejected, message='Default auth level: Rejected'}"
"x-cxt-useragent" -> "PostmanRuntime/7.29.0"
"x-b3-traceid" -> "0337ede747d8740b"
"x-b3-spanid" -> "dc3145235e34c1c8"
"host" -> "host.docker.internal:9916"
"content-type" -> "application/json"
"x-cxt-user-token" -> "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJQZXRlciIsInJvbCI6WyJST0xFX2dyb3VwX3VzZXIoVVNFUikiLCJST0xFX1VTRVIiXSwiY2xpZW50X2lkIjoiVG9rZW4tY29udmVydGVyIiwibmFmIjoxNjQ4NDc5MTgzLCJpZHAiOnsic3ViIjoiUGV0ZXIifSwiY25leHAiOnRydWUsInNjb3BlIjpbXSwiYW5sb2MiOnRydWUsImFuZXhwIjp0cnVlLCJleHAiOjE2NDgzOTYzODMsImVuYmwiOnRydWUsImlhdCI6MTY0ODM5Mjc4MywianRpIjoiNTk0ODZmOGYtYzc2YS00YTA0LThhMTEtNDU2MTlkNzFlOGI5IiwidHJhbnMiOiJoZWFkZXIifQ.Cm9sKmyBpmU3GfC4mi74njE_56Y_lV2cxphVOu5ml2Y"
"connection" -> "Keep-Alive"
"accept-encoding" -> "gzip,deflate"
"user-agent" -> "Apache-HttpClient/4.5.12 (Java/11.0.12)"
```

Multi-tenancy support
---------------------

When multi-tenancy is enabled, Service SDK automatically passes the tenant identifier in the X-TID HTTP header of service requests. It adds a `ClientHttpRequestInterceptor` to the `interServiceRestTemplate` bean to achieve this.

HTTPS for inter-service communication
-------------------------------------

Set the following configuration properties to enable HTTPS service-to-service communication:

`backbase.communication.http.default-scheme=https`

Sets the default scheme used by the code-generated service-api clients for outbound requests to HTTPS.

You can override this scheme for a specific target service using `backbase.communication.services.<service-spec-package>.scheme`, where `<service-spec-package>` is the package name configured for the service in the service specification project.

For example, to override the default scheme used by the service-api client provided by the approvals persistence service specification project, use `backbase.communication.services.dbs.approval.persistence.scheme=<http/https>`.

`eureka.instance.secure-port=<HTTPS-port>`

Advertises the given port as the secure port for this service instance in the service registry.

`eureka.instance.secure-port-enabled=true`

Advertises that this service instance will accept requests sent to the secure port.

`eureka.instance.non-secure-port-enabled=false`

Advertises that this service instance will not accept requests sent to the non-secure port.

For details of how to configure SSL certificates and a port for listening for HTTPS connections, consult the documentation for your application server.

HTTP request timeouts
---------------------

You configure the timeouts used for inter-service HTTP requests with the following properties:


`backbase.communication.http.request-config.connection-request-timeout`
Timeout in milliseconds used when requesting a connection from the connection manager.
Default: 10000

`backbase.communication.http.request-config.connect-timeout`
Timeout in milliseconds used when waiting for a new connection to the remote server to be established.
Default: 10000

`backbase.communication.http.request-config.socket-timeout`
The socket read timeout in milliseconds.
This is the maximum period of inactivity between two consecutive data packets being received.
Default: 30000

For each of these timeouts, a value of zero is interpreted as an infinite timeout and a negative value is interpreted as undefined (system default).

HTTP connection pooling
-----------------------

The inter-service HTTP client pools and reuses connections for multiple requests.

You use the `backbase.communication.http.client.*` properties to fine-tune the behavior of the connection pool, for example, to set the maximum connections per route, or the idle connection timeout.

See [Group backbase.communication.http](https://community.backbase.com/documentation/ServiceSDK/11-3-0/service_sdk_ref_config_params#group_backbase_communication_http). for details of the available configuration properties.


BE Essentials Notes
-------------------
https://community.backbase.com/documentation/ServiceSDK/latest/http_service_to_service_communication
- client-api: available to web clients (via gateway/api)
  - specifies the external requests received from users or external clients through Edge service
  - require user JWT and apply access control

- service-api: available within services
  - specifies machine-to-machine requests received from trusted internal systems
  - requests happen outside of the context of the user
  - require service token

- integration-api: available to external clients
  - may require MTLS authentication
  - doc says it doesn't require token, but it may require service token (? - NEED TO VERIFY)

Rules:
- if called from another service -> service-api
- if called by clients through the Edge service -> client-api
- if both -> service-api & client-api
- if external client: integration-api

## Token converter call example
```log
2022-03-27 17:08:33.200 DEBUG [token-converter,945a40584c56c142,7c59bb9d8edec295,true] 13764 --- [nio-8080-exec-5] o.s.w.f.CommonsRequestLoggingFilter      : Before request [GET /bb-au
thentication-token-converter-service/convert, client=127.0.0.1, user=Peter, headers=[host:"localhost:8080", authorization:"Bearer eyJraWQiOiJNYmV1VmVVWlhVT2FJcDgwYmx1XC9sanFOQjNKZE9aSD
gxQ3JGU0tpMmVcL2M9IiwiY3R5IjoiSldUIiwiZW5jIjoiQTEyOENCQy1IUzI1NiIsImFsZyI6ImRpciJ9..SjDuaohCdBJKPo5vquJwqg.eJEpRUP5uPGk3jzMDXu_orzTXdwMGiqjhX7mxYLp18VDl70B78y5MnlDKEKmDHRiu3wzGDxscKzOa
jnh6s69Q5KH6UsAMUYBlmOfdwqbRKgJFDvpNhW412qLLp_yZ9q_Q7BDIxR61rQdTiOSN1hapOG706XonUwS1yx_T96fVyaVpAriyvZFS5e97V9A-W3iFQ52I4prVbpCNgFk39MXBB2Q8xgCoZJDZh8pxhuIObt9hCmQo_8-4r8iy26HIcfaxHOPl
zZ0hvRrPZ9bWtjt9Kx4uY9LvargpOED0b83ntd7bEzHJmpQzhK20jw9Rjmy-A6jeiwXStXbdzzL4tSuc-v7mFd-ibUOEck0EClSmSUtmP4_5WyDlXS-XV13Pu6IHn-eM7dWFpwqFYuD1ELtT2E7p8fRwgH-ZdY-SDtmqS25la39tlnY547MpryQw
rm7H4K0YdIpPNr2ghydo0qieuqHtSdJ7sGPxuf5J5Lcn53VLRi_K2IUJSeUU-mOnLU2wEf3RAcSkPIQ4h6JkKReFL_gYpd0k8PFdjORZO4erWI.iJy1jB1SXe9LAL82fqUGNg", user-agent:"PostmanRuntime/7.29.0", accept:"*/*"
, postman-token:"d5ff8e68-a64b-4b54-8290-a5a12c31a66e", accept-encoding:"gzip, deflate, br", connection:"keep-alive", cookie:"XSRF-TOKEN=07d1c5ff-144a-4240-a447-cbba4c969a45", bb_auth:
"true", x-original-uri:"http://localhost:7777/api/exchange-rate-service/client-api/v1/exchange/fee", x-b3-traceid:"945a40584c56c142", x-b3-spanid:"7c59bb9d8edec295", x-b3-parentspanid:"945a40584c56c142", x-b3-sampled:"1"]]

```