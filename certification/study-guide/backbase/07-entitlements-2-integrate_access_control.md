Integrate Access Control into a custom capability
=================================================

You use Access Control to easily allow specific users to perform clearly defined tasks on a limited group of arrangements.

When a user identified by a JWT makes a request, Access Control checks that the user has the permissions to perform that function. For example, if the current user has the right to make domestic payments on a certain account.

[![access control integrate](https://community.backbase.com/documentation/entitlements/2-19-3/images/puml/dbs/access_control_integrate.svg?ver=2.19.3)

Permissions are evaluated with an `OR` operator; the user must be assigned at least one permission to complete the function they require. When the user has permissions to perform the specified function, the request proceeds to the requested handler method. Otherwise, HTTP 403 (Forbidden) is returned.

This section shows you how to integrate Access Control into another service.


Implement Access Control
------------------------

The full source code of this example is available [on GitHub](https://github.com/Backbase/sample-service-with-access-control).

To use `auth-security-dbs-accesscontrol` to control user permissions:

1.  **Create a new project**

    1.  Create a new project based on `service-sdk-starter-core`.

    2.  Add the following dependencies in `pom.xml`:

            <dependency>
              <groupId>com.backbase.dbs.accesscontrol</groupId>
              <artifactId>auth-security-dbs-accesscontrol</artifactId>
              <version>[specify version]</version>
            </dependency>


2.  **Integrate Access Control into your code**

    1.  **Configure DBS auth-security access control**

        To configure clients in the library to point to the services, set the following properties:

        *   `backbase.communication.services.pandp.accesscontrol.query.serviceId = access-control`

        *   `backbase.communication.services.pandp.user.query.serviceId = user-manager`

        This configures `access-control` as the persistence and presentation service for access control, and `user-manager` as the persistence and presentation service for user.

    2.  **Use the library**

        To provide a convenient method for access control using the Entitlements engine, auth-security-dbs-accesscontrol uses the Spring Security `@PreAuthorize` annotation in combination with a custom security expression.

        To use the annotation to check permissions, supply a function name, resource name and a privilege:

            @PreAuthorize("checkPermission('[your_resource_name]', '[your_function_name]', {'[privilege]'})")

        Add the @PreAuthorize annotation on your REST controller handler methods. For example:

            @PreAuthorize("checkPermission('Payments', 'Domestic Payments', {'create'})")@RequestMapping(method = {RequestMethod.POST}, value = {""})@ResponseStatus(HttpStatus.CREATED)public void createPayment(@RequestBody @Valid CreatePaymentPostRequestBody payment){//TODO Implement method}

        In this example, a check is made that the user making the request (as identified by the JWT token) has been assigned a function group with a `'create'` privilege for a `'Payments'` resource and `'Domestic Payments'` function.

        If the user is privileged to perform the specified function on a resource, the request will proceed with the request handler method. Otherwise, an HTTP `403 Forbidden` status code is returned.


3.  **Manually integrate Access Control into your code**

    To manually verify the configuration, use the service GET methods of the following endpoints:

    1.  Check if the user has the specified privileges assigned:

        Make a request:
            GET /access-control/service-api/v2/accessgroups/users/permissions?userId=<Internal User ID>&serviceAgreementId=<Service agreement ID>&functionName=<Function Name>&resourceName=<Resource Name>&privileges=<Privilege>

            Using the client included in the library:
            <presentationAccessgroupUsersClient>.getUserPermissionCheck(permissionsQueryParameters);

    2.  Check the list of all privileges:

        Retrieve all privileges for a user under single service agreement for legal entity Backbase.
        Make a request:
            GET /access-control/service-api/v2/accessgroups/users/privileges?userId=&<Internal User ID>&serviceAgreementId=<Service agreement ID>&functionName=<Function Name>&resourceName=<Resource>

    3.  Get the list of arrangements with privileges:

        Retrieve a list of arrangements for a user called “Jonathan”. Make a request:
            GET /access-control/service-api/v2/accessgroups/users/privileges/arrangements?userId=<Internal User ID>&serviceAgreementId=<Service agreement ID>&functionName=<Function Name>&resourceName=<Resource Name>&privilegeName=<Privilege>

    4.  Check the permission for an arrangement:

        Check if a user in the service agreement has some permission over a specific arrangement.
        Make a request:
            /access-control/service-api/v2/accessgroups/users/user-privileges/arrangements/<arrangement-internal\_id>?function=<Function Name>&resource=<Resource Name>&privilege=<Privilege>

    5.  Check if the user has permissions to access users or arrangements that belong to the list of legal entities.

        You should use the method `userHasNoAccessToEntitlementsResource` in the component `AccessControlValidator`. For example:

        *       Boolean hasNoAccess = accessControlValidator.userHasNoAccessToEntitlementResource(legalEntities, AccessResourceType.ACCOUNT);

        *       Boolean hasNoAccess = accessControlValidator.userHasNoAccessToEntitlementResource(legalEntities, AccessResourceType.USER);

        *       Boolean hasNoAccess = accessControlValidator.userHasNoAccessToEntitlementResource(legalEntities, AccessResourceType.USER_OR_ACCOUNT);

    6.  Check if the user has permissions to access users or arrangements that belong to service agreement.

        You should use the method `userHasNoAccessToServiceAgreement` in the component `AccessControlValidator`. For example:

        *       Boolean hasNoAccess = accessControlValidator.userHasNoAccessToServiceAgreement(serviceAgreementId, AccessResourceType.ACCOUNT);

        *       Boolean hasNoAccess = accessControlValidator.userHasNoAccessToServiceAgreement(serviceAgreementId, AccessResourceType.USER);

        *       Boolean hasNoAccess = accessControlValidator.userHasNoAccessToServiceAgreement(serviceAgreementId, AccessResourceType.USER_OR_ACCOUNT);

4.  **Create a business function**

    If you need a new business function, contact Backbase and request the new business function. In some cases, Backbase may not be able to comply with your request. In that case, use the following instructions to add the custom business function to the database. Please read the instructions and the guidelines carefully before proceeding.

    Depending on your database type, to add a new business function for your service in the Access Control database, update and run one of the following example scripts for your database:

    *   MySQL:      

            INSERT INTO `business_function`
            (
              `id`,
              `function_code`,
              `function_name`,
              `resource_code`,
              `resource_name`
            )
            VALUES
              ('1014', 'manage.shadow.limits', 'Manage Shadow Limits', 'limits', 'Limits');

            INSERT INTO `applicable_function_privilege`
            (
              `id`,
              `business_function_name`,
              `function_resource_name`,
              `privilege_name`,
              `supports_limit`,
              `business_function_id`,
              `privilege_id`
            )
            VALUES
              ('33', 'Manage Shadow Limits', 'Limits', 'view', '0', '1014', '2'),
              ('34', 'Manage Shadow Limits', 'Limits', 'create', '0', '1014', '3'),
              ('35', 'Manage Shadow Limits', 'Limits', 'edit', '0', '1014', '4'),
              ('36', 'Manage Shadow Limits', 'Limits', 'delete', '0', '1014', '5');

            INSERT INTO `assignable_permission_set_item`(
                assignable_permission_set_id,
                function_privilege_id
            )
            VALUES
                (1, '33'),
                (1, '34'),
                (1, '35'),
                (1, '36');

        a.  When you insert the `applicable_function_privilege`, the value of `privilege_id` should be the `id` from the privilege table. The values are: 2- view, 3 - create, 4 - edit, 5- delete, 6- approve.

        b.  Follow these business function guidelines when adding a business function.            
        When adding a new business function, it is crucial to follow the guidelines and naming conventions for adding a custom business function. By doing so we can prevent disorganization, clashing with business function names used in the product and the breaking of current flows:          

        c.  After updating the database, restart the access-control service.



5.  **Display custom business function name on frontend**

    If you skip this step in the procedure, there is a fallback mechanism in place, which will trigger displaying the business function name entered in the database on frontend.

    1.  Modifications on the following extensions that display Business Function list is required: `ext-bb-sa-user-privileges-ng`, `ext-bb-function-access-groups-ng`. Add the following (example using the _Manage Shadow Limits_ busines function) to the `messages.json` inside the `assets` folder:

            manage.shadow.limits (function_code): "Configure Shadow Limits"

    2.  For the `ext-sa-user-privileges-ng` extension, additional step is required:

        In `templates/template.ng.html`, find the `ui-bb-permissions-modal-ng` component and update the `businessFunction` object inside of data-labels directive with the newly added business function:

            'manage.shadow.limits': ('business.function.manage.shadow.limits' | i18n)

    3.  Business functions on the front-end are grouped based on the resource name. When a business function with a new resource name is added, by default it will be displayed in the group **Others**. You may configure the newly added business function to be displayed in one of the available groups, by referencing the resource name to the selected group by either of the following methods:

        1.  Update the `constants.js` file to include the new resource in a group (or creating a new group)

        2.  If you created a new group, update the `messages.json` file to add internationalization for the new group
