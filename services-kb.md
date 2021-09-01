## Services
- client-api: available to web clients (via gateway/api)
 - specifies the external requests received from users or external clients through Edge service
 - require user JWT and apply access control
- service-api: available within services
 - specifies machine-to-machine requests received from trusted internal systems
 - requests happen outside of the context of the user
 - raml-spec generates client to invoke

Rules:
- if called from another service -> service-api
- if called by clients through the Edge service -> client-api
- if both -> service-api & client-api


- bootstrap.yml: sets the context for web

- service spec project: specification first, contains the client and example data to be used as mock

# Apis
https://developer.backbase.com/be/api/


## service specification
mvn archetype:generate \
  -DarchetypeArtifactId=raml-specifications-archetype \
  -DarchetypeGroupId=com.backbase.archetype \
  -DarchetypeVersion=9.3.1 \
  "-DserviceName=Example Presentation Service API Spec" \
  -DservicePackageName=presentation.example \
  -DgroupId=exampleGroupId \
  -DartifactId=exampleArtifactId

NOTE: if the service is a service-api, add serviceId to raml plugin to make sure the service is correctly callable
ex:
<serviceId>fee-service</serviceId>

## service impl
mvn archetype:generate \
  -DarchetypeArtifactId=core-service-archetype \
  -DarchetypeGroupId=com.backbase.archetype \
  -DarchetypeVersion=9.3.1 \
  -DserviceName=example-service \
  -Dpackage=com.backbase.example \
  -DgroupId=com.backbase.example \
  -DartifactId=example-service

# references
https://community.backbase.com/documentation/ServiceSDK/9-3-1/ssdk_ref_raml_guidlines#ssdk_ref_raml_guidlines
https://community.backbase.com/documentation/ServiceSDK/9-3-1/service_sdk_ref_raml_api_maven_plugin_1_0#service_sdk_ref_raml_api_maven_plugin_1_0
https://community.backbase.com/documentation/ServiceSDK/9-3-1/http_service_to_service_communication
https://community.backbase.com/documentation/ServiceSDK/9-3-1/create_a_service_specification_using_a_maven_archetype
https://community.backbase.com/documentation/ServiceSDK/9-3-1/create_a_core_service#create_a_core_service
https://community.backbase.com/documentation/ServiceSDK/9-3-1/service_to_service_communication


# tokens
https://community.backbase.com/documentation/ServiceSDK/latest/http_service_to_service_communication
- Authorization (internal/external token)
- X-CXT-User-Token (external token sent by Token converter?)

- XSRF-TOKEN
## POST/PUT/ETC request in Postman
- headers
  - Cookie must include XSRF-TOKEN (simple way may be to copy cookie header after login in chrome)
  - x-xsrf-token must have the same value as XSRF-TOKEN
- auth: generate OAuth 2.0 token in Postman with same user as chrome

https://community.backbase.com/t/create-a-new-dbs-capability-part-4-test-the-capability/3691
https://community.backbase.com/documentation/platform-services/latest/csrf_protection
- XSRF-TOKEN: This token is used to guard against CSRF attacks and is constantly updated with the responses. 
All POST requests should include the X-XSRF-TOKEN header with a value identical to the one set in this cookie.

- Authorization: This is an external JWT token that should be provided as a Bearer token in the HTTP Authorization header with all requests.

## Postman
- put on Tests for request or collection:
pm.environment.set("XSRF-TOKEN", decodeURIComponent(pm.cookies.get("XSRF-TOKEN")))
NOTE: only stores AFTER request
- header on request: X-XSRF-TOKEN = {{XSRF-TOKEN}}


## Example Service API with internal token and user context
- Authorization: Internal token
- X-CXT-User-Token: External token(?)

