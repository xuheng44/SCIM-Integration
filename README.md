# Configure ServiceNow for Genesys Cloud SCIM (Identity Management)

## Object
Blueprint for SCIM integration between Genesys Cloud and ServiceNow

## Background Knwoledge

### Genesys Cloud SCIM Support
Genesys Cloud SCIM (Identity Management) is an implementation of the System for Cross-domain Identity Management (SCIM) version 2.0 specification (RFC-7642, RFC-7643, and RFC-7644). SCIM is an open standard collection of APIs for managing the identities of users.

Genesys Cloud SCIM uses the SCIM APIs to simplify and automate the management of user entities between your identity management system and Genesys Cloud. With SCIM, changes you make to user entities in your system can be easily pushed to a single Genesys Cloud organization or multiple Genesys Cloud organizations.

**Important: Genesys Cloud currently only supports unidirectional syncing from identity management systems to Genesys Cloud.  Any changes made in Genesys Cloud will not be synced to the identity management systems and may be overwritten.**

### ServiceNow SCIM Support
ServiceNow Support below two SCIM use cases:
* SCIM Provider: The SCIM Provider synchronizes the changes made to identities in the IdP, including creating, updating , or deleting records.These changes are automatically synchronized to the provider according to the SCIM protocol.Also, the IdP can read identities from the provider to add to the IdP directory. The IdP can then detect incorrect values in the provider that could create security vulnerabilities. The synchronization enables end users to have seamless access to applications for which they’re assigned, with up-to-date profiles and permissions.
* SCIM Client: The SCIM Client is used for creating, updating and deleting identifity resrouces in a system that supports SCIM compliant REST requests.The client is used for identity life-cycle management and for identity attribute synchronization across ServiceNow instances or between ServiceNow and other SCIM providers.​

### Intergration methods
* Method 1: Using 3rd party IDP
  Both Genesys Cloud and ServiceNow could support 3rd party IDP integration, Like Microsoft Entra ID, OTKA or OneLogin, please checkout below link for detail information:

  https://help.mypurecloud.com/articles/about-genesys-cloud-scim-identity-management/
  
  https://docs.servicenow.com/bundle/washingtondc-platform-security/page/integrate/authentication/concept/explore-scim-provider.html
  
  We will not cover this method in this blueprint.
  
* Method 2: Using ServiceNow SCIM Client
  In the blueprint, We will use ServiceNow SCIM client to privision Users/Groups in the Genesys Cloud.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/a853c8d8-a34a-4de9-a880-acf343acece7">

### Limitations

* For Users: Support Create and Updating, but not support Delete(Due to ServiceNow workflow limitations)
* For Group: Support add/remove members, but not support create/delete group (Due to Genesys Cloud SCIM limitations)

## SCIM Client Integration configuration Step

