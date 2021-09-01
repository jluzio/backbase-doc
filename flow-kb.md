# Flow

- Flow
Is a set of interactions, business processes, integrations and data structures that work together to accomplish a particular business goal

- Journey
Is a group of web, mobile, service and integration components that fulfil a specific business capability
(a step in the flow?)

- Case data
is all data associated with a flow

- Process
an ordered set of activities that represent the steps required to achieve an objective

- Task
activity associated to the case that is needed to complete the process

# Case manager
http://localhost:7777/case-management-portal
manager & manager

- standalone
http://localhost:4202

## login before standalone
http://localhost:7777/api/auth/login
user & user

# Flow | Platform SDK
- Archetypes
- Starters
- OOTB Journeys

# Platform components
- Interaction engine
is the engine that renders the right questions, steps and experiences based on customer input on the digital customer touchpoint

- Process engine
is the engine that has the ability to do process automation and decision management

- Case data store
is the engine in charge of storing the case data of the flow in the database

# Flow Platform capabilities
- Document store
storing documents in cases from taks, upload, download files, cxs content services integration possible

- Comments
Task and Case comments, multiple threads per case

- Event log
Event action performer, e.a. description, several sources of information

- Messaging
Notify CSR: Task reminders, task assignments, tagged comments
Notify customer: case events, reminders


# Flow architecture
- Customer Facing Experience
  - Web Interaction SDK
  - Mobile Interaction SDK  
Composed of several widgets that represent a single step in the flow

- Employee Facing Experience
  - Employee Portal: web app (angular)

- IPS

- Digital Sales Service
  - Interaction Engine: connects to Customer FE
  - Process Engine: connects to employee portal
  - Case Data Store: connects to employee portal
  - Integration Engine: connects with the flow app


# BPMN
## Basic elements
V: Visual aid, no execution in BPMN

- Pool, Lanes [V]: visual aid to separate tasks with different actors
- Flow objects:
  - Event: 
  - Activity: task, subprocess, automatic activity
  - Gateway: decision
- Connecting objects
  - Sequence flow: flow execution direction
  - Message flow [V]: visual aid for showing messages between parts
  - Association [V]: to attach documentation to any element
- Artifacts [V]: 
  - Data object: represent info flowing through the process
  - Group: mark parts of the flow
  - Comment

## Task types
- undefined
- business rule: apply business logic
- user: associated with actor, that can be followed by the flow app (ex: validate, comment, etc in the portal)
- manual: task that can't be tracked, such as adding a file on a specific folder (physical task)
- service task: execute service on BE
- script task: action defined in the script/flow itself
- receive task: wait until message arrive
- send task: send message

## Gateways
- can be used as merging gateway, multiple inputs, one output (can be useful for visual representation)
- default flow, useful for grouping all unknown responses and avoid a deadlock
- Parallel: when flow runs 2+ parts of a diagram in parallel
- Implicit gateways:
  - XOR: when a task receives 2 sequence flows
  - Parallel: when a task splits into 2 sequence flows
  - OR (inclusive gateways)
- Inclusive: parallel gateway depending on inputs (ex: inputA, inputB | flow options: inputA, inputB or inputA and inputB

## Naming convections
- Event: object + status
- Activity: Verb + object
- Gateway: question and answers

## Events
- none (start, end)
- message
- timer
- signal (like a broadcasted message)
- error
- terminate

## Boundary event: event on task, which can or not interrupt task and continue with event specific flow
represented in lower-right inside task

## Event based gateway
- run first event that is triggered (others are forgotten)

## Notes
- Flows can have multiple end events
- Flows can have parallel tasks which go to each end event, so it doesn't end on first end event
- There is an event to kill all remaining tokens in flow
- Tasks and events can send and recieve messages, tasks can be assigned to a specific role

## Subprocess
A subprocess can be defined with a data event, in order to redirect flow to the subprocess (interrupting and non-interrupting)
Ex: "payment postponed 3 times" event can be used to create a subprocess to end the flow and make sure no more than it is postponed more than 3 times


## Executable Process
- Process: set executable flag on general
- User Tasks:
  - BE: extensions -> userTaskHandlerBeanName -> <value>
  - FE: forms -> form key -> <widget to be shown> | ex=embed:wa3:CmShowUserTaskWidgetComponent 
- Service task: implementation -> impl type
ex: expression -> <flow-el for method to be executed> | ex: ${loanOriginationProcess.sendOTP(execution)}
- Exclusive Gateways
  - details -> condition type | ex: expression -> ${case.query("$.data.customerRejection.rejectionCode") == "B301"}
- Events
  - timed: details -> duration -> <timer definition in ISO Format>
  - conditional: conditionType=expression & expression=<expression language>
  

# DMN - Decision management annotation

Components:
- Table name + id
- Hit policies 
- input name+expression 
- output name+expression

- Hit policies:
  - Single: (A)ny, (U)nique, (F)irst
  - Multiple: (R)ule order, (C)ollect

