# Blade mvn config
- Access control: 2G
- Product Summary: 1G

mvn clean install -Pclean-database

## Direct Integration service
- source system provides all functionalities widget expects
- responds in timely manner
- no need to persist data to achieve the desired functionality
For other scenarios use DBS

Integration services:
- don't require authentication since the meant to be invoked in trusted environments
- useful for ingestion and other operations

# Entitlements
- access-control
- approvals
- limits

## Access control concepts
Legal Entity:
- personal or non-personal entity that is involved in a transaction or an arrangement with the bank
- hierarchical (root is the bank)

User:
- natural person
- acting of behalf of legal entity

Arrangement:
- when product is sold to customer, it becomes an arrangement
- product is a template

Account Group:
- set of arrangements
- can be combined with job roles to assign permissions to users
- unique in the scope of a service agreement

Business function:
- alias that represents a function in BE, to be checked for permissions

Privilege:
- what can be assigned to a business function: execute, view, create, etc..

Applicable function privileges:
- combination of business functions and privileges that can be made and assigned to function groups

Job Role:
- combination of business functions
- defining what a user can do
- optionally constrained by limits

Service Agreement:
- contract that includes 1+ legal entities
- way to give 3rd party specific access to your arrangements (legal entities)
Ex: accounts on Bank X
Note: root service agreement is named single/master service agreement (was master, now single?)

Permission:
- assigning permissions to a user using:
 - job profiles
 - product group

- apply additional constraints
 - limits

Context:
- consists of the service agreement
- combination of user and s.a. define the context where the permissions and privileges are set
