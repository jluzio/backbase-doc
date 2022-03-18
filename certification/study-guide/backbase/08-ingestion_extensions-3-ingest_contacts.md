> Create Function Group to Manage Contacts for a Service Agreement

    POST {{entitlementsUrl}}accessgroup-integration-service/v2/accessgroups/function-groups

    {
      "name": "Manage Contacts for KPMG",	
      "description": "This job profile will enable users to view, create and edit contacts for KPMG",
      "externalServiceAgreementId": "KPMG_External",
      "permissions": [
        {
          "functionId": "{{FG_ID_CONTACTS}}",
          "assignedPrivileges": [{
              "privilege": "view"
            },
            {
              "privilege": "create"
            },
            {
              "privilege": "edit"
            }
          ]
        }
      ]
    }

> Assign permissions to user

    PUT {{entitlementsUrl}}accessgroup-integration-service/v2/accessgroups/users/permissions/user-permissions

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
            },
            {
                "functionGroupIdentifier":{
                  "idIdentifier":"{{FG_ID_MANAGE_CONTACTS_AMSTERDAM}}"
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

> Set context for user

    POST {{url}}access-control/client-api/v2/accessgroups/usercontext?_csrf={{xsrf-token}}

    {
      "serviceAgreementId": "{{msaIdForKPMG}}",
      "legalEntityId": null
    }

> Search user's Service Agreement

    GET {{url}}access-control/client-api/v2/accessgroups/usercontext/serviceagreements

> Check previlege view for user

    GET {{url}}access-control/client-api/v2/accessgroups/users/user-permissions?functionName=Contacts&resourceName=Contacts&privileges=view

> Retrieve all contacts

    GET {{url}}contact-manager/client-api/v2/contacts?saId={{serviceAgreementId}}

> Create contact for user

    GET {{url}}contact-manager/client-api/v2/contacts

    {
      "name": "John Doe",
      "alias": "John",
      "category": "Employee",
      "contactPerson": "Jane Doe",
      "phoneNumber": "055512345678",
      "emailId": "john@example.com",
      "addressLine1": "Backbase enterprise",
      "addressLine2": "",
      "streetName": "Jacob Bontiusplaats 9",
      "town": "Amsterdam",
      "postCode": "1018 LL",
      "countrySubDivision": "North Holland",
      "country": "NL",
      "accounts": [
        {
          "name": "Saving account",
          "alias": "Savings",
          "IBAN": "FI21 1234 5600 0007 85",
          "bankName": "Test Bank",
          "bankAddressLine1": "Test Bank Co",
          "bankAddressLine2": "",
          "bankStreetName": "Jodenbreestraat 96",
          "bankTown": "Amsterdam",
          "bankPostCode": "1011NS",
          "bankCountrySubDivision": "North Holland",
          "bankCountry": "NL",
          "accountHolderAddressLine1": "Backbase enterprise",
          "accountHolderAddressLine2": "",
          "accountHolderStreetName": "Jacob Bontiusplaats 9",
          "accountHolderTown": "Amsterdam",
          "accountHolderPostCode": "1018 LL",
          "accountHolderCountrySubDivision": "North Holland",
          "accountHolderCountry": "NL"
        }
      ],
      "approved": false,
      "accessContextScope": "LE"
    }