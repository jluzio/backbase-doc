# DBS

## When to use DBS
* When direct integration to the core systems is not possible - e.g. transaction search
* When extra data needs to be stored that the core systems do not know about - e.g. contracts and avatars
* When extra business logic is needed - e.g. entitlements
* When the core systems is not always available - e.g. payments processing

## OOTB capabilities

Access Control
=========================

When exposing digital banking business functions over public channels, a fundamental part of the solution is to control who has access to what and what can they do with it. You use Access Control to allow and restrict user access to resources. Examples of resources are transactions, contacts, and accounts.

Access to a resource means that the user is able to take one of the following actions:
*   View the resource
*   Create, update, or delete the resource
*   Approve another user to access the resource

Access Control in Backbase Platform governs and enforces the data a user has access to, the business functions he or she can perform on that data, and the additional constraints that apply when performing these business functions. For example, Access Control manages which accounts a user is allowed to view, and whether a certain user is allowed to approve a payment order.
> (continued on Entitlements)


Account Statements
=============================

An account statement is a document issued by a financial institution that lists transactions for an account over a given period. With banks moving toward digital banking, the account statements document is changing from print to a digital document type (PDF) and to structured document types that allow importing into accounting software. Typical structured document types include CSV, XLSX, and XML.

Account Statements in Backbase Platform always works in conjunction with the core banking system. Account Statements assumes there is a fixed list of documents available to download for a specific account. The functionality provided by Account Statements is to show a list of account statement documents, and the ability to download a specific document.

As an option, Account Statements supports categories if the core banking system provides this information.

When listing statements, Account Statements checks if the user has access to the selected account within the service agreement they are currently using.

DBS only provides an integration layer and no persistence. The core banking system provides the documents.


List and download account statements
------------------------------------

This functionality allows the user to do the following:

*   Retrieve a paginated list of available account statements.    
    *   Sort the list of account statements.        
    *   Filter the list on the following fields:        
        *   Account          
        *   Date from            
        *   Date to
        *   Category
*   Retrieve the list of available categories.
*   Download an account statement specified by its ID.



Actions
==================

Use Actions to send notifications to bank users (customers or employees) via DBS Notifications, email, SMS messages, or push notifications when a business event occurs. For example, _if account balance goes below 100, send an email to the account holder_.

