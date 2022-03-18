What is Entitlements
===================
* Defines what a user is allowed to do using a specific system
* About defining the rules
  * What users can access
  * What they can do
  * What they can use
  * How they are further constrained

Why Entitlements
================
* Create an environment that can be trusted
* Where users can securely access business functions over public channels
* Prevent errors
* Prevent frauds
* Mitigate risk and exposure
* Support the segregation of responsibilities

Entitlements in Business Banking examples
-----------------------------------------
* Permission to do a specific action: e.g. a payment of SEPA CT
* Approval of a action: e.g. approve the payment order
* Limit on an action: e.g. limit payments of a group to 100k per day
* The bank has the option to apply the limits or delegate to the user

Entitlements in Wealth Management examples
------------------------------------------
* Users can apply restrictions for other users of the shared arrangements. E.g. Roxana manages the investment portfolio for her children, and they can view it but not make any transactions.
* Can enable the other users to make transactions, and with the owner approval.

Entitlements in Retail Banking examples
---------------------------------------
* Open an account for another and allow transfer funds only between selected accounts
* Can set approval for withdrawing funds
* Can give full control when needed

Capabilities: Access Control (permissions), Approvals (approval), Limits (limit)



Access Control
=========================

When exposing digital banking business functions over public channels, a fundamental part of the solution is to control who has access to what and what can they do with it. You use Access Control to allow and restrict user access to resources. Examples of resources are transactions, contacts, and accounts.

Access to a resource means that the user is able to take one of the following actions:
*   View the resource
*   Create, update, or delete the resource
*   Approve another user to access the resource

Access Control in Backbase Platform governs and enforces the data a user has access to, the business functions he or she can perform on that data, and the additional constraints that apply when performing these business functions. For example, Access Control manages which accounts a user is allowed to view, and whether a certain user is allowed to approve a payment order.

Access Control concepts
-----------------------

The concepts used for Access Control and the way Backbase uses them are:

*   **Legal entity**: Is any personal or non-personal entity that is involved in a transaction or an arrangement with the bank. Both the bank and its customers are legal entities.    
    A legal entity:    
    *   Has one or more users that act on its behalf.      
    *   Owns one or more arrangements.    

    A legal entity hierarchy is a collection of parent-child relationships. For example, within the same deployment, this allows you to:
    *   Set up a holding structure for a corporate customer of the bank.
    *   Set up a structure within the bank to support country and regional offices.      

*   **User**: A person who interacts with the bank and who uses Backbase applications on behalf of the legal entity they are representing.  
(Note: retail customer is 1-to-1 for legal entity, corporate is 1-to-many)

*   **Product**: A financial instrument such as a credit or debit card, a business current account or a 25-year fixed rate mortgage.

*   **Arrangement**: A generic concept to represent an instance of any product held by a customer. For example, Bob’s 25-year fixed rate mortgage or his wife’s credit card.

    Note: Products and arrangements are part of Products in DBS.

*   **Service agreement**: A contract that includes one or more legal entities. A legal entity that is participating in a service agreement can allow a subset of its users to act through that service agreement and/or allow a subset of its arrangements to be accessed through the service agreement. Within each service agreement, permissions to perform specific tasks are granted to users, including access to arrangements shared by one or more legal entities (participating in that service agreement). As such, a service agreement is a way to give third party users specific access to your arrangements.

    A special kind of service agreement is called the single service agreement. This service agreement has one legal entity participant and once configured, the participant cannot be changed. Important to know is that once the user is granted with administrative permissions (e.g. manage account groups), he or she has the power to execute the task in any service agreement lower in the hierarchy. For example, if the user of the bank is assigned with manage account groups permission in the single service agreement of the bank, the user can manage account groups in any service agreement lower in the hierarchy.

    A service agreement may be restricted in time, by setting a time bound. Permissions granted to users in the time-restricted service agreement, are active and may be consumed, only while the time bound is valid.

*   **Account group**: A grouping mechanism for arrangements. You define the account groups for service agreements. Account groups contain one or more arrangements owned by participating legal entities that share arrangements. The account group manages access to these arrangements. You can include one arrangement in multiple account groups.

*   **Business function**: A task that a user performs for a specific capability. For example, Payments has the _Batch - ACH Credit_ business function to allow users to make multiple ACH credit transfers in batch. Each business function has one or more available privileges, eg. view, create, approve.

*   **Privilege**: The action the user is allowed to perform for a specific business function.

