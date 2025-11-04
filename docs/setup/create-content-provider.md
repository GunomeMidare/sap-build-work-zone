# Create a Content Provider

## Goal 🎯

The goal of this file is to document how to create a Content Provider in **SAP Build Work Zone**. Create a content provider for your SAP S/4HANA system in SAP Build Work Zone provider manager and add the exposed roles to the My Content area and to the Work Zone site, so that end users can access the federated apps and groups.

## References 📝
- [SAP Help: Create a Content Provider](https://help.sap.com/docs/joule/integrating-joule-with-sap/configure-destinations?locale=en-US#create-a-content-provider)
- [Developer Tutorial: Add Federated SAP S/4HANA Roles to Your SAP Build Work Zone Site](https://developers.sap.com/tutorials/cp-launchpad-federation-consumption.html)
- [Integrgation Guide: WZ: Creating a Content Provider](https://help.sap.com/docs/CIAS%20FES%202020/ecb81b5bfce440ca8e7e7c9ad58fcf3a/70e980150c174778aff9f698fde04307.html?locale=en-US)

#### SAP Notes
- [3382241 - Content is being updated: Work Zone update content is stuck](https://me.sap.com/notes/3382241/E)
- [3389613 - Error "<Internal>: Could not create content due to an internal error. Please try again later." occur](https://me.sap.com/notes/3389613/E)

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

### Map Alias 🛠️
When using V2, after creating the content provider, it is necessary to map the alias FLP_STANDALONE to the default runtime destination. 
- [Federation of Remote Content Providers](https://help.sap.com/docs/build-work-zone-standard-edition/sap-build-work-zone-standard-edition/federation-of-remote-content-providers?locale=en-US0)
- [Mapping App and Card Aliases to Destinations](https://help.sap.com/docs/build-work-zone-standard-edition/sap-build-work-zone-standard-edition/mapping-app-and-card-aliases-to-destinations?locale=en-US)
- [3345119 - Expose Launchpad Content to SAP BTP - Exposure Version 2](https://me.sap.com/notes/3345119/E)

1. Click `Map Alias` and map the alias `FLP_STANDALONE` to the default runtime destination.

<img width="2175" height="727" alt="image" src="https://github.com/user-attachments/assets/8aa67986-376d-4231-a567-7fb4d8f4a287" />

