# Create the Run-Time Destination for SAP Build Work Zone

## Goal 🎯

The goal of this file is to document how to create the run-time destination. The runtime destination is used to launch federated applications at runtime.

## References 📝
- [Create Runtime destination](https://help.sap.com/docs/SUPPORT_CONTENT/fioritech/5173040124.html?locale=en-US)

## Prerequisites 📝
- XXXX

---

### Create Run-Time Destination 🛠️


1. Enter the following `Additional Properties`:
   
| Property Name                 | Value     | Remarks                                                                                                                                                                                                         |
|-------------------------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `HTML5.DynamicDestination`    | `true`    |                                                                                                                                                                                                                 |
| `sap-platform`                | `ABAP`    |                                                                                                                                                                                                                 |
| `sap-service`                 | `3200`    | A concatenated string that contains 4 characters: the first 2 characters are “32”; the last 2 characters are the instance number of the ABAP application server or the SAP system number. For example: 3200     |
| `sap-sysid`                   | `S4H`     |                                                                                                                                                                                                                 |
