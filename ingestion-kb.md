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

- remove assigned permissions (if there are errors removing legal entity due to assigned permissions)
https://developer.backbase.com/api/specs/access-control/access-control-service-api/2.8.2/operations/ServiceAgreements/putAssignUsersPermissions

curl --location --request PUT 'http://localhost:20001/service-api/v2/accessgroups/service-agreements/8a67ad8e7c70045c017c9e60e579005a/users/6b65a725-5c97-4921-af2b-17d049865f79/permissions' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJiYi1jbGllbnQiLCJhdWQiOlsiYmItb2F1dGgiXSwic2NvcGUiOlsiYXBpOnNlcnZpY2UiXSwiaXNzIjoidG9rZW4tY29udmVydGVyIiwiZXhwIjoxNjM0NzQ1NzQ0LCJqdGkiOiJhZTE4NzA0YS05NTA0LTRiNzEtYmUwOC01NDk0ODEzMGQ4ZDIiLCJjbGllbnRfaWQiOiJiYi1jbGllbnQifQ.DoX8hzHI6DNRRKs_7MqO7_gPKd44qjcNF6AiwtUZC_Y' \
--header 'Content-Type: application/json' \
--header 'Cookie: XSRF-TOKEN=138d66e9-4af4-4e24-97ad-7486448949ec' \
--data-raw '{
  "items": []
}'

- delete arrangements
https://developer.backbase.com/api/specs/arrangement-manager/arrangement-service-api/2.4.4/operations/Arrangements/postDelete
Note: this endpoint doesn't delete "arrangement x legal entity" relations

arrangement-manager
post /service-api/v2/arrangements/delete

curl --location --request POST 'http://localhost:20201/service-api/v2/arrangements/delete' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJiYi1jbGllbnQiLCJhdWQiOlsiYmItb2F1dGgiXSwic2NvcGUiOlsiYXBpOnNlcnZpY2UiXSwiaXNzIjoidG9rZW4tY29udmVydGVyIiwiZXhwIjoxNjM0NTcyOTY5LCJqdGkiOiJkMmNkNzhjMi1hMGJlLTQ5ZjQtYjE5YS1kMDExMmU0NmYzNDAiLCJjbGllbnRfaWQiOiJiYi1jbGllbnQifQ.8KipGFCXzu64LDv6zH4T_oX925zs5WKQ8GlMWVtlyyI' \
--header 'Content-Type: application/json' \
--header 'Cookie: XSRF-TOKEN=ee0b97d8-d437-434d-953c-708fb577c2f8' \
--data-raw '[
  {
    "selector": "ID",
    "value": "dc21d610-056a-4436-af3b-1ff8d64eed02"
  },
  {
    "selector": "ID",
    "value": "1b47ea73-54cd-49f0-862a-c10fdc2c80b7"
  },
  {
    "selector": "ID",
    "value": "10b3209a-5807-49ac-b0fc-6ea4093fac7d"
  }
]
'


- db cleanup
a) delete arrangement x legal entities
delete from ARRANGEMENT_LEGAL_ENTITIES
where legal_entity_id = '<customer-legal-entity-id>';

b) delete user
delete
from EN_USER
where external_id = '<username>';
