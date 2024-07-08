# Configure ServiceNow for Genesys Cloud SCIM (Identity Management)

## Object
Blueprint for SCIM integration between Genesys Cloud and ServiceNow

## Introduction

## Genesys Cloud SCIM Support
Genesys Cloud SCIM (Identity Management) is an implementation of the System for Cross-domain Identity Management (SCIM) version 2.0 specification (RFC-7642, RFC-7643, and RFC-7644). SCIM is an open standard collection of APIs for managing the identities of users.
Genesys Cloud SCIM uses the SCIM APIs to simplify and automate the management of user entities between your identity management system and Genesys Cloud. With SCIM, changes you make to user entities in your system can be easily pushed to a single Genesys Cloud organization or multiple Genesys Cloud organizations.
** Important: Genesys Cloud currently only supports unidirectional syncing from identity management systems to Genesys Cloud.  Any changes made in Genesys Cloud will not be synced to the identity management systems and may be overwritten. **
