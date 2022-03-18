> Create Function Group
    
    POST {{entitlementsUrl}}accessgroup-integration-service/v2/accessgroups/function-groups

    {
        "name": "Manage SEPA CT Payments",
        "description": "This job profile will enable users to view, create and edit SEPA payments",
        "externalServiceAgreementId": "KPMG_External",
        "permissions": [        
            {
                "functionId": "{{SEPA_CT_ID}}",
                "assignedPrivileges": [
                    {
                        "privilege": "view"
                    },
                    {
                        "privilege": "edit"
                    },
                    {
                        "privilege": "delete"
                    },
                    {
                        "privilege": "create"
                    }
                ]
            }
        ]
    }

> Assign Permissions to user

    PUT {{entitlementsUrl}}accessgroup-integration-service/v2/accessgroups/users/permissions/user-permissions

    [  
      {  
          "externalServiceAgreementId":"KPMG_External",
          "externalUserId":"Peter",
          "functionGroupDataGroups":[      	
            {  
                "functionGroupIdentifier":{  
                  "idIdentifier":"{{FG_ID_MANAGE_SEPA_PAYMENTS}}"
                },
                "dataGroupIdentifiers":[  
                  {  
                      "idIdentifier":"{{DG_KPMG_All_Accounts}}"
                  }
                ]
            },
            {  
                "functionGroupIdentifier":{  
                  "idIdentifier":"{{FG_KPMG_View_PS_Transactions}}"
                },
                "dataGroupIdentifiers":[  
                  {  
                      "idIdentifier":"{{DG_KPMG_All_Accounts}}"
                  }
                ]
            }
          ]
      }
    ]

> Login as user

    POST {{url}}auth/login

    curl --location --request POST 'localhost:7777/api/auth/login' \
    --header 'Content-Type: application/x-www-form-urlencoded' \
    --data-urlencode 'username=Peter' \
    --data-urlencode 'password=Peter' \
    --data-urlencode 'submit=Login'    

> Get Payments

    GET {{url}}payment-order-service/client-api/v2/payment-orders?size=100

> Post Payment

    POST {{url}}payment-order-service/client-api/v2/payment-orders

    {
      "paymentType": "SEPA_CREDIT_TRANSFER",
      "originatorAccount": {
        "identification": {
          "identification": "{{ACC1}}",
          "schemeName": "ID"
        }
      },
      "requestedExecutionDate": "2021-01-01",
      "instructionPriority": "HIGH",
      "transferTransactionInformation": {
        "instructedAmount": {
          "amount": "100.00",
          "currencyCode": "EUR"
        },
        "counterparty": {
          "name": "J. Sparrow"
        },
        "counterpartyAccount": {
          "identification": {
            "identification": "NL09ABNA0519246539",
            "schemeName": "IBAN"
          }
        }
      }
    }
    