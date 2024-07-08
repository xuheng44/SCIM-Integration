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
![image](https://github.com/xuheng44/SCIM-Integration/assets/89450349/48066f8d-e774-4434-b4a5-1b118c14a844)



