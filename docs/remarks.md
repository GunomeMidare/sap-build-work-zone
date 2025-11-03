## SAP Build Work Zone Remarks 📝

### General
- []()

### SAP Cloud Identity Services 📰
- Starting from 20th March, 2025, new subscriptions will be using the Identity Authentication service as the default authentication mechanism. A prerequisite for using Identity Authentication, is to create a trust using OIDC protocol between the subaccount on SAP BTP and Identity Authentication.
  - [SAP Build Work Zone: Initial Setup](https://help.sap.com/docs/build-work-zone-standard-edition/sap-build-work-zone-standard-edition/initial-setup?locale=en-US)
 

### Exposure Version 1 & 2 📰
- Modifying the exposure version in an existing content provider is not supported. For instance, switching a destination used for CDM version 1 to one used for CDM version 2 is not supported. If not already available, you need to create a new content provider specifically for CDM exposure version 2.
- It is not recommended to mix content from exposure version 1 and version 2 in a single SAP Build Work Zone site. Some apps may launch in-place while others may open in a new S/4HANA shell and create confusion for users.
