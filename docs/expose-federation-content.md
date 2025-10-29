# Expose Federation Content from SAP S/4HANA

## Goal 🎯

The goal of this file is to document how to expose Federation Content from SAP S/4HANA. Select and expose roles in SAP S/4HANA to make them available with their assigned apps, groups, catalogs, and spaces in SAP Build Work Zone.

## References 📝
#### SAP Notes
- [3668043 - Unable to schedule job for exposing S/4HANA content to BTP Work Zone using program /UI2/CDM3_EXP_SCOPE](https://me.sap.com/notes/3668043/E)
- [3345119 - Expose Launchpad Content to SAP BTP - Exposure Version 2](https://me.sap.com/notes/3345119)

#### Blogs, SAP Help and other Sources
- [SAP Help: Exposing Launchpad Content to SAP BTP](https://help.sap.com/docs/ABAP_PLATFORM_NEW/a7b390faab1140c087b8926571e942b7/8216497368a9417f8008db8eb63fab72.html?version=202310.003&locale=en-US)
- [Developer Tutorial: Expose Federation Content from SAP S/4HANA](https://developers.sap.com/tutorials/cp-launchpad-federation-expose-content.html)

## Prerequisites 📝
- You also need to make sure that the user which does the content exposure has the right role. 
  -  The permissions to run the content exposure are delivered with the role SAP_FLP_ADMIN
- Verify that the page cache is turned on for the user. 


---

### Select SAP Fiori Content for Exposure using transaction 
1. Launch transaction `/n/ui2/cdm3_exp_scope`.
2. Choose the exposure version you would like to use.

> [!Note]
> Each exposure version uses a dedicated repository, allowing you to run, schedule, and use both version 1 and version 2 simultaneously.

3. Choose which roles you want to provide to SAP BTP.
4. Save your defined roles by clicking on `Save Selected Roles`. The role selection must be saved first before exposing. Otherwise you will expose the last saved role selection.

### Expose Selected Roles
