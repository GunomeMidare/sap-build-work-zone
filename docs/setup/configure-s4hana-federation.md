# Configure Your SAP S/4HANA System for Content Federation

## Goal 🎯

The goal of this file is to document how to create a Content Provider in **SAP Build Work Zone**. Create a content provider for your SAP S/4HANA system in SAP Build Work Zone provider manager and add the exposed roles to the My Content area and to the Work Zone site, so that end users can access the federated apps and groups.

## References 📝
- [SAP Help: Create a Content Provider](https://help.sap.com/docs/joule/integrating-joule-with-sap/configure-destinations?locale=en-US#create-a-content-provider)
- [Developer Tutorial: Add Federated SAP S/4HANA Roles to Your SAP Build Work Zone Site](https://developers.sap.com/tutorials/cp-launchpad-federation-consumption.html)

#### SAP Notes
- [3382241 - Content is being updated: Work Zone update content is stuck](https://me.sap.com/notes/3382241/E)
- [3389613 - Error "<Internal>: Could not create content due to an internal error. Please try again later." occur](https://me.sap.com/notes/3389613/E)

## Prerequisites 📝
- You have created a Design-Time and Runtime Destination

---

### Create a Content Provider 🛠️
1. In SAP Build Work Zone, standard edition, go to `Channel Manager`.
