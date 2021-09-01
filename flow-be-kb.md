# Flow BE

## Modes
- Integrated: within BB stack
- Standalone: without IPS, for dev purposes

## Authentication
- Every flow request must be authenticated
- internal JWT is used in integrated mode
- cookie is used on unauthenticated requests
- possible to add custom authentication

## Authorization
- all resource in a flow are authorized
- applied to service layer

## Access control lists
resources are protected using a.c.l. each resource is a combination of user x resource x permission

- permission: {business-function}({privilege}) on {resource-reference:type*}
- resource-references:
  - case-definition: ex=case:{caseKey}
  - process-definition: ex=process-definition:{caseKey}:{processDefinitionKey}
  - group: ex=group:{caseKey}:{processDefinitionKey}:{groupKey}
  - task: ex=task:{caseKey}:{processDefinitionKey}:{taskKey}
  - decision-definition: decision-definition:{caseKey}:{decisionDefinition}
  

## Interactions  
- Capabilities:
  - save & retrieve
  - state machine
  - integrations
  
- Action Handler
interface ActionHandler<P, B>
processes a specific user request action  

- Settings config
<resources>/interactions/*.json
defines:
  - interactions
  - action handlers
  - event-based routing
  - Spring Expression Language to set conditional fields

### Navigation
See possible steps:
[GET] /api/interaction/v1/interactions/{{interactionId}}/steps

## Case data
Stored as json, supports versioning (can retrieve by version, defaults to latest).
Can be done (closed), deleted (hard delete), archive.
Related data on document store is deleted by deleting a case.

## Process Engine
ability to do the process automation and decision management

## Document store
- File set: represents a set of files (file references)
- File reference: contains metadata about the file, and associated with the file
files can be:
  - active: available to users with permissions to process the case
  - temporary: shortlive file that exist in task view context, once task is complete temporary files becomes active
if required to be stored as binary, must be stored in content services repository
- Case: can have multiple file sets

### Document status
- ACTIVE: final document that is visible to all case workers that have permission to process the case
- TO_BE_ADDED: temporary document that is added during a case task
- TO_BE_REPLACED: temporary document that signifies pending changes to the original document during a task
- TO_BE_DELETED: temporary document that signifies pending deletion to the original document during a task

### Storage options
- Content services
application.yml: 
backbase.flow.storage.impl: cs

- Local file system
default for dev environments
backbase.flow.storage.impl: fs

## Comments and events
comments can be added/edited/etc by case workers
events can be added, can extend existing events, to add info on who/what/when and it's shown in a time log.