Business scenarios for notifications are defined in action recipe specifications. You can [define your own](https://community.backbase.com/documentation/DBS/2.19.3/extend_transactions_event_handler) or use out-of-the-box specifications that come pre-integrated with other DBS capabilities.

For example, notify a user when:
*   A new transaction has occurred
*   A business or retail account has low balance
*   A new message is received
*   A new unassigned conversation is created
*   A new reply in assigned conversation is received
*   A payment status is updated
*   A contact status is updated
*   A savings goal is completed
*   Limits are consumed
*   An account statement is ready for preview or download


Manage notification preferences
-------------------------------

Notifications are delivered to bank users in real time or as a digest.

**Real-time notifications**

Actions sends a notification via the specified channel every time when the corresponding business event occurs. For example, a bank customer receives a text message whenever a new transaction is made on their current account. Real-time notifications are the default option.

**Digest notifications**

Actions accumulates all occurrences of business events over the previous period and delivers a single digest notification at the scheduled time. For example, at 9 AM every Monday a bank customer receives an email with all transactions made on their current account over the previous week.

Action components
-----------------

An Action is a combination of the two following components:

*   [Action recipe specification](https://community.backbase.com/documentation/DBS/2-19-3/actions_understand#action_recipe_specification): An action blueprint created by the developer for _User_ actions and _System_ actions.

*   [Action recipe](https://community.backbase.com/documentation/DBS/2-19-3/actions_understand#action_recipe): A particular instance of the blueprint implementation created by the bank customer or created by Actions using the default values from the specification.


### Action recipe specification

Use action recipe specification to define the business case that an action handles. This specification is the blueprint for the user’s action recipe. It defines the following components for the future recipe:

*   **Action type**

    This defines whether the specification is for a _User_ or a _System_ action.

    *   **User actions**: Created by bank users. For example: _notify me when I receive a transaction with an amount higher than 1,000_.

    *   **System actions**: Defined by developers in the backend system. Bank users cannot disable, enable, change, or remove these. For example: _send an email to the Compliance Department for each transaction with an amount higher than 20,000_.


*   **Event**

    This defines what backend system event triggers the action. DBS capabilities already emit some events based on business logic. However, you can emit events from any custom service. You can also use HTTP or JMS to emit events from third-party applications or core banking systems.

    For low account balance and contact update specifications, Actions checks the emitted business event for a service agreement ID. If this ID is present, Actions generates a notification with such ID included. The [Notifications](https://community.backbase.com/documentation/DBS/2.19.3/notifications_understand) DBS capability then uses this ID to display only relevant notifications to customers.

*   **Action**
    The following components are defined for every action:

    *   Adapter
        The adapter is the channel through which the action is delivered. The following adapters are supplied out of the box:
        *   DBS Notifications
        *   Mobile SDK Push Notifications
        *   SMS - pluggable with your provider of choice
        *   Email - pluggable with your provider of choice

    *   Notification template
        The action recipe specification must define at least one notification template for every delivery channel. This is known as the fallback notification template. In most of the cases, actions use custom templates rather than the fallback template.

*   **Digest availability**
    This defines whether bank users can set up digest notifications instead of real-time ones.

*   **Recipe defaults**
    The default values and channels that are used to create a recipe when a user does not provide custom values.


### Action recipe
The action recipe implements the action recipe specification. It defines the following:
*   The triggered actions: Which of the actions defined in the specifications are triggered by this action recipe.    
*   Additional optional data, such as:
    *   The arrangement ID that the action recipe applies to.        
    *   The amount that triggers the action.



Audit
================

There are many reasons to audit business processes, for example:

*   Security, such as detecting and investigating malfeasance, fraud, or intrusion.

*   Evidence gathering, to support legal action.

*   Compliance with regulatory standards such as PSD2, Sarbanes Oxley, GDPR, or tax legislation.


Backbase auditing provides a comprehensive insight into the history of business activity, as recorded in an audit log. This business activity includes:

*   User activity - “who did what and when”, for activities such as authentication and deletion.

*   Lifecycle events - for example, payment status changes.


Note

This auditing doesn’t include system logging. For information on how your services can use effective system logging, see the [Service SDK logging reference](https://community.backbase.com/documentation/ServiceSDK/11.3.0/service_sdk_ref_service_sdk_starter_logging).

The DBS Audit capability is designed around the concept of the audit log - a database of records to record business operations. Audit users with the appropriate entitlements can publish records to and query from this database.

The Audit capability also enables other DBS and Identity Services capabilities to publish audit records, out of the box. For more information on these audited operations, see the following sections:

*   [DBS audited operations](https://community.backbase.com/documentation/DBS/2-19-3/audited_operations)

*   [Identity Services audited operations](https://community.backbase.com/documentation/identity/1.7.2/audited_operations_identity_services)



Contacts
===================

Contacts is a capability to manage contacts or beneficiaries to whom users make payments. Users can view, create, edit, and delete contacts.

Changes to contacts are audited, except for changes made via the integration layer.

You can enable an authorization mechanism to control creation, modification, and deletion of contacts in a shared setup. If enabled, it may require one or several users to approve any change requests before new contacts or changes to existing contacts become active.

Manage contacts
---------------

This functionality allows the user to do the following:
*   Retrieve contact list
*   Create a contact
*   Retrieve a specific contact
*   Change a specific contact
*   Delete a specific contact
*   Retrieve the IBAN restrictions for all countries
*   Retrieve the IBAN restrictions for a specific country


Approval flow for changes to contacts
-------------------------------------

If Entitlements Services is purchased, Contacts can integrate with [Approvals](https://community.backbase.com/documentation/entitlements/2.19.3/approvals_understand) to have an approval flow for changes to contacts. This allows the user to do the following:

*   Manage my approval requests
    As a user who made a change to a contact that requires approval:
    *   View the list and details of the current user’s own create, edit, and delete requests that have been submitted for approval.
    *   Cancel a request that is pending for approval.
    *   Delete a request that was rejected by an approver.

*   Approve/reject approval requests
    As an approver:
    *   View the list and details of all contact requests that require authorization.
    *   Review the details and approve or reject a request.

Contacts in DBS connects with Approvals in Entitlements Services that offers the approval functionality.



External Account Aggregation
=======================================

External Account Aggregation, or EAA, helps retail banking customers to have an overview of their finances by aggregating all bank accounts they have in multiple financial institutions into a single app. This enables viewing transactions, account balances, and reports that aggregate financial data from multiple bank accounts. External Account Aggregation in Backbase Platform provides an overview of the bank accounts customers connected to their account in the platform.

External Account Aggregation in Backbase Platform supports the following functionalities:
*   **View financial institutions**    
    Users view financial institutions that may be connected to Backbase Platform to aggregate account data.



Employee
===================

For bank employees, one of the most important jobs to be done is providing world-class customer service. Seamless customer service is what bank customers expect when engaging with their financial services, yet bank employees continuously face challenges with outdated tools and unnecessary workarounds. Dealing with multiple legacy processes and systems, lacking a single overview of their clients. Furthermore, operational back-office processes of traditional banks are still heavily dependent on manual efforts that require large workforces, high costs and a great deal of time. Inefficient back-office systems not only impact employee productivity but also hurt the bank’s bottom line.

The DBS capability Employee is built around core customer support journeys. Employee utilises functionality from across the Backbase Platform and leverages other Backbase DBS services to help provide a full view of a customer’s information and the products and services they are using. For example, as a bank employee you can retrieve the list of all service agreements the customer is participating in and see a customer’s [arrangements](https://community.backbase.com/documentation/DBS/2-19-3/glossary#glossary_arrangement) within the context of a specified service agreement.

Bank employees need the correct entitlement to be able to view a given customer’s account details. Employee depends on [Access Control](https://community.backbase.com/documentation/DBS/2-19-3/entitlements_understand) for this purpose. For more information on how to set up customer access, see [Implement customer segmentation for Employee](https://community.backbase.com/documentation/DBS/2-19-3/employee_segmentation).

The DBS capability Employee is built specifically to provide functionality to support the [Employee App](https://community.backbase.com/documentation/employee_web_app/2020.11/index).

List service agreements
-----------------------
This functionality allows the user to do the following:
*   Retrieve a paginated list of service agreements available to a specified bank customer.
    *   Filter the list by service agreement name.

View arrangements
-----------------
This functionality allows the user to do the following:
*   Retrieve a paginated list of arrangements available to a specified bank customer within the context of a given service agreement.
*   Retrieve the details of an arrangement available to a specified bank customer within the context of a given service agreement.

View transactions
-----------------
This functionality allows the user to do the following:
*   Retrieve a paginated list of transactions available to a specified bank customer within the context of a given service agreement.
    *   Filter the list by a specific term or list of arrangements.



Messages
===================

Messages is a functionality that allows the financial institution to interact with their customers in a secure environment.

The typical process with Messages is that the customer creates a message, selects a topic, composes the message, and submits it. Messages automatically resolves the message recipient, an employee of the financial institution, based on the selected topic. This conversation continues as the two parties reply to each other.

Send and receive messages
-------------------------

The scope of this functionality is different for bank customers and employees. The following is available for bank customers:

*   Create and send a message to the financial institution with a topic, subject, and body.
*   Attach files to messages and delete attachments before sending.
*   Save a message as a standalone draft or a draft attached to a conversation.
*   View all conversations with the financial institution.
*   View a specific conversation.
*   Reply to a conversation unless it is marked read-only by the sender.
*   Delete a conversation unless it is marked non-deletable by the sender.
*   Mark conversations as read or unread.
*   View count of unread conversations.


Bank employees require Manage Messages or Supervise Messages business function from [Access Control](https://community.backbase.com/documentation/entitlements/2.19.3/entitlements_understand) to send and receive messages from bank customers. The following is available for bank employees:

*   With Manage Messages or Supervise Messages business function:
    *   Create and send a message to the customer with a topic, subject, and body.
    *   Attach files to messages and delete attachments before sending. See [Messages reference](https://community.backbase.com/documentation/DBS/2.19.3/message_center_reference) for allowed attachment formats.
    *   Mark a message as important, read-only, or non-deletable.
    *   View the following tabs separately:
        *   All conversations.
        *   Resolved conversations.
        *   Conversations assigned to the employee, with the counter and the highlighting of unread messages.
        *   Conversations resolved by the employee.
        *   Unassigned conversations within the topics that the employee is subscribed to, with a counter.
    *   Mark conversations as read or unread.
    *   View a specific conversation.
    *   View **sent** and **inbox** messages separately.
    *   Filter conversations by topic, customer name, and date.
    *   Set themselves as assignee of the conversation, or several conversations in a bulk; the status changes to `IN_PROGRESS`.
    *   Reply to a conversation. The employee is assigned and the status changes to `IN_PROGRESS`.
    *   Remove themselves as assignee from a conversation, or several conversations in a bulk; the status changes from `IN_PROGRESS` to `NEW`. If the status was `RESOLVED`, the status does not change.
    *   Change the topic of the conversation. The status changes from `IN_PROGRESS` to `NEW`, if the assignee does not have access to the new topic, or remains unchanged.
    *   Mark a conversation or several conversations in a bulk as resolved or unresolved. The status changes from `IN_PROGRESS` to `RESOLVED` or vice versa.

*   Only with Supervise Messages business function:
    *   Assign a conversation or several conversations in a bulk to another employee. The status changes to `IN_PROGRESS` if the conversation was unassigned or remains unchanged.
    *   View and search employees subscribed to the same topic.

Send mailouts
-------------

Mailouts are messages sent to multiple recipients. Only bank employees can send mailouts, bank customers can neither send nor reply to mailouts. Bank employees require Manage Messages or Supervise Messages business function from [Access Control](https://community.backbase.com/documentation/entitlements/2.19.3/entitlements_understand) to send mailouts to bank customers. The following is available to bank employees within the scope of this functionality:

*   Create and send a mailout with a topic, subject, and body.
*   Mark a mailout as non-deletable or important.
*   Upload a list of recipients for the mailout as a CSV file. The CSV must contain the recipients' internal IDs in the `UserBBID` column. For example:
        UserBBIDuser111user222user333user444

*   Upload and preview an HTML template to be sent as a mailout. See [Messages reference](https://community.backbase.com/documentation/DBS/2.19.3/message_center_reference) for required attachment formats.
*   See a list of sent mailouts and individual mailout details.
*   See mailout statuses

*   Delete mailouts with any status, except of already deleted ones. Mailouts that have already been delivered are removed from the recipients' inbox.


Manage topics
-------------

Message topics are used to group messages on similar subjects, such as **Loans** or **News**. When a bank customer sends a message with a specified topic, the bank employee subscribed to this topic receives and handles this message.

Usually a dedicated bank employee, such as an admin, handles the creation and assignment of topics. The [Manage Topics business function](https://community.backbase.com/documentation/entitlements/2.19.3/entitlements_understand) is required to view, create, and delete topics and to assign or unassign employees from them.

The following is available to bank employees within the scope of this functionality:
*   View existing topics.
*   Edit topic names.
*   Create new message topics.
*   Delete topics.
*   Manage employees' subscriptions to topics.

Employees can use regular message topics when sending mailouts. However, if a topic was created specifically for mailouts, such topic is not available to customers and employees when sending regular messages. It is also impossible to subscribe employees to such topic.

Message statuses
----------------

Conversations have a workflow with the following statuses:

*   `NEW`: The conversation is created or reopened and no financial institution employee has replied or has assigned themselves.
*   `IN_PROGRESS`: The conversation is assigned to a financial institution employee.
*   `RESOLVED`: The assignee has marked the conversation as resolved.

When the customer replies to a resolved conversation, the conversation is reopened and the status changes back to one of the following:
*   `NEW`: When there is no financial institution employee assigned.
*   `IN_PROGRESS`: When the conversation is assigned to a financial institution employee.

When the employee unassigns themselves from a conversation, the following happens to the status:
*   The status changes to `NEW`: When the conversation has not been resolved.
*   The status stays `RESOLVED`: When the conversation has been resolved.

When the employee changes the topic of the conversation, the status changes from `IN_PROGRESS` to `NEW`.

Required message setup
----------------------

To use Messages correctly, you need to set up the following:

*   **Employee anonymization**: Replace the real employee name with custom text - for example, Customer Service - by setting the corresponding property in the Message Center presentation service. See [Messages references](https://community.backbase.com/documentation/DBS/2.19.3/message_center_reference).

*   **Message topics**: Used to group messages on similar subjects, so that they can be routed to dedicated groups of financial institution employees. Messages dynamically resolves recipients based on message topics that employees are subscribed to. The setup is managed by a user with Manage Topics business function.

*   **Topic subscriptions**: Used to assign employees to certain topics so that messages within those topics are routed to them. Each employee can be subscribed to several topics. The setup is managed by a user with Manage Topics business function.

*   **Encryption**: Used to secure messages. The configuration includes a password that Messages uses together with a key stored in an environment variable on the host machine to encrypt and decrypt messages.



Notifications
========================

Notifications sends instant short web messages to individual users or groups of users.

The notifications can originate from the following sources:
*   Another capability or underlying system
    The capability sends a `POST` request to the Notifications API in order to send the notification. For example, Actions sends a notification to a bank customer when a payment of over $1,000 is received. Supports _user_ and/or _customer_ [Target groups](https://community.backbase.com/documentation/DBS/2-19-3/notifications_understand#notifications_target_groups).

*   A custom frontend interface for bank employees
    Note
    The functionality to create notifications by employees (for global, customer, or user target groups) is available only via Digital Banking Services API. There are **no widgets** that support this functionality.

    The bank developer implements DBS APIs to create a custom frontend, which can then be used by other bank employees to send notifications to bank customers. For example, a bank employee sends a notification about changes in branch work hours. To send a notification, the employee must have the [Manage Notifications](https://community.backbase.com/documentation/DBS/2-19-3/entitlements_understand) business function assigned in Access Control. Supports all [Target groups](https://community.backbase.com/documentation/DBS/2-19-3/notifications_understand#notifications_target_groups).


Receive notifications
---------------------

Bank developers or employees send notifications via a custom frontend interface. Within the scope of this functionality, they can:
*   Send a notification to one or more recipients
    *   Individual users (_User_ target group)
    *   Users belonging to one or more legal entities (_Customer_ target group)
    *   All users (_Global_ target group)
        Global notifications created by employees of the financial institution are subject to an approval flow.
    Notifications supports multiple languages and a fallback message when localization doesn’t match the user’s locale.

*   Schedule notification

*   Retrieve a paginated list of notifications for a user, including notifications on the legal entity level and on the global level, with the following filter options:
    *   Date
    *   Severity level
    *   Origin/category
    *   Read/unread status

*   List latest unread notifications with filtering by date, severity level, and origin/category

*   Retrieve the number of unread notifications with filtering by severity level and origin/category

*   Retrieve the number of unread notifications grouped per origin/category

*   List all available origins/categories

*   Mark a notification as deleted

*   Mark a notification as read or unread

*   Automatically mark expired notifications as read


### Notification types

Depending on the configuration and widgets used, a bank user can view the following:

*   A list of all notifications, excluding the ones marked as deleted. Retrieved by a `GET` request to the following endpoint:

    [https://ips\_host:ips\_port/gateway/api/notifications-service/client-api/v2/notifications](https://docs.backbase.com/extranet/technical-docs/endpoints/2020.11/specs/notification-client-api.html#api-Notifications-getNotifications)

*   Unread notifications. Retrieved by a `GET` request to the following endpoint:

    [https://ips\_host:ips\_port/gateway/api/notifications-service/client-api/v2/notifications/stream](https://docs.backbase.com/extranet/technical-docs/endpoints/2020.11/specs/notification-client-api.html#api-Notifications-getNotificationsStream)

    Notifications can be of the following types:

    *   **Sticky** (the `expiresOn` parameter is set): The notification is displayed to the user until it expires, which is a relatively long period of time (hours or days), or until it is dismissed by the user. You need to additionally configure the corresponding widget in Experience Manager to allow users to dismiss sticky notifications. This type of notifications is used for major important announcements, such as scheduled downtime. Several sticky notifications can be displayed at the same time. When a user dismisses the sticky notification or the notification expires, it appears in the list of all notifications marked as read.

    *   **Non-sticky** (the `expiresOn` parameter is not set): The notification disappears automatically, usually after a few seconds, or is dismissed by the user. Used for quick updates, such as notifying about payment status changes. Several non-sticky notifications can be displayed at the same time. When the non-sticky notification disappears or is dismissed by the user, it appears in the list of all notifications marked as read.


    Individual notifications can be scheduled by setting the `validFrom` parameter to the desired date and time.


### Notifications severity and origin/category

Notifications have one of the following severity levels indicated by an icon:

*   **Success**: Reports a successful action, for example:
    _You have reached your savings goal!_
*   **Information**: reports neutral information unrelated to a user action, for example:
    _Your salary payment has arrived._
*   **Warning**: Reports important information unrelated to a user action that may require a user action, for example:
    _Your three-month term deposit will mature on 31/03/2021, click here to extend the contract._
*   **Alert**: Is one of the following:
    *   Reports important information that requires immediate user action, for example:
        _The terms and conditions of the Bank have been changed. You have to agree to them before you can continue using the app._
    *   Reports that an action failed or resulted in an error, for example:
        _Your loan application has been rejected._


Notifications have an origin/category that allows filtering and grouping of notifications. Notifications does not force any categorization, but allows the service that triggers the notification or the employee that creates the notification to set the origin/category. Examples set by DBS services are: _limits_, _transactions_, and _actions_.

### Target groups

Notifications in Digital Banking Services offers convenient target groups that allow sending the notification to the right people:

*   **User**: Sends the notification to one or more specific users. Notifications allows sending _User_ notifications in one of the following ways:
    *   All new notifications that are relevant to a user regardless of the selected [context](https://community.backbase.com/documentation/DBS/2-19-3/glossary#glossary_context).
    *   Only the notifications that are relevant for the context the user is working in, and those that are not associated with any context.
*   **Customer**: Sends the notification to users belonging to one or more legal entities.
*   **Global**: Sends the notification to all users.

Approval flow
-------------

Note

The functionality to create notifications by employees (for global, customer, or user target groups) is available only via Digital Banking Services API. There are **no widgets** that support this functionality.

Notifications integrates with Approvals to ensure global notifications are approved before being sent to all users. This feature is configurable: the target groups for which an approval flow applies can be adjusted or disabled entirely. By default, only global notifications have an approval flow. The approval flow only applies to notifications created manually by bank employees.

The following approval statuses are available for notifications for which an approval flow applies:
*   Pending
*   Approved
*   Rejected
*   Cancelled

The flow of the approval status is simple: the approval status changes from pending to one of the other statuses. No other status changes are supported.



Payments
===================

A payment is an international banking term that refers to a directive to a bank or other [financial institution](https://en.wikipedia.org/wiki/Financial_institution) from a bank account holder instructing the bank to make a payment or series of payments to a third party.

Payments in Backbase Platform allow the user to transfer money from one account to another. Users either select the account to transfer to from their contact list, their own accounts, or they specify new account data. Users also have the option to save new account data to their contact list.

You can easily [add any payment type](https://community.backbase.com/documentation/DBS/2-19-3/payments_configure_custom_payment_type) that your system requires, or use one of the payment types that are provided out of the box.

Users can schedule payments for one-time execution or recur during a defined period or for a defined number of times. Users can also make batch payments by uploading a file.

Backbase Platform always works in conjunction with a core banking system for the actual processing of a transaction. The widget that initiates the payment sends the payment order to the backend, which is either Backbase’s own DBS or a third-party system. The payment order ends up in the core banking system that processes the payment order and, if successful, sends the payment to the beneficiary. DBS adds a backend layer on top of the core banking system that offers 24/7 banking through store-and-forward. Payments in DBS always accepts payment orders and sends them to the core banking system. If the core banking system is not available, Payments stores the payment orders and forwards them to the core banking system when it is available again.

Users need the correct entitlement to be able to initiate a payment order. Payments depends on [Access Control](https://community.backbase.com/documentation/DBS/2-19-3/entitlements_understand) for this purpose. Optionally, Payments relies on [Limits](https://community.backbase.com/documentation/entitlements/2.19.3/limits_understand) and [Approvals](https://community.backbase.com/documentation/entitlements/2.19.3/approvals_understand), that are part of Entitlements Services, to restrict the maximum amount that users can pay and to have an approval workflow for business banking users. For more information, see [Payment approvals and limits](https://community.backbase.com/documentation/DBS/2-19-3/payments_understand#understand_paymentlimits).


Note

In addition to Payments, Backbase also offers [Bill Pay](https://community.backbase.com/documentation/DBS/2-19-3/billpay_understand) for bank users to pay their electronic or paper bills.

Initiate SEPA payment
---------------------

Single Euro Payments Area (SEPA) is an effort of the European Union to harmonize euro payments between banks. A credit transfer is an electronic payment from one bank account to another. The SEPA Credit Transfer (SCT) scheme makes the transfer of money in Europe easy and convenient (source: [European Payments Council](https://www.europeanpaymentscouncil.eu/what-we-do/sepa-credit-transfer)).

Users initiate a SEPA credit transfer from their account to any other SEPA reachable account.

Users can save a payment as draft. Draft payments are manageable, see [Manage draft payments](https://community.backbase.com/documentation/DBS/2-19-3/payments_understand#manage_draft_payments).


Initiate US payment
-------------------

The United States have several electronic payment methods, such as an internal transfer, a US domestic wire transfer, an international wire transfer, and ACH payments.

DBS covers the following payment methods:
*   **US domestic wire transfer**: Payment from a US account to another US account (at a different financial institution)
*   **International wire transfer**: Payment from a US account to a foreign account
*   **ACH debit transfer**: Incoming money transfer (or direct debit) from a US account to another US account
*   **ACH credit transfer**: Outgoing money transfer from a US account to another US account
*   **A2A external transfer**: Payment between the user’s own accounts at different financial institutions


Internal transfer
-----------------

Internal transfer is the transfer between the accounts of the same bank customer in the same financial institution.


Manage draft payments
---------------------

The initiate payment functionalities support saving a payment as draft. Users can list their draft payments and they can edit, submit, or delete a draft payment.

The list of draft payments supports filtering and pagination. Filter options include amount range, execution date (single or range), next execution date (single or range), one-off or [standing order](https://community.backbase.com/documentation/DBS/2-19-3/glossary#glossary_standing_order)s and the frequency for standing orders.

Intracompany payment orders
---------------------------

Intracompany payment orders are payment orders where both the originator and counterparty accounts belong to the same legal entity. They are also known as intra-legal entity payment orders. Multiple payment types are supported out of the box. See [Payments reference](https://community.backbase.com/documentation/DBS/2-19-3/payments_reference) for the default configuration.

When a payment order is being validated or submitted for initiation, the accounts are checked. When the accounts share the same legal entity, the payment type is updated to the configured intra-legal entity payment type. The user also needs to have the privileges to create such a payment, otherwise it remains unchanged.

Custom payment types can also be used as intracompany payment types. After creating and adding the custom payment type, it can be configured in the payment type mapping configuration.

An intracompany payment order is validated using the same rules as its regular counterpart. Any custom validators that apply to the regular type automatically apply to the intracompany type as well.

Closed payments
---------------

Closed payments is a feature that allows financial institutions to restrict users so that they are only allowed to send payments to one of the existing accounts or contacts within the service agreement. Restricted users cannot add a new account as the beneficiary and initiate a payment.

Manage payment orders
---------------------

The initiate payment functionalities support scheduling a payment to be executed in the future, either as a one-off or as a [standing order](https://community.backbase.com/documentation/DBS/2-19-3/glossary#glossary_standing_order). The manage payment orders functionality lists these future payment orders and allows users to manage them.

Users have the following options to manage a payment order:
*   Search and filter the list of payment orders.
    Filter options include amount range, execution date (single or range), next execution date (single or range), one-off or [standing order](https://community.backbase.com/documentation/DBS/2-19-3/glossary#glossary_standing_order)s, the frequency for standing orders, status, and credit or debit payments (or other configured payment type groups).

*   Approve or reject one or multiple payment orders (available when Approvals, that is part of Entitlements Services, is enabled).

*   Cancel a payment order.

*   Delete a payment order.
    Deleting a payment order is only available when the following conditions are met:
    *   The payment order is not yet sent to the core banking system.
    *   Only the user that has initiated the payment order is able to delete the payment.
    *   The user has the privilege to delete payment orders.
    *   The payment order approval does not have any approval records (only applicable if approvals is enabled).


Payment templates
-----------------

A payment template is a copy of an existing payment order that speeds up creating similar payment orders in the future.

DBS has the following features for payment templates:
*   Create a payment template from an existing payment order
*   List payment templates
*   Delete a payment template


Batch payments
--------------

A logged-in user can do the following with the batch payment features in DBS:

### Manage batch payment files

*   Upload a batch payment file
    Payments checks whether the user is entitled to upload a batch file and parses the file to create payments from it.
    The following types of batch files are supported:
    *   [SEPA CSV format](https://community.backbase.com/documentation/DBS/2-19-3/payments_reference#sepa_csv_batch)
    *   [NACHA ACH batch format](https://community.backbase.com/documentation/DBS/2-19-3/payments_reference#nacha_ach_batch)
        A balanced ACH credit batch file contains per batch one debit payment that should match the total sum of the credit payments in the batch.
        The original records from uploaded NACHA balanced ACH credit batch files are stored and are available for integration services.

*   Cancel a batch upload in progress

*   Track the progress of the batch upload
    *   The status of the batch upload changes from `OPEN`, `UPLOADING`, `UPLOADED`, `VALID` to `DONE`, see [Batch file states](https://community.backbase.com/documentation/DBS/2-19-3/payments_understand#understand_batch_file_states).

*   Retrieve a list of completed file uploads that the user has initiated


### Manage batch payment orders

*   Retrieve a paginated list of batches within the service agreement in the context of the user

    Filter options include amount range, execution date (single or range), and status.

*   Track progress of the batch order
    *   The status of the batch order changes from `OPEN`, `CLOSED`, `VALID`, `ENTERED`, `READY`, `ACKNOWLEDGED`, `DOWNLOADING`, `ACCEPTED` to `PROCESSED`.
    *   Fail end states are `INVALID`, `CANCELLED` and `REJECTED`.
    *   The success end state is `PROCESSED`.

*   Cancel a batch order
    Canceling a batch order is possible if the following requirements are met:
    *   Payments checks whether the user is entitled to cancel a batch order.
    *   The batch order has the state `ENTERED`.
    *   The batch order has partially been approved by any approver.

*   Delete a batch order
    Deleting a batch order is possible if the following requirements are met:
    *   Only the initiator of the batch can delete a batch order.
    *   The batch order has the state `ENTERED`.
    *   The batch order has not yet been approved by any approver.

*   Approve a batch order
    *   Payments integrates with Approvals to offer an approval workflow for batches.
    *   Payments integrates with Limits to ensure the limits of the user are checked prior to approving and that limits are consumed when the user approves.


*   Retrieve status updates from the core banking system
    Payments connects to the core banking system to retrieve status updates. The core banking system can either reply directly or defer by actively sending status updates to Payments.
    The status update accepts the following information:
    *   **Bank status**: This is the status of the batch payment in the core banking system.
    *   **Reason**: States why the core banking system has accepted or rejected the batch payment. The reason can be in the form of a code, a text and an error description.

    In case the core banking system rejects a particular transaction, Payments instructs Limits to roll back the consumed limits for that transaction.


### Configure custom batch types

Add a custom parser and validator for batch types that are not available out of the box, see [Configure a custom batch type](https://community.backbase.com/documentation/DBS/2-19-3/payments_configure_custom_batch_type).

The original batch records from uploaded batch files with a custom configured batch type are stored and are available for integration services. This is not the case for custom batch types that were developed using Java. See [Access original batch records from an integration service](https://community.backbase.com/documentation/DBS/2-19-3/adopt_batchpayments#access_original_batch_records).

Payment approvals and limits
----------------------------

Payments works seamlessly with Entitlements Services to offer the following business banking features. The dependency on Entitlements Services is optional.

*   **[Limits](https://community.backbase.com/documentation/entitlements/2.19.3/limits_understand)**

    The ability to restrict bank customers in the maximum amount of payment orders that they are allowed to initiate within a specific time frame. If enabled and configured, each payment order consumes a part of the limit set for that bank customer. As an option, payments made to accounts within the same legal entity do not consume limits. This is configurable with the `backbase.limits.skip-intra-company.enabled` setting for Limits, see [Limits reference](https://community.backbase.com/documentation/entitlements/2.19.3/reference_limits).

    If the amount of a payment order exceeds the remaining limit for that bank customer for that time frame, Payments will not process the payment order further. The payment order stays in the `ENTERED` state. In such cases, the bank customer has the option to change the amount to fall within the remaining limit, cancel the payment order, or wait until a later time when his/her limits are reset. For example, a bank customer Paul is allowed to make payment orders of $1,000 per day. If his daily limit is already consumed, he waits until the next day and proceeds with making the payment.

    Limit consumption for specific cases:

    *   For future dated payment orders, Payments consumes the limit on the day the payment order was created. For example, Paul creates a payment order for €50 on 02 Jan 2020 with execution date 05 Feb 2020. Payments attempts to consume €50 from Paul’s limits on 02 Jan 2020.

    *   For [standing order](https://community.backbase.com/documentation/DBS/2-19-3/glossary#glossary_standing_order)s, Payments only consumes the limit once: on the day the payment order was created.
        Payments does not automatically create new payment orders for next execution dates of standing orders. If you want this functionality, integrate Payments with a core banking system that offers this.

    *   For batch payments, Payments consumes the total batch payment order amount. When individual orders fail, the limit is restored for the amounts of the failed orders.

    *   For future dated batch payments, Payments consumes the total batch payment order amount on the day the batch payment was created.


    Limits allows you to disable limit consumption for particular payment order states and payment types. See the `backbase.limits.disable-default-consumption-strategies.*` and `backbase.limits.exceptional-flow-strategies.skip-consumption-strategies` settings in [Limits reference](https://community.backbase.com/documentation/entitlements/2.19.3/reference_limits).

*   **[Approvals](https://community.backbase.com/documentation/entitlements/2.19.3/approvals_understand):**

    The ability to set up an approval workflow for payment orders. Bank customers with a specific entitlement either reject or approve a payment order, before Payments sends the payment order to the core banking system. Approvals integrates with Limits to offer approval limits, which is the total amount that a bank customer is allowed to approve in a specific time frame.


![limits payments approvals](./images/puml/dbs/limits_payments_approvals.svg?ver=2.19.3)

Payment transaction signing
---------------------------

Payments works seamlessly with Identity Services to offer [Transaction Signing](https://community.backbase.com/documentation/identity/1.7.2/understand_transaction_signing).

Note

From the Identity Services documentation:

To help protect transactions that are at higher risk of fraud or attack, banks can require customers to confirm the transactions by authenticating as an additional security step. For example, the bank can use Transaction Signing for online operations such as payments or changes to personal details. Transaction Signing increases the security of these operations as follows:

*   The confirmation process creates a record of the transaction details and shows that the authenticated user authorized the operation before it was carried out. You can use the [audit logs](#confirmation_audited_operations) for the confirmation to support non-repudiation.

*   The authentication process involves a signing operation that protects the authenticity and integrity of the transaction.

    If you are using the Auth Server as your identity provider (IdP), the authentication process produces a signed JWT containing a reference to the confirmation record. In this case, the signature on the JWT verifies the transaction’s authenticity and integrity. Each time an operation needs to be confirmed, the user is challenged so that a new signed JWT is produced. An attacker cannot change the confirmation ID in the JWT without invalidating the signature, which prevents an attacker from changing the transaction details in the confirmation record. To ensure that transaction details your capability has stored haven’t been tampered with, make sure they match the transaction details in the confirmation record.


Note

In Transaction Signing, transactions do not refer exclusively to financial processes. You can set up confirmations for any kind of online operation, from making a payment to changing a passcode or removing a registered device.

Payment states
--------------

A payment goes through a number of states during the processing of the payment order. The following diagram shows the state flow without cancellation:

![payment states](https://community.backbase.com/documentation/DBS/2-19-3/images/puml/dbs/payment_states.svg?ver=2.19.3)

The following diagrams show the states at which the user is able to cancel the payment order and how the states flow in those cases:

|   |   |   |
| - | - | - |
| ![payment states cancel entered](https://community.backbase.com/documentation/DBS/2-19-3/images/puml/dbs/payment_states_cancel_entered.svg?ver=2.19.3) | ![payment states cancel approval](https://community.backbase.com/documentation/DBS/2-19-3/images/puml/dbs/payment_states_cancel_approval.svg?ver=2.19.3) | ![payment states cancel pending](https://community.backbase.com/documentation/DBS/2-19-3/images/puml/dbs/payment_states_cancel_pending.svg?ver=2.19.3) |

The states in the Payments workflow are:

`CONFIRMATION_PENDING`
The user has entered a payment order via a journey/widget and now needs to confirm or decline the payment order through a transaction signing process. The payment order is not accessible through the client API when it is in this state.

`ENTERED`
The user has entered and confirmed a payment order. Payments has validated and enriched the payment order.

`CONFIRMATION_DECLINED`
The user has entered and declined a payment order. The payment order is not accessible through the client API when it is in this state.

`DRAFT`
The user has saved the payment order as draft.

`READY`
The payment order is ready to send to the core banking system. If Approvals is enabled, Payments has sent the payment order through the approval workflow and the payment order has been approved. If Limits is enabled, Payments has verified that the payment order falls within the limits set for the user.

`CANCELLED`
The user has canceled the payment order. This is a final state.

`ACCEPTED`
The core banking system has accepted the payment order. Only the integration service can set this state.

`CANCELLATION_PENDING`
The user has canceled the payment order after it was accepted by the core banking system.

`PROCESSED`
The core banking system has processed the payment order. This is a final state. Only the integration service can set this state.

`REJECTED`
The core banking system has rejected the payment order. This is a final state. Only the integration service can set this state.

The following states are part of Approvals and only apply if you have Entitlements Services and Approvals is enabled.

`PENDING`
The payment order requires approval.

`APPROVED`
An authorized user has approved the payment order.

`REJECTED`
An authorized user has rejected the payment order.

`CANCELLED`
The user has canceled the payment order.

This capability fulfills the Payment Initiation Service Provider scenario of [Payment services (PSD 2) - Directive (EU)](https://ec.europa.eu/info/law/payment-services-psd-2-directive-eu-2015-2366_en).

### Actions

Each payment order that you retrieve contains the `actions` property that indicates the actions available to the current user:

*   `APPROVE` - available when:
    *   Approvals is enabled.
    *   The current user has permissions to approve and reject the payment order.

*   `FINAL_APPROVE` - allows clients to notify the user the payment order will be processed further upon approval. Available when:
    *   The action `APPROVE` is present.
    *   The approval related to the payment order is pending the final approval.

*   `REJECT` - available when:
    *   Approvals is enabled.
    *   The current user has permissions to approve and reject the payment order.

*   `CANCEL` - available when:
    *   The payment order does not have one of these statuses: `DRAFT`, `PROCESSED`, `REJECTED`, `CANCELLED`, `CANCELLATION_PENDING`
    *   The user has the privilege to cancel payment orders.

*   `DELETE` - available when:
    *   The payment order status is `ENTERED`.
    *   The payment order is created by the user who is viewing it.
    *   The user has the privilege to delete payment orders.
    *   When approvals is enabled, the payment order approval does not have any approval records.

### Batch file states

A batch file goes through a number of states during the processing of the batch file.

[![batch file states](https://community.backbase.com/documentation/DBS/2-19-3/images/puml/dbs/batch_file_states.svg?ver=2.19.3)

The batch file states are:

`OPEN`
The batch process has started. This is the state after the first step of [initiating a batch payment](https://community.backbase.com/documentation/DBS/2-19-3/adopt_batchpayments#adopt_batchpayments_files).

Validation rulesa marked for the `on-file` phase are executed.

`UPLOADING`
The batch file upload has started. This is the state during the second step of [initiating a batch payment](https://community.backbase.com/documentation/DBS/2-19-3/adopt_batchpayments#adopt_batchpayments_files). When the upload is interrupted or has failed, the state changes to `FAILED`.

Validation rules marked for the following phases are executed:
1.  `on-batch-upload`: After parsing the file header.
2.  `on-batch`: After parsing a batch header.
3.  `on-batch-payment`: After parsing a payment.
4.  `on-batch-payment-addenda`: After parsing an addenda.
5.  `on-batch-payment-end`: After parsing all addenda of a payment.
6.  `on-batch-end`: After parsing the batch footer.

`UPLOADED`
The batch file is successfully uploaded and the batch file has passed the validations listed above.

The final validation rules marked for the `on-batch-upload-end` phase are executed.

`VALID`
The batch file is valid.

`DUPLICATE`
Payments recognizes a possible duplicate. The user decides to either proceed with this file or delete it after comparing it with possible other duplicate uploads.

`DONE`
The batch orders in the batch file have been processed. This is an end state.

`FAILED`
The batch file upload has failed or the batch file is invalid. This is an end state.

a The validation rules differ per batch type:
*   [SEPA CSV format](https://community.backbase.com/documentation/DBS/2-19-3/payments_reference#sepa_csv_batch)
*   [NACHA ACH batch format](https://community.backbase.com/documentation/DBS/2-19-3/payments_reference#nacha_ach_batch)
*   You can configure a custom batch type and define custom validation rules, see [Configure a custom batch type](https://community.backbase.com/documentation/DBS/2-19-3/payments_configure_custom_batch_type).



Personal Finance Management
======================================

Personal Finance Management, or PFM helps retail banking users to better manage their finances by providing insight into their financial situation with functionality such as spending analysis, budgeting, and saving goals.

PFM in Backbase Platform supports the following functionalities:

*   **Manage transaction categories**

    Users update and view transaction categories. Users assign a category to a transaction. Transaction categories in Backbase Platform support parent and child categories (one level).

*   **Budget**

    Users create and manage budgets to control their spending for a certain transaction category. Users get insight in the current status of their budgets.

*   **Analysis and forecast of transactions**

    Users get insight into their spending within a certain transaction category and/or within a certain period. Users get a forecast about the expected amount spent and received, and the expected amount left to spend. Turnovers and Left to Spend don’t have dependencies on the category information and can be adopted as-is whereas Top Metrics and Income and Spending Analysis by Category functionalities require transactions to have category information.

*   **Saving goals**

    Users plan and track saving goals to achieve their targets, such as buying a new phone or saving for a holiday. Users can create one saving goal and link it to their savings account. This functionality doesn’t have dependencies on the category information and can be adopted as-is.



Products
===================

A product is a financial instrument offered by a bank or other [financial institution](https://en.wikipedia.org/wiki/Financial_institution) to their customers. Examples of products are a credit or debit card, a business current account, or a 25 year fixed rate mortgage.

An arrangement or customer account in Backbase Platform is an instance of a particular product. The arrangement is typically identified with a number, for example an IBAN account number.

A bank may have an existing system to catalogue products and store arrangement details. Developers need to integrate the existing system with Backbase Platform. In case you use DBS, it includes a mock implementation that showcase a pattern that developers can use to ingest existing products and accounts. This pattern is based on pushing transactions through an integration service.

The DBS capability Products classifies products by kind. There are seven predefined product kinds. The financial institution can add more product kinds (see [Ingest product kinds](https://community.backbase.com/documentation/DBS/2-19-3/custom_product_kind)), but it is not possible to remove any of the predefined product kinds. Here follows the list of predefined product kinds:

*   **Current Account** – is a transactional, non-interest bearing account wherein a deposit is placed with the bank for an unspecified period of time and the depositor can withdraw or transfer the funds whenever required through various means. Synonyms for current account are transaction account and checking account.

*   **Savings Account** – is an interest-bearing deposit account held at a bank or another financial institution that provides a modest interest rate. A savings account is the most basic type of account at a bank or credit union, allowing to deposit money and withdraw funds as needed.

*   **Term Deposit** – is a fixed-term deposit held at a financial institution. They are generally short-term deposits with maturities ranging anywhere from a month to a few years. When a term deposit is purchased, the the money can only be withdrawn after the term has ended or by giving a predetermined number of days notice.

*   **Debit Cards** – is a payment card that is directly connected to the consumer’s checking account. During the transaction the amount of a transaction is withdrawn from the available balance in the cardholder’s account.

*   **Credit Cards** – is a payment card issued by a financial company which enables the cardholder to borrow funds which may further be used as payment for goods and services.

*   **Loan** – is a debt provided by a financial organization to another entity at an interest rate, specified by the attributes of the loan such as: principal amount of money borrowed, the interest rate the lender is charging, and date of repayment. A loan may be for a specific, one-time amount or can be available as an open-ended line of credit up to a specified limit or ceiling amount.

*   **Investment Account** – is a type of financial account that contains a deposit of funds and/or securities that is held at a financial institution. The typical objectives of an Investment Account are to achieve long term growth, income or capital preservation from the deposited asset portfolio.


The product type allows the bank to further classify their products. The widget collections currently do not consume the product type. Developers may extend a widget to display it, for example to distinguish car loans from personal loans.

Both the product kind and product type support translations. Also predefined product kinds can have translations. See [Manage product kind translations](https://community.backbase.com/documentation/DBS/2-19-3/custom_product_kind#productkind_ingesttranslations) and [Manage product type translations](https://community.backbase.com/documentation/DBS/2-19-3/product_ingest#products_ingesttranslations).

The presentation services always return one translation: either the translation that matches the requested locale set in the `Content-Language` header, or the fallback value. The translations are managed in the integration layer.

The arrangement is crucial in DBS, as it forms the connection between a legal entity (customer) and a product. For each arrangement, DBS stores the account name, the user-defined account alias, the currency, the account holder name and address, Bank Identification Code (BIC), the associated credit and debit cards, the account balance, and financial details, such as interest rate, credit limit, investment value, etc.

Arrangements support a parent-child relationship. Integration services may ingest a parent for an arrangement. The following criteria apply:
*   The parent has to be an existing arrangement.
*   There can be only one parent and many children.
*   An arrangement cannot be parent to itself.
*   A parent arrangement cannot be a child arrangement.
*   A child arrangement cannot be a parent arrangement.


Users need the correct entitlement to be able to view certain arrangements and their associate details/balances. Please refer to [Entitlements Services documentation](https://community.backbase.com/documentation/entitlements/2.19.3/entitlements_understand) for more information.

Products in DBS supports soft-deletion of arrangements. This soft-delete is intended for arrangements that have never been actively used, meaning they never had payments or transactions related to them. A use case for soft-deletion is when a financial institution wants to easily remove an account that was wrongly ingested into DBS. In order to have safe soft-deletion, the project team implementing DBS needs to create a validation that checks the following preconditions:
*   No transactions are using the arrangement
*   No payments orders are using the arrangement
*   There is no relation to other arrangements via the parent ID

Important

There is no automatic validation that checks if these rules are fulfilled. The validation must be created during the implementation of DBS. In case these validations are skipped, DBS may become unstable when an arrangement is soft-deleted that is actively used across the system.


List products
-------------

This functionality lists all financial products. For each product, some details are included, such as the account balance.

Display product details
-----------------------

Display the details of the selected product. The actual details depend on the type of product.

DBS offers a wide range of product details, amongst which are the following:

*   **Account holder**: name, address

*   **General**: account type, IBAN, BBAN, BIC, internal IDs, external IDs

    As an optional feature, Products masks the account numbers, which means that the service only includes the last four digits of the account number and masks the rest of the account number with a bullet character (`•`). Clients set this option when they make a request to Products, see [Retrieve arrangements, balances, link arrangements, and manage user preferences](https://community.backbase.com/documentation/DBS/2-19-3/retrieve_products).

*   **Balance**: available balance, booked balance, currency

*   **Interest-Related**: interest rate, accrued interest, and possibly minimum required balance

*   **Maturity**: monthly installment amount, principal amount, interest rate, and term

*   **Overdraft**: overdraft limit and expiry date

*   **Settlement Account**: details of the account for receiving interest and for settling transactions

*   **Associated Debit Cards**: card number (masked) and expiry date

*   **Account Opening Date / Last Updated Date**: date the account was opened and the last time account details were updated

*   **Product name/alias**: the product name or user-defined alias

*   **External Transfer**: whether transfers are allowed to products other than the users' own products


Manage products
---------------

Bank customers can manage their financial products:
*   Define an alternative name for a product, called an alias. If defined, the alias is the display name for the product.
*   Set the visibility of a product. Hidden products are not shown in the product list.

If the alias of a product is undefined, widgets display the product name.



Transactions
=======================

A financial transaction is an agreement between a buyer and a seller to exchange goods, services or financial instruments. Transactions in Backbase Platform provides an overview and a detailed view of transactions for one or more financial products.

Completed or billed transactions are executed and settled payment orders. This is the final state of a transaction. A pending transaction is an authorized transaction. The balance of a pending transaction is held as unavailable either until the counterparty clears the transaction, or until the hold drops off. Transactions supports both completed and pending transactions.

DBS adds a backend layer on top of the core banking system that offers 24/7 availability and responsiveness, and integrates with external enrichment data providers to enrich each new eligible transaction. For example, by automatically adding a category, merchant name, geographic location, and by cleaning up the description.

Personal Finance Management includes some features that are supported by the Transactions capability in DBS, instead of Personal Finance Management. For more information, see [Understand Personal Finance Management](https://community.backbase.com/documentation/DBS/2-19-3/pfm_understand).


List transactions
-----------------

DBS supports this functionality with the following features:

*   **List transactions with details**
    *   Retrieve the list of transactions for a given arrangements.
    *   Retrieve the most recent transactions for a given arrangement.
    *   Retrieve additional information about the transactions.

*   **Load transactions incrementally or use pagination**

*   **Retrieve the running balance of transactions**

*   **Sort transactions**
    This feature includes:
    *   Primary sorting: The bank customer specifies a field for the primary sorting and sets the sorting order to either ascending or descending.
    *   Secondary sorting: Configurable in the persistence service with the `backbase.transactions.secondary-sort` setting.


Manage transactions
-------------------

DBS persists user notes and allows to create, read, update, and delete user notes.

Enrich transactions
-------------------

Note

This section describes an add-on. You may use this add-on only if you have purchased an appropriate license for this add-on based on a signed Product Order Form.

DBS integrates with third-party data enrichment providers to enrich the transaction data, for example by automatically adding a category to each new transaction. See [Enable transaction enrichment](https://community.backbase.com/documentation/DBS/2-19-3/enable_transaction_enrichment). DBS persists transaction categories and allows to change the category for a transaction.

Export transactions
-------------------

Bank customers are able to export transactions in CSV, OFX, or PDF format. The OFX export format is only available for checking and saving accounts.

Filter transactions
-------------------

Bank customers can filter their transactions list based on a set of criteria, including:
*   Credit/debit indicator
*   Transaction type
*   Date range
*   Amount range
*   Check serial number range


Search transactions
-------------------

The bank customer can run a free text search with keywords and searchable attributes. Both full and partial matching is supported, for example:

*   If the bank customer searches "Insurance", the system scans all the searchable fields that contain the string `Insurance`.

*   If the bank customer searches "Insur", the system scans all the searchable fields that contain the string `Insur`.

*   If the bank customer searches "Car Insurance", the system scans all the searchable fields that contain the string `Car` and/or the string `Insurance`.


Print transactions
------------------

Printed transactions lists can be used for the bank customer’s own administration, or for official purposes, such as a proof of payment.

Bank customers print the list of transactions by clicking the printer icon. If there are any filters and/or search criteria applied, only the results of those are printed.

Display check images
--------------------

Transactions retrieves the front and back image of a cleared check from the internal image persistence system of the core banking system, and/or from institutions such as [Fed](https://en.wikipedia.org/wiki/Federal_Reserve), and enables the frontend to display the images. One or more images can be available for each check withdrawal transaction.

Display geolocation
-------------------

Geolocation is the identification or estimation of the real-world geographic location of an object. If latitude and longitude values are present for a transaction, Transactions can display a map in transaction details which shows the geolocation of that transaction.

Integration options for Transactions
------------------------------------

Transactions integrates with the core banking system, which is the final ledger of the banking transactions. The following methods to integrate Transactions widgets with the core banking system are available:

1.  **Inbound integration**

    The Transactions widgets interact with DBS that ties into the core banking system. The core banking system pushes changes to DBS that persists the transactions and enriches them with Digital Banking Services features, such as the ability to add notes or categories. In addition to receiving pushed changes, DBS can also actively pull changes from the core banking system. Mixing pushing and pulling methods is possible.

2.  **Outbound integration**

    The Transactions widgets interact with DBS that only checks with Access Control whether the user is entitled to access the transactions. When the permission is established, Digital Banking Services passes the request on to the core banking system, via a custom integration service. In this mode, transactions are not persisted in DBS, enrichment of data is not possible, and several features of Personal Finance Management are not supported.

3.  **Direct integration**

    The Transactions widgets interact directly with the core banking system. DBS is not required. This option requires developers to create a custom integration layer that maps the interface expected by the Transactions widgets to the interface of the core banking system.


For more information, see [Integration patterns for Transactions](https://community.backbase.com/documentation/DBS/2-19-3/transaction_integration_patterns).



Users
================

In Backbase Platform, a user is a person who interacts with the bank and who uses Backbase applications on behalf of the legal entity they are representing. The Users capability within [Access Control](https://community.backbase.com/documentation/DBS/2-19-3/entitlements_understand) provides the functionality to manage these users. If [Identity Services](https://community.backbase.com/documentation/identity/1.7.2/understand) is part of your environment, you can also integrate Users with Identity Services to enable Identity Services to authenticate the users and provide additional identity management functionality.

Users includes the following user functionality:

*   **User Management**
    User Management enables bank employees to manage bank users' details and account security. Users provides the following User Management functionality:
    *   Create a user that belongs to a specific legal entity.
    *   Search for users and retrieve a user’s details.
    *   Modify a user’s details.

    If you have integrated Users with Identity Services, User Management provides the following additional functionality:
    *   Create or update a corresponding identity in Identity Services when a user is created or updated in Users. The DBS user ID is linked to their internal ID in Identity Services.
    *   Reset a user’s password to require them to change it.
    *   Set required actions that a user must complete before they can log in.
    *   Retrieve an identity and any email address, email verification, mobile number, status, creation date, and required actions associated with the identity.
    *   Send an email to a user to tell them to perform an action.
    *   Manage user sessions. For example, list the sessions associated with a user or log the user out of their sessions.
    *   Change the user’s status. For example, lock the user.

*   **User Self Service**
    User Self Service enables bank users to manage their own accounts and update their credentials. If you have integrated Users with Identity Services, User Self Service enables a user to change their own password.

*   **User Enrollment**
    User Enrollment enables bank employees to enroll customers and their accounts from the core banking system into the Backbase Platform. Users provides the following functionality within User Enrollment:
    *   Manage the users associated with a legal entity
        Users retrieves the list of users that belong to a specific legal entity, providing each user’s full name and user ID.

        If you have integrated Users with Identity Services, Users additionally provides the user’s email address, email verification, mobile number, status, creation date, and required actions.

    *   If you have integrated Users with Identity Services, Users provides the functionality to lock a user’s account after multiple failed verification attempts, or unlock a user’s account.
        You can also set up an approval policy for lock and unlock operations through the Approvals capability. In this case, Users handles requests that require approvals through the [Approval flow in Access Control](https://community.backbase.com/documentation/DBS/2-19-3/entitlements_understand#approval_flow).

*   **Self Enrollment**
    Self Enrollment enables existing bank customers to onboard into the bank’s platform. Users provides the following functionality within Self Enrollment:
    *   If you have integrated Users with Identity Services, users can view locked out messages. To unlock their account, the user contacts the bank.

*   **User profile management**
    User profile management within the Users capability enables integration with third-party core banking and customer relationship management (CRM) systems, using the [System for Cross-domain Identity Management (SCIM)](https://tools.ietf.org/html/rfc7644) v2 standard as the user profile data transfer protocol. Users provides the following user profile management functionality:

    *   Create and manage user profiles for the users in the Users capability. Perform create, read, update, and delete (CRUD) operations on these profiles.

    *   Integrate with third-party core banking and customer relationship management (CRM) systems, using the [System for Cross-domain Identity Management (SCIM)](https://tools.ietf.org/html/rfc7644) v2 standard as the user profile data transfer protocol.



Vendor
=================

Vendor is a capability that helps other capabilities in Digital Banking Services to offer out-of-the-box connections to third-party vendors.

Payveris integration
--------------------
The Payveris integration in Vendor helps the following capabilities in Digital Banking Services:
*   **Payments**: Support for A2A payments via Payveris. A2A payments is a payment type available in the United States, see [About A2A external transfer](https://community.backbase.com/documentation/DBS/2-19-3/payments_understand#understand_a2a_payments).
*   **Bill Pay**: Support for bill payments via Payveris. Bill Pay is a payment system in the United States that allows consumers to pay a person or business online electronically or by check, see [Understand Bill Pay](https://community.backbase.com/documentation/DBS/2-19-3/billpay_understand).