*   **Job role**: A set of business function privilege combinations or tasks such as SEPA CT payments, create, cancel, approve that are grouped together. You can further constrain business function privileges using Limits. For instance, one job role allows users to approve payment orders up to 100K and another one allows users to approve payment orders of 50K.

    A job role may be restricted in time, by setting a time bound. Permissions granted to users in the time-restricted job role, are active and may be consumed, only while the time bound is valid.

    Having these job roles consisting of business functions allows you to assign multiple tasks to a user in one go, see [available business function privilege combinations](https://community.backbase.com/documentation/DBS/2-19-3/entitlements_reference#bf_privileges_matrix).

    Job roles are set for a service agreement.

*   **Reference job role**: A special kind of job role that saves time in retail banking scenarios.

    If you need to set up the same permissions for multiple users in different service agreements, for example for retail banking customers that each have their own service agreement, it saves time to create a reference job role in the single service agreement of the bank, which then is available by default to all the service agreements of your retail customers, if they are assigned to the same assignable permission set as the reference job role.

    For more information, see [Manage and use reference job roles](https://community.backbase.com/documentation/DBS/2-19-3/entitlements_understand#reference_job_role).

*   **Permission**: The moment you assign a job role to a user, that user has the permission to perform all business function privileges in that job role. Additionally, you may assign account groups to a user job role combination. By applying these job roles combined with account groups, you set up fine grained access control. Permissions are set for a service agreement.

*   **Assignable permission set**: Banks may restrict the complete list of available combinations of business functions and privileges with an assignable permission set. This allows for fine-grained classification of permissions on service agreement level.


Access Control workflow
-----------------------

When you onboard a new customer to your DBS installation:

1.  You create the legal entity as a child of the specified parent. In most cases, this parent is a bank. However, you can also create a child entity for a customer legal entity. You do this to create a corporate entity structure such as a holding. Once a corporate entity structure is defined, users of the parent legal entity can perform typical administrator tasks like user management on child entities.

2.  You create:

    *   At least one user and associate it with the new legal entity. A user can only be associated with one legal entity.

    *   The arrangements for the new customer and associate them with the legal entity or legal entities owning them.

    *   A service agreement which is the container for the user permissions. Depending on the setup, you create either of the following:

        *   Single service agreement for each legal entity (automatically on legal entity creation or manually after the legal entity creation). This way, you define permissions of users from the participant legal entity, to access its arrangements. Users assigned with a job role containing the manage service agreements business function are entitled to traverse and manage service agreements in hierarchy (only applicable for Entitlements related actions, such as manage job roles, manage permissions, manage account groups etc.) As we mentioned before, a single service agreement cannot be changed in terms of participation, meaning it is not possible to add or remove legal entities.

        *   Create or update a service agreement (either a multi or a single service agreement). By setting up these service agreements you can allow a user of one legal entity to access the arrangements of another legal entity and perform specific tasks on those arrangements. This enables a company to access accounts of a third party in order to render a specific service. For example giving users of an accountancy company read access to your accounts.


    *   Job roles and assign them to the created user(s), in the context of the created service agreement.

    *   Account groups that contain the created arrangement(s) and assign them to one or more combinations of users and job roles in the context of the created service agreement.

After you have set up user permissions, after a user is authenticated, he or she must select the context they will be working in. Context is the service agreement in, during this session. Users only have to select context if they have permissions set in more than one service agreement. When the context is determined the permissions of the user, including optional limits, are evaluated and enforced in the context of that service agreement. As a result; users can only access what they are allowed to use and perform only the business function privileges they have permissions for. Optionally these permissions are constrained by Limits.


Classification of permissions
-----------------------------

The table above shows a list of all available combinations of business functions and privileges. Banks may not need the complete list and can restrict it. To restrict the list of business functions and privileges combinations, create assignable permission sets. This allows for fine-grained classification of permissions on service agreement level.

Access Control has predefined assignable permission sets for regular users, administrators and for the US market. You can create custom assignable permissions sets that may have every possible combination of business functions and privileges.


Manage and use reference job roles
----------------------------------

If you need to set up the same permissions for multiple users in different service agreements, for example for retail banking customers that each have their own service agreement, it saves time to create a reference job role in the single service agreement of the bank, which then is available by default to all the service agreements of your retail customers, if they are assigned to the same assignable permission set as the reference job role.

You may create, update and delete reference job roles similar to normal job roles, with the following additional characteristics:
*   Set up an assignable permission set to the reference job role to scope the possible combinations of business functions and privileges
*   Create, update, or delete reference job roles only in the single service agreements of the bank

A reference job role may be restricted in time, by setting a time bound. Permissions granted to users in the time-restricted reference job role, are active and may be consumed, only while the time bound is valid, additionally restricted by the time bound of the service agreement, where the reference job role is assigned to a user.


Approval flow in Access Control
-------------------------------

Access Control integrates with Approvals to provide more control over changes made to user permissions and to manage account groups and job roles flows. When enabled, the following actions are not immediately active, but go into a pending state until users with sufficient permissions have approved the action. The following actions and their related business function are part of the optional approval flow:

*   User permissions assignment: _Assign Permissions_
*   Account group management: _Manage Data Groups_
*   Job role management: _Manage Function Groups_
*   Service agreement management: _Manage Service Agreements_


The required permissions for a user to approve and the number of users required to approve, depend on the assigned approval policy to the related business function. For example, the _Manage Data Groups_ business function is assigned with approval policy A + B. An administrator creates a new account group, and the action results in a pending account group, that requires two approvers: one approver of level A, and another one of level B. The users who want to approve this action are:

*   Sarah with a job role of approval level A, containing _Manage Data Groups_ business functions with `view`, `approve` privileges

*   Mary with a job role of approval level B, containing _Manage Data Groups_ business functions with `view`, `approve` privileges


The created account group is approved and becomes active, after both Sarah and Mary approve the pending request.

You cannot update the created account group while the current changes are pending. This is also true for the user’s permissions and job role changes.


Audit
-----

By default Access Control uses [Audit](https://community.backbase.com/documentation/DBS/2.19.3/audit_understand) to publish and retrieve records about key business functions. Calls to the [access-control-client-api](https://docs.backbase.com/extranet/technical-docs/endpoints/2020.11/dbs.html#specs/access-control-client-api.html) create an audit record when the:
*   Request is received
*   Service responds
*   Request fails



Limits
=================

You use Limits to mitigate exposure risk for customers and financial institutions. Limits extends Entitlements Services by imposing financial restrictions on payments that a bank customer can create or approve.

Limits can connect with Approvals to support use cases where creating or updating a limit requires an approval from an authorized user. This feature is turned off by default. Developers can enable it in the configuration of Limits.

DBS, custom services, and your core banking system should call Limits API on specific payment events. These events include the following:
*   Creation
*   Intermediate approval
*   Final approval
*   Rejection
*   Failure
*   Cancellation

On creation, intermediate approval, and final approval, limits are consumed. On rejection, failure, and cancellation, limits are rolled back partially or in full.

![limits payments approvals](https://community.backbase.com/documentation/entitlements/2-19-3/images/puml/dbs/limits_payments_approvals.svg?ver=2.19.3)

By default, Limits is an add-on to Entitlements Services that your users utilize through the widgets in the Entitlements Widget Collection. However, you can integrate Limits with a third-party payments provider or core banking system.

Global and customer limits
--------------------------

Set up the following types of limits based on their scope:

### Global limits

Blanket limits created by bank employees on the bank level for all customers. Once created, global limits are effective immediately for all existing and newly created relevant entities. The [Manage Global Limits](https://community.backbase.com/documentation/entitlements/2-19-3/entitlements_understand) business function is required to handle global limits. When a customer submits a payment that breaches one or more global limits, Limits produces a breach report explaining which limits were violated in detail. Global limits are enabled by default. To disable global limits, switch them off in the [configuration](https://community.backbase.com/documentation/entitlements/2.19.3/reference_limits).

### Customer limits

Specific limits created by bank customers or employees per individual relevant entity. Customer limits can be regular or shadow.

*   **Regular customer limits**: The regular type of limits created by bank customers or bank employees. When a customer submits a payment that breaches one or more regular limits, Limits produces a breach report explaining which limits were violated in detail. The [Manage Limits](https://community.backbase.com/documentation/entitlements/2-19-3/entitlements_understand) business function is required to handle regular limits.

*   **Shadow customer limits**: An additional precaution set up by bank employees. When a customer submits a payment that breaches one or more shadow limits, a breach report is produced. Such breach report does not disclose the existence of the shadow limit; instead, it prompts the customer to contact an authorized employee for clarification. The full report is persisted in Entitlements Services for future reference by the authorized employee. The [Manage Shadow Limits](https://community.backbase.com/documentation/entitlements/2-19-3/entitlements_understand) business function is required to handle shadow limits.

    Bank employees check whether a new customer limit will be consistent with the existing shadow limits using [Limits soft check](https://community.backbase.com/documentation/entitlements/2.19.3/limits_soft_check) prior to limit creation.


There is no hierarchy between customer and global limits. The capability consumes the ones associated with a smaller amount first.

Transactional and periodic limit bounds
---------------------------------------

Global and customer limits can have the following bounds:

#### Transactional bound

Restrict the maximum amount of a single payment.

#### Periodic bound

Restrict the expenditure over a certain period.

Define the periods for limits with periodic bounds in one of the following ways:

*   Fixed periods: Daily, weekly, monthly

*   Rolling periods: 24 hours, 7 days, 30 days

    Rolling periods are independent from the start of the day, week, or month. For each payment, Limits checks if the user has consumed their limit in the rolling period preceding the payment execution date and time.

    For example, if the rolling period is 24 hours, when the user initiates a payment, Limits checks all payments initiated in the preceding 24 hours. If the user has already consumed their payment limit in that period, the payment is rejected.


### Fixed periods

The default fixed time periods for limits are the following:
* Daily: Starting at 00:00 and ending at 00:00 the following calendar day.
* Weekly
* Monthly
* Quarterly
* Yearly

You can change default start of the week or year, or add custom time periods such as bi-weekly using the system configuration. There are logical constraints to these limits. For example, weekly limits should be greater than daily limits. The bank time zone is used for Limits, the user time zone is ignored.

### Rolling periods

Fixed periods correspond to rolling windows as follows:
* Daily: 24h
* Weekly
* Monthly
* Quarterly
* Yearly

You can also use custom periods if you are using rolling limits.

To set up and apply rolling periods, you have to [switch them on in the configuration](https://community.backbase.com/documentation/entitlements/2.19.3/reference_limits).

Limits entities
---------------

Using the Entitlements Widget Collection widgets or implementing API requests, you can set up limits on the following entities:
**Global limits**
- Global user
- Global legal entity
- Global service agreement

**Customer limits**
- User
- User in service agreement
- Legal entity
- Legal entity in service agreement
- Service agreement
- Job role, business function, privilege, and service agreement
- Job role, business function, privilege, service agreement, and user

Limits consumption
------------------

Both global and customer limits are consumed by the capability on user interaction. For example, a user’s personal limit on payment creation is consumed when the user submits a new payment. Respectively, the user’s personal limit on approving a payment is consumed when the user approves the payment.

The base configuration of what limits are consumed on payment entry, intermediate approval, and final approval is illustrated in the table below. However, you can change the configuration so that a different combination is consumed.


Limit currency settings
-----------------------

The default limit currency is a global setting. Global limits can be set only in the default currency. However, you can set individual customer limits in a non-default currency. Limits supports multiple currencies.

When the payment currency and the limit currency are different, Limits send a request to the currency conversion service in order to validate the payment amount against the amount in the limit currency. See [Implement currency exchange service](https://community.backbase.com/documentation/entitlements/2.19.3/implement_currency_exchange_service) for setup.

You use the following currency conversion strategies:
*   Ask-always: Convert payment to the limit currency every time, default strategy.    
*   Ask-once: Convert only if no conversion was previously made for this payment and limit currency pair. Converted value is reused from earlier conversions.

Extending Limits
----------------
Using Limits extensions you can:
*   Add custom validation to Limits check and consumption requests.    
*   Enrich the breach reports returned when a limit is exceeded.    
*   Create custom entities for customer limits.



Understand Approvals
====================

Approvals works in conjunction with any process that requires the approval of one or more users, before it is allowed to continue. For instance a payment order which has to be reviewed before it can be executed, or changing the account number of a contact. These examples are provided out of the box, but Approvals can integrate with any process.

Approvals allows for very fine grained configuration to set up almost any scenario you can think of. It is possible to require one approval for a contact but two for payment orders. Or one approval for payments up to €500 and two approvals for amounts greater than €500.

It is also possible to require two approvals, where one of them has to be given by a manager. Or you can make an exception for your payroll accounts to require more approvals than other regular accounts.

Approvals are evaluated in real time and on the fly. Changes made to the approvals setup will have immediate affect, meaning the new setup will apply immediately. Every time a user approves something, the applicable setup is evaluated and the status will be changed accordingly. Was it the final approval? Then the payment order can be send to core or the contact updates can be applied, for instance.

Besides approving and rejecting, Approvals also provides the ability to filter for items that requires approval of the user. Allowing for useful overviews for the user. When the details of a certain item are being displayed to the user, it is also possible to show the approval log to the user. This shows the user who approved or rejected it and when.


Approval process
----------------

The approval process involves the following steps:

1.  Another capability initiates the approval process for an **item**.

2.  Approvals creates the **approval request** for the item that requires approval. During the approval process, the approval request goes through various **approval states**.

3.  Approvals checks whether an applicable **policy** exists. The applicable policy defines the criteria that need to be met for the approval request to be fully approved.

4.  If the item is a payment, Approvals checks whether the policy and the payment have the same currency. If the currencies differ, Approvals first calls the exchange-integration-service to convert the payment to the currency of the policy, before applying the policy.

5.  The **approval level** assigned to a users' job role determines what the user can approve.

6.  One or more users approve or reject the approval request. For each action, Approvals creates a **record** to store the action.


The concepts in the approval process are explained in detail below.

Concepts
--------

The concepts in Approvals are as follows:

*   **Item that requires approval**

    This is the item that has been created, changed, or deleted, which requires approval. The item belongs to another capability that has initiated the approval process. Examples are: a payment order or a contact.

*   **Approval request**

    The approval request is created when another capability initiates an approval process. During the approval process, the pending approval request is either rejected, approved or canceled.


*   **Approval states**

    The states of the approval request are: Pending, Approved, Rejected, Cancelled

*   **Policies**

    Policies define the criteria that need to be met for an approval request to be fully approved. The system does not provide any policies out of the box.

    Only one policy applies to an item. Approvals cannot handle items without a policy. For those cases where no approval is required, setup a zero-approval policy, see [configure approvals](https://community.backbase.com/documentation/entitlements/2.19.3/approvals_configure).

    The bank sets the policies and they apply system wide, but they do not have to apply equally to every user. Retail users or SMEs with only one user may require zero or one approval, while the larger corporate clients with multiple users may require two approvals or even more. Which policy applies to what is determined by the policy assignment.

*   **Approval levels**

    When defining a policy, you may want to distinguish the approval of different groups of users. For example, to give the approval of a CFO more weight than the approval of an employee. Or to require the approval from different departments. An approval level indicates a group of users.

    Examples of polices that use approval levels:
    *   One approval from department X and one approval from department Y
    *   One approval from an employee and one approval from a manager
    *   Two approvals from an employee or one approval from the CFO


    The bank creates the approval levels and they apply system wide. The financial administrator of the customer assigns each approval level to a job role. Based on the job roles the user has, and the policy that applies, Approvals determines whether the user is allowed to approve or reject the approval request.

    A job role can only have one approval level assigned. However, because a user can have multiple job roles, multiple approval levels may apply. Therefore, the approval levels are ranked. In case more than one approval level is applicable, Approvals uses the approval level with the highest rank.

*   **Record**

    The record keeps a detailed note of an action that was taken on the approval request. Approvals creates the record when a user approves or rejects an approval request. It holds the details about who, when and what action was taken and the optional comment that the user left while approving or rejecting.


Approval flow in Access Control
-------------------------------

The following flows are integrated with Approvals:

*   **User permissions assignment**

*   **Account group management**


Access Control integrates with Approvals to provide more control over changes made to user permissions and to manage account groups flows. When enabled, assigned permissions or account group changes are not immediately active, but go in pending state until the assignment or account group change is properly approved by a number of approvers with sufficient permissions. The required permissions for an approver and the number of approvers, depend on the assigned approval policy to **Assign Permissions** or **Manage Data Groups** business function. Meaning, if the approval policy requires two approvers of level A, then two different users assigned with a job role with Approval Level A and an assigned **Assign Permissions** or **Manage Data Groups** business function with `view`, `approve` privilege are required to approve the pending change, for example:

*   Business function: **Manage Data Groups** is assigned with approval policy **A + B** in the service agreement SA1.

*   An administrator creates a new account group, and the action results in creating a pending account group, that requires two approvers for a proper approval process, one approver of level A, and another one of level B.

    *   First approver is a user Sarah assigned with the following permissions:

        *   Job role of approval level A, containing Manage Data Groups business functions with `view`, `approve` privileges


    *   Second approver is a user Mary assigned with the following permissions:

        *   Job role of approval level B, containing Manage Data Groups business functions with `view`, `approve` privileges



*   The created account group is approved and becomes active, after both Sarah and Mary approve the pending request.


You cannot update the created account group while the current changes are pending. This is also true for the user’s permissions.

For more information, see [Prepare Access Control for using the Approval flow](https://community.backbase.com/documentation/entitlements/2.19.3/prepare_accesscontrol_approvalflow).
