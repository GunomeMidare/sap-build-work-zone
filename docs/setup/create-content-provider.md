# Create a Content Provider

## Goal 🎯

The goal of this file is to document how to create a Content Provider in **SAP Build Work Zone**. Create a content provider for your SAP S/4HANA system in SAP Build Work Zone provider manager and add the exposed roles to the My Content area and to the Work Zone site, so that end users can access the federated apps and groups.

## References 📝
- [SAP Help: Create a Content Provider](https://help.sap.com/docs/joule/integrating-joule-with-sap/configure-destinations?locale=en-US#create-a-content-provider)
- [Developer Tutorial: Add Federated SAP S/4HANA Roles to Your SAP Build Work Zone Site](https://developers.sap.com/tutorials/cp-launchpad-federation-consumption.html)

#### SAP Notes
- [3382241 - Content is being updated: Work Zone update content is stuck](https://me.sap.com/notes/3382241/E)

## Prerequisites 📝
- You have created a Design-Time and Runtime Destination

---

### Create a Content Provider 🛠️
1. In SAP Build Work Zone, standard edition, go to `Channel Manager`.
2. Select `New -> Content Provider`.

   <img width="610" height="800" alt="image" src="https://github.com/user-attachments/assets/433d01aa-2f68-480a-9d41-e669b8dbb136" />

3. Enter the following information:

| Field                                                                      | Value    | Remarks                                                                                                                                                |
|----------------------------------------------------------------------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Title**                                                                  |          | Enter a meaningful name |
| **Description**                                                            |          | Enter a description |
| **ID**                                                                     |          | `<A unique ID>` |
| **Design-Time Destination**                                                |          | Select the design time destination |
| **Runtime Destination**                                                    |          | Select the runtime destination |
| **Runtime Destination for Dynamic Data**                                   |          | The runtime destination for retrieving dynamic data to display on dynamic tiles. By default, the default runtime destination is used. |
| **Automatically add all content items to subaccount**                      |          | If you select Automatic addition of all content items, all exposed content items will be automatically selected in the Content Explorer and added to the My Content tab, as soon as you create the content provider. In productive accounts, you would usually use automatic addition to reduce manual effort and allow for automatic content sync. |  
| **Use the Identity Provisioning service to provision user authorizations** |          | Using Identity Provisioning service is recommended for productive scenarios as it allows to reuse user-role assignments from the content provider and reduces manual effort. |
| **Include group and catalog assignments to roles**                         |          | Use this toggle switch depending on how the provider is modeled<br><br>**Disable this feature** to include all groups and catalogs in this site, without considering their assignment to roles.<br><br>**Enable this feature** to include only groups and catalogs in this site, that have been directly assigned to roles. |
   
4. Click `Save`.
