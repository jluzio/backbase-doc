# Ingestion

## Trying to reset user
- get access token for delete
https://developer.backbase.com/api/specs/accessgroup-integration-service/accessgroup-integration-inbound-api/2.4.1/operations/AccessToken/getAccessToken

accessgroup-integration-service
get /v2/accessgroups/access-token

curl --location --request GET 'http://localhost:20002/v2/accessgroups/access-token' \
--header 'Cookie: XSRF-TOKEN=d3f285f9-7864-4661-a476-7dfbfafc5363'

- delete legal entity
https://developer.backbase.com/api/specs/access-control/access-control-service-api/2.8.2/operations/LegalEntities/postLegalEntitiesBatchDelete

legalentity-integration-service
post /service-api/v2/legalentities/batch/delete

curl --location --request POST 'http://localhost:20003/v2/legalentities/batch/delete' \
--header 'X-AccessControl-Token: 6336ebae-6032-90c4-f65b-253f51d3943f' \
--header 'Content-Type: application/json' \
--header 'Cookie: XSRF-TOKEN=d3f285f9-7864-4661-a476-7dfbfafc5363' \
--data-raw '{
  "externalIds": ["8010119457"]
}'

- delete user in database
note: maybe optional

delete
from EN_USER
where external_id = '<username>';