2021-04-12 13:56:37.144 DEBUG [user-manager,ac511a21574fcfe66398dec4bc3e2f7e,676a0d1616bf5d82,false] 1 --- [nio-8080-exec-9] b.b.b.c.h.InterServiceLoggingInterceptor : request: GET http://user-manager/service-api/v2/persistence/users/externalId/admin headers=[[Authorization:"Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJiYi1jbGllbnQiLCJhdWQiOlsiYmItb2F1dGgiXSwic2NvcGUiOlsiYXBpOnNlcnZpY2UiXSwiaXNzIjoidG9rZW4tY29udmVydGVyIiwiZXhwIjoxNjE4MjM1OTIxLCJqdGkiOiI0ZmY5NDU4Ni1jOTEyLTQyMzItYTk3OS05NzY2MTNjOGE1MDQiLCJjbGllbnRfaWQiOiJiYi1jbGllbnQifQ.5mKV4WrSWGIxQvVWzIHdVZ29yMZZp9ukFcerAnAU1s0", Accept:"application/json, application/xml, application/*+json, text/xml, application/*+xml", Content-Type:"application/json", X-CXT-User-Token:"Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpbnVpZCI6IjJhMTdlNjdjLTI5NzctNGEyZC1iZjE1LWE2NDM1ODA1ODM0NSIsInN1YiI6ImFkbWluIiwiaWRwIjp7InN1YiI6IjJhMTdlNjdjLTI5NzctNGEyZC1iZjE1LWE2NDM1ODA1ODM0NSIsImlzcyI6Imh0dHBzOi8vaWRlbnRpdHkuZGV2LmJucC5saXZlLmJhY2tiYXNlc2VydmljZXMuY29tL2F1dGgvcmVhbG1zL2JucC1lbXBsb3llZSJ9LCJzY29wZSI6W10sImV4cCI6MTYxODI3ODk5Nywic2FpZCI6IjhhODA5N2ZlNzViN2Q5ZmQwMTc1YjdlYzljZTAwMDAxIiwicm9sIjpbIlJPTEVfQURNSU4iLCJ1bWFfYXV0aG9yaXphdGlvbiIsIkFETUlOIiwiUk9MRV9ncm91cF9tYW5hZ2VyKE1BTkFHRVIpIiwiUk9MRV9ncm91cF9hZG1pbihBRE1JTikiLCJvZmZsaW5lX2FjY2VzcyJdLCJqdGkiOiJlZTg0MDBhNS0yMzViLTQ2ZTItODE2Ni1lZWZkNmE5NTVkYjUiLCJjbGllbnRfaWQiOiJUb2tlbi1jb252ZXJ0ZXIifQ.KeahqahfSBcaQXFeD6a0IUSDVVoTSv7ddf9If5OAQEE", X-CXT-Remote-User:"admin", X-CXT-RequestTime:"1618235797", X-CXT-UserAgent:"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:87.0) Gecko/20100101 Firefox/87.0", X-CXT-ChannelID:"channelId", X-CXT-RequestUUID:"caa283b9-3232-4e83-ab9e-b949022e1d70", X-CXT-AuthStatus:"DefaultAuthStatus{authLevel=bb-auth-rejected, message='Default auth level: Rejected'}", Content-Length:"0", X-B3-TraceId:"ac511a21574fcfe66398dec4bc3e2f7e", X-B3-SpanId:"676a0d1616bf5d82", X-B3-ParentSpanId:"15c1b4b6cbeaf1a7", X-B3-Sampled:"0", x-amzn-trace-id:"Root=1-60745195-639ace7135c16320429e9558", x-envoy-external-address:"10.0.16.114", x-request-id:"6dc156f0-31ae-448d-a908-09fae77c9977", x-envoy-attempt-count:"1", forwarded:"proto=https;host=app.dev.bnp.live.backbaseservices.com;for="54.216.203.94:33114""]] payload=[]


# random port for service
- application.yaml
server:
  # random port
  port: 0

# private service (useful for service-api)
# API Registry client configuration
eureka:
  instance:
    metadata-map:
      public: false
      role: live

-> public: false

# Lombok copyable annotations
lombok.config (file at project root)
lombok.copyableannotations += org.springframework.beans.factory.annotation.Qualifier

# Run with Spring tools
Run "Application.java"
Add on environment vars:
SIG_SECRET_KEY
JWTSecretKeyDontUseInProduction!

# Json schema
Extending a base schema (BB extension?)
e.g.:
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "type": "object",
  "javaType": "com.backbase.integration.arrangement.rest.spec.v2.arrangements.ArrangementsPostRequestBody",
  "extends": {
    "$ref": "base.json"
  },
  "properties": {
  }
}

## Internal Token (service-api)
curl --location --request POST 'http://token-converter:8080/oauth/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=bb-client' \
--data-urlencode 'client_secret=bb-secret' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode 'scope=api:service'

Note: this request does not include user claims. Send them in request params, headers, body, etc.


## User permissions
- claims: Token Converter(?)
- Access Control


# csrf
security.enable.csrf=false
buildingblocks.security.csrf.enabled=false

        - name: security.enable.csrf
          value: "false"
        - name: buildingblocks.security.csrf.enabled
          value: "false"
        

# maven-blade-plugin
mvn com.backbase.oss:blade-maven-plugin:run


# Mobile FIDO
curl --location --request POST 'http://localhost:8181/fido-service/service-api/v1/applications/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer {{ACCESS_TOKEN}}' \
--data-raw '{
    "appKey": "retail",
    "appId": "http://{{YOUR_IP}}:8180/auth/realms/backbase/protocol/fido-uaf/applications/retail/facets",
    "trustedFacetIds": [
        "android:app-key-hash:com.backbase.examplebanking",
        "android:apk-key-hash:{{FACET_ID}}",
        "ios:bundle-id:com.backbase.start"
    ]
}'

curl --location --request POST 'http://localhost:8181/fido-service/service-api/v1/applications/' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer {{ACCESS_TOKEN}}' \
--data-raw '{
    "appKey": "retail",
    "appId": "http://{{YOUR_IP}}:8180/auth/realms/backbase/protocol/fido-uaf/applications/retail/facets",
    "trustedFacetIds": [
        "android:app-key-hash:com.backbase.examplebanking",
        "android:apk-key-hash:{{FACET_ID}}",
        "ios:bundle-id:com.backbase.start"
    ]
}'

