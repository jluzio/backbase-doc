# Identity

## JWT claims
JWT claims are mapped to the external token in Keycloak.
The realm has mappers for setting these values on claims.
See: Clients, Client Scopes (module that contain mappings, can be used in a Client)

## Internal Token (service-api)
curl --location --request POST 'http://token-converter:8080/oauth/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=bb-client' \
--data-urlencode 'client_secret=bb-secret' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode 'scope=api:service'

Note: this request does not include user claims. Send them in request params, headers, body, etc.

## Others (?)
curl --location --request POST 'http://token-converter:8080/api/token-converter/oauth/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=bb-tooling-client' \
--data-urlencode 'client_secret=bb-secret' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode 'scope=api:service'
