****
> Create Legal Entity

    POST {{entitlementsUrl}}legalentity-integration-service/v2/legalentities

    {
      "externalId": "Bank",
      "name": "BANK",
      "parentExternalId": null,
      "type": "BANK"
    }

> Create Legal Entity under another

    {
      "externalId": "KPMG",
      "name": "KPMG",
      "parentExternalId": "Bank",
      "type": "CUSTOMER"
    }

> Create Users

    POST {{entitlementsUrl}}user-manager/integration-api/v2/users

    {
    "externalId": "Jonathan",
    "legalEntityExternalId": "KPMG",
    "fullName": "Jonathan"
    }

> Set Service Agreement External Id

    GET {{entitlementsUrl}}legalentity-integration-service/v2/legalentities/Bank/serviceagreements/master
    (obtain id)

    {{entitlementsUrl}}accessgroup-integration-service/v2/accessgroups/serviceagreements/{{msaIdForBank}}

    {
      "externalId": "Bank_External"
    }

> Add Admins

    POST {{entitlementsUrl}}accessgroup-integration-service/v2/accessgroups/serviceagreements/admins/add

    [
        {
            "externalUserId": "admin",
            "externalServiceAgreementId": "Bank_External"
        },
        {
            "externalUserId": "Jonathan",
            "externalServiceAgreementId": "KPMG_External"
        }
    ]    

> Create Products

    POST {{productSummaryUrl}}arrangement-manager/integration-api/v2/products

    {
        "id": "checking-account-001",
        "productKindId": "kind1",
        "productKindName": "Current Account",
        "productTypeId": "1111111",
        "productTypeName": "Checking Account"
    }

> Create Arrangements

    POST {{productSummaryUrl}}arrangement-manager/integration-api/v2/arrangements

    {
      "id": "A01",
      "legalEntityIds": ["KPMG"],
      "productKindId": "kind1",
      "productKindName": "Current Account",
      "productId": "checking-account-001",
      "name": "ACC1",
      "alias": "alias1",
      "bookedBalance": 100.1,
      "availableBalance": 100.2,
      "creditLimit": 100.3,
      "IBAN": "NL67ABNA8835593166",
      "BBAN": "BBAN",
      "currency": "EUR",
      "externalTransferAllowed": true,
      "urgentTransferAllowed": false,
      "accruedInterest": 100.00,
      "number": "PANS",
      "principalAmount": 100.4,
      "currentInvestmentValue": 100.5,
      "BIC": "BIC123",
      "bankBranchCode": "bankBranchCode1"
    }    

> Create Account Groups

    POST {{entitlementsUrl}}accessgroup-integration-service/v2/accessgroups/data-groups/batch

    [{
    	"name": "All Accounts",
    	"description": "All Accounts",
    	"externalServiceAgreementId": "KPMG_External",
    	"type": "ARRANGEMENTS",
    	"dataItems": [
    		{"internalIdIdentifier": "{{ACC1}}"},
            {"internalIdIdentifier": "{{ACC2}}"},
            {"internalIdIdentifier": "{{ACC3}}"}		
    	]
    }]    

> Create Job Role

    POST {{entitlementsUrl}}accessgroup-integration-service/v2/accessgroups/function-groups

    {
        "name": "Viewer of Product Summary and Transactions",
        "description": "This profile will control viewing of Product Summary and Transactions",
        "externalServiceAgreementId": "KPMG_External",
        "permissions": [
            {
                "functionId": "{{FG_ID_PRODUCT_SUMMARY}}",
                "assignedPrivileges": [
                    {
                        "privilege": "view"
                    },
                    {
                        "privilege": "edit"
                    }
                ]
            },
            {
                "functionId": "{{FG_ID_MANAGE_TRANSACTIONS}}",
                "assignedPrivileges": [
                    {
                        "privilege": "view"
                    }
                ]
            }
        ]
    }

> Assign Permissions

    POST {{entitlementsUrl}}accessgroup-integration-service/v2/accessgroups/users/permissions/user-permissions

    [  
       {  
          "externalServiceAgreementId":"KPMG_External",
          "externalUserId":"Peter",
          "functionGroupDataGroups":[  
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

> Login and Set Context

    {{url}}auth/login

    {
         "username": "Peter",
         "password": "Peter"
    }


    {{url}}access-control/client-api/v2/accessgroups/usercontext?_csrf={{xsrf-token}}

    {
    	"serviceAgreementId": "{{msaIdForKPMG}}"
    }    
