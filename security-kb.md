# Security

Info:
- Stateless
- JWT tokens for internal and external communication
- Short lived sessions
- Based on Spring Security

JWT consists in 3 parts: Header, Payload (claims), Signature
External JWT token:
 - is signed and encrypted
 - stored on client side and identifies the active session
Internal JWT token
 - is signed
 - inter-service communications

application.yml:
# Configure Internal JWT handler
sso:
  jwt:
    internal:
      signature:
        key:
          type: ENV
          value: SIG_SECRET_KEY

1) type: ENV
Environment variable
2) internal:
  signature: ...
It means it's only signed with the configured data
External would have external/signature and external/encryption

# default auth service - authentication-ldap
- LDAP
- uses in memory users defined in users.ldif

# custom auth service
mvn archetype:generate \
    -DarchetypeArtifactId=auth-service-archetype \
    -DarchetypeGroupId=com.backbase.archetype \
    -DarchetypeVersion=2.12.0 \
    -DgroupId=com.backbase.service.auth \
    -DartifactId=auth-service \
    -Dversion=1.0-SNAPSHOT \
    -Dpackage=com.backbase.service.auth \
    --batch-mode

must define /auth
configured with auth:
/login
/logout
/status
/refresh
- refreshes token up to 6 times, if the token isn't invoked after 1/2 of token time (5m default?)

# permissions
JWT roles can be used to define if user has permission to a specific widget (e.g. transactions),
but if the objective is to define permissions for data, the permissions are in Access Control (more fine grained).

# Roles
- ROLE_group_<NAME>(<ROLE>) : name is group name, role is associated role

# Security Projects
## Auth
https://community.backbase.com/documentation/platform-services/1-11-7/build_a_custom_authentication_service
> Change eureka endpoint to 8080
> Disable authenticator-ldap blade in platform pom.xml or Blade Trinity


# Extension points
- Beans implementing ExternalJwtMapper/ExternalJwtTenantMapper can add claims to JWT external token
- Beans implementing InternalTokenEnhancer can add claims to JWT internal token
- Claims must be mapped from external to internal, in order to be accessible in internal tokens

Token Converter's application.yml:
backbase:
  ...
  token-converter:
    ...
    claimsMapping:
      ...
      - external: bank_group
        internal: bank_group
        required: false

# references
https://community.backbase.com/documentation/platform-services/1-11-7/build_a_custom_authentication_service
https://community.backbase.com/documentation/cxs/6-2-1/understand_access_control
https://community.backbase.com/documentation/cxs/6-2-1/permissions

https://community.backbase.com/documentation/platform-services/1-11-7/add_extra_claims
https://community.backbase.com/documentation/platform-services/1-11-7/add_custom_claims_to_the_internal_token
https://community.backbase.com/documentation/identity/1-5-3/oidc_token_converter_service
