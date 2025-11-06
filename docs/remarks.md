## SAP Build Work Zone Remarks 📝

### General
- []()

### SAP Cloud Identity Services 📰
- Starting from 20th March, 2025, new subscriptions will be using the Identity Authentication service as the default authentication mechanism. A prerequisite for using Identity Authentication, is to create a trust using OIDC protocol between the subaccount on SAP BTP and Identity Authentication.
  - [SAP Build Work Zone: Initial Setup](https://help.sap.com/docs/build-work-zone-standard-edition/sap-build-work-zone-standard-edition/initial-setup?locale=en-US)
- For SAP Build Work Zone, IAS must at least be used in proxy mode when connecting to another SAP or third party. IdP isn’t supported. Directly connecting a corporate identity provider to the SAP BTP subaccount doesn’t work for SAP Build Work Zone or SAP SuccessFactors Work Zone. The manual trust configuration on the BTP subaccount with IAS (based on SAML2) is supported. However, the recommended trust setup with IAS is using the automated, establish trust feature (OpenID Connect).
  - [Explaining the Authentication Flow of SAP Build Work Zone](https://learning.sap.com/learning-journeys/implement-and-administer-sap-build-work-zone/explaining-the-authentication-flow-of-sap-build-work-zone)



### Exposure Version 1 & 2 📰
- Modifying the exposure version in an existing content provider is not supported. For instance, switching a destination used for CDM version 1 to one used for CDM version 2 is not supported. If not already available, you need to create a new content provider specifically for CDM exposure version 2.
- It is not recommended to mix content from exposure version 1 and version 2 in a single SAP Build Work Zone site. Some apps may launch in-place while others may open in a new S/4HANA shell and create confusion for users.