### Create OAuth Client for ServiceNow SCIM Integration
Login to your Genesys CLoud Org with admin account, and create an Auth Client(https://help.mypurecloud.com/articles/create-an-oauth-client/), make sure assign the SCIM Integration Role. And Copy Client ID and Client Secret for ServiceNow SCIM Client Integration.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/fa086044-6d02-4bce-a6ab-3d4818445009">

### Login ServcieNow and Create a developer instance

Login to ServiceNow(https://developer.servicenow.com/dev.do#!/home), Click "Request Instance" on the top-Right Page, choose the lastest Release: Washtington DC.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/48066f8d-e774-4434-b4a5-1b118c14a844">
After ServiceNow create an instance, then can click the button "Start Building" to login instance with admin role.
![image](https://github.com/xuheng44/SCIM-Integration/assets/89450349/6cb7f7c1-c2dd-49e2-a2d9-5c2e0e494e22)

### Install SCIM V2  - ServiceNow Cross-domain Identity Management Client

Navigate to All > System Applications > All Available Applications > All.  Find the SCIM v2 - ServiceNow Cross-domain Identity Management Client (com.snc.integration.scim2.client)  plugin using the filter criteria and search bar. And install that Plugin.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/3d57929e-3353-4066-b230-2d6a5811be50">
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/8a3387dc-adb6-4e8e-ba92-c0b7045adf78">

### Install oAUTH
Search "oAUTH" in the Search Bar, Click System Oauth→ Application Registry
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/530ed6ea-eec3-42cf-93bb-00897f8b0456">
Click "New" to create a third party oAuth Provider:
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/4146e297-5837-49d5-9c2d-24cdc5218549">
Client ID and Client Secret: Copied from Step1: Create OAuth Client for ServiceNow SCIM Integration
Default Grant Type: Client Credentials
Authorization URL:  https://login.apne2.pure.cloud  (Please fill it with your org info)
TOken URL: https://login.apne2.pure.cloud/oauth/token (Please fill it with your org info)
You can visit https://developer.genesys.cloud/platform/api/ for the detail org info.

### Configure REST Message for SCIM Client
Go to System Web Services → Outbound → REST Message, Create new Rest Message record based on below link:
https://docs.servicenow.com/bundle/washingtondc-platform-security/page/integrate/authentication/task/create-a-rest-message.html
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/181f16e6-2d79-427b-abb4-462634acde29">
Endpoint: https://api.apne2.pure.coud/api/v2/scim (Please fill it with your org info)
You can visit https://developer.genesys.cloud/platform/api/ for the detail org info.
Authentication Type: OAuth 2.0
OAuth profile: choose the profile configured in the previou step.
After configuration and save, you can click "Get oAuth Token" to make sure the oAuth configuration is correct.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/4a8ad925-19b6-4b74-8af4-011ea2411baf">
Then goto panel "HTTP Request" to configure HTTP Headers: Content-Type:  application/json
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/4b0d7d98-3de5-41a9-ab5e-ba5eec4eed42">
Then Configure GET/PATCH/PUT/POST/DELETE HTTP methods for this REST Message.
<img width="487" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/479f19cb-235a-479b-9a7d-1be0a7e1520a">
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/d226dd21-341c-469f-ac1c-7ee1ac83d9ad">

**Please replace the API Endpoint URL with your region domain. Also need to make sure there is no “/” between scim and ${resourceName} in the endpoint except for get Request(And you must remove "/" after configure SCIM Provder)**

### Create SCIM Provder
Goto SCIM Client → SCIM Provider to create a new provider to connect Genesys Cloud SCIM.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/a5df4327-79c5-4405-b8aa-82c33775d7b4">
After create SCIM Provider, please goto REST Message step, remove the "/" in the GET http method.

### Create SCIM Provider Resource Mapping
Goto SCIM Client -> SCIM Provider Resource Mapping, Create a new Mapping, Choose "User" for Resource name and "User[sys_user] for Primary Table.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/2b7b73fa-b44e-4cc0-aa73-b6ff3d096198">
Then configure SCIM Attributes Mappings for User, need to configure one attribute as unique: use userName as unique mapping as below:
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/554157d3-acc2-4473-bf26-fbe49d73fb4f">
Please refer to link(https://help.mypurecloud.com/articles/scim-and-genesys-cloud-field-mappings/), configure more attributes like below: 
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/09c7f85f-3f99-49ef-ab05-3fb6fcb75184">
Then create new Mapping for Group:
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/ee08e1bb-98ed-4733-9d0a-4cc922a5bf89">
Add Attribute displayName as unique attribute:
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/1876dadf-2b9f-494c-832b-5e4e0e07e0b1">
And Another attribute memeber as follow
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/860df422-a07b-46d8-ab2d-36de189d46ef">
When configure Members of Group, you need use below script for mapping, and update the SCIM provider in the previous step.
```
(function getValue(resourceGR) {
    try {
        //user
        var grMem = new GlideRecord('sys_user_grmember');
        var response = [];
        grMem.addQuery('group', resourceGR.sys_id);
        grMem.query();
        while (grMem.next()) {
            user = {};
            var userId = grMem.user;
            if (userId) {
                var externalUserId = sn_auth.SCIM2ClientUtil.getProviderIdByResourceId('Genesys Cloud SCIM','User', userId);
                gs.info("For userId :" + userId + ", external userId in provider's system is:" + externalUserId);
                if (externalUserId) {
                    user.value = "" + externalUserId;
                    response.push(user);
                }
            }
        }
        //group
        var grp = new GlideRecord('sys_user_group');
        grp.addQuery('sys_id', resourceGR.sys_id);
		grp.addQuery()
        //grp.addQuery('active', true);
        grp.query();
		gs.info("group search start!" + resourceGR.sys_id );
        while (grp.next()) {
			gs.info("Loop")
            group = {};
            var groupId = grp.sys_id;
			gs.info("Group name " + groupId + " !" );
            if (groupId) {
                var externalGroupId = sn_auth.SCIM2ClientUtil.getProviderIdByResourceId('Genesys Cloud SCIM','Group', groupId);
                gs.info("For groupId :" + groupId + ", external groupId in provider's system is:" + externalGroupId);
                if (externalGroupId) {
                    group.value = "" + externalGroupId;
                    response.push(group);
                }
            }
        }
        return JSON.stringify(response);
    } catch (e) {
        gs.error('Unable to get attribute value using script' + e);
        return null;
    }
})(resourceGR);
```
### Setup workflow in ServiceNow
Flow Designer is a Now Platform® feature that enables process owners to automate work. Build multi-step flows from reusable components without having to code. Please refer to link(https://docs.servicenow.com/bundle/washingtondc-build-workflows/page/administer/flow-designer/concept/flow-designer.html) for it's detail features and functions. 
Here we only use Trigger + Action to support below 2 use cases:
#### SYNC user from Service Now to Genesys Cloud
Click All -> Process Automation -> Flow Designer, ServiceNow will open a new tab for flow desiner.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/957def3d-87a1-444b-8c87-8fcabd0c0d27">
Clicke New -> Action in the workflow Studio.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/2399015e-356b-4b51-8805-355371299e9f">
Create Input Variable: userid
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/89a843b8-ecc2-45a0-a354-6de4966765fb">
Add a scrip step
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/1250040b-4002-4dc7-bde4-c149184aa9ac">
Input below code to script, and add Input Varialbes as below screen
```
var scimClient = new sn_auth.SCIM2Client(); 
var response = scimClient.provision('Genesys Cloud SCIM','User',inputs.UserID); 
gs.info('response: ' + response);
```
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/da5c9633-f0dc-4af1-90d0-148dbd6bd856">
After configuration, please save and publish.
Then click New -> Flow.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/8dd5a91d-10bf-48f5-a3a6-a6a612956299">
Add a trigger, Trigger: Select Record -> Created or Updated, Table: Select User[sys_user], Run Trigger: Select For Every update. Add Filter: Select Department.Name Contains 'Genesys". (this filter can be changed by user case, here only sync users with Department name contains "Genesys").
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/8a2ba607-e62d-4be6-abf4-9ee4c2ddb488">
Add Action, Select the Action "Genesys Cloud SCIM User", which just created in the previous step, and select userid as below screen.
<img width="1177" alt="image" src="https://github.com/xuheng44/SCIM-Integration/assets/89450349/28216337-bd55-48b9-8455-5455960cd146">
Then save and Active this flow.
Then you can create new user(with Department name contains "Genesys) in ServiceNow, you will find the same user will be created or updated in the Genesys Cloud at the same time.

**NOT Supported Now**

* Not support Delete User
* Not support deselect "Active" in user
 
#### SYNC Group and Group Members from Service Now to Genesys Cloud












